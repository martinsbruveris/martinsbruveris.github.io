---
layout: post
title: "TensorFlow Model Surgery"
date: 2021-07-03 12:00:00
author: Martins Bruveris
tags: deep-learning
---

There are things that are very easy to achieve in TensorFlow 2 and there are things that are annoyingly hard and often the dividing line is surprisingly narrow. For example, it is easy to combine two models into a bigger model, but splitting a model into two parts is difficult. Here we explore some ways of achieving the latter.

<!--more-->

### Code

Full code can be found in this <a href="https://gist.github.com/martinsbruveris/1ce43d4fe36f40e29e1f69fd036f1626">notebook</a>.

### Problem statement

What do we want to achieve? We start with a simple Keras model.

{% highlight python %}
def build_model():
    model = Sequential()
    model.add(Input((2,)))
    model.add(Dense(4, name="fc1"))
    model.add(Dense(8, name="fc2"))
    model.add(Dense(16, name="fc3"))
    model.add(Dense(2, name="fc4"))
    return model
model = build_model()
{% endhighlight %}

We would like to split the model into two parts, such that the first part contains the first two layers and the second part the second two layers.

{% highlight python %}
model_a = ...  # Should hold fc1 and fc2
model_b = ...  # Should hold fc2 and fc3

# Concatenated they should equal the model we started with
x = tf.constant([[1., 2.]])
y1 = model(x)
y2 = model_b(model_a(x))

assert y1 == y2
{% endhighlight %}

Why do want to do this? We may want to apply some operation to the outputs of a layer before it is passed to the subsequent layer. For example, the "Restricting the Flow" <a href="https://arxiv.org/abs/2001.00396">paper</a>, which uses the information bottleneck method as a basis for attribution, needs to add noise at an intermediate layer. How do we add noise if we can't cut the model apart?

If we know in advance where we will want to split the model, there are options available to us:
<ul>
 	<li>We can include a special layer in the model allowing us to do the required operations</li>
 	<li>We can start with two separate models and combine them into a joint model using the Keras' functional API.</li>
</ul>
But what do we do if we have an already built and pre-trained model? In that case I could find two options, neither of which is perfect or easy to find.

### Keras functional API

It is possible to create new models with the functional API using intermediate layer outputs on one model as inputs or outputs of a new model. We can do this as follows:

{% highlight python %}
def cut_model(model, cut_layer_name):
    # Input(...) below changes the model, hence we copy first
    clone = clone_model(model)
    # Cloning only affects topology, not weights
    clone.set_weights(model.get_weights())

    cut_layer = clone.get_layer(name=cut_layer_name)

    model_a = Model(clone.inputs, cut_layer.output)
    model_b = Model(
        Input(tensor=cut_layer.output), clone.outputs
    )
    return model_a, model_b

model = build_model()
model_a, model_b = cut_model(model, &quot;fc2&quot;)
{% endhighlight %}

The `clone_model` statement is important, because when constructing `model_b`, the <code>Input(tensor=cut_layer.output)</code> actually changes the <code>cut_layer.output</code> tensor to a placeholder (yes, they still exist in TF2), which can will lead to problems, if TF needs to trace the graph of the model in the future.

While cloning solves this particular problem, it also means that this method can only be applied to Keras models that are <em>clone-able</em>. If a model uses custom layers, the layers need to correctly implement <code>to_config</code> and <code>from_config</code> methods and cloning subclassed models might potentially not work. But if a model is clone-able, then this is a reasonably clean solution.

### Working with graphs
It is still possible to work with graphs in TF2 and to perform surgery using graphs. On the plus side this method will work even if cloning the model is not possible. However, this method will split the Keras model into two callables, that are not themselves Keras models, just graphs.

We can perform the splitting as follows:

{% highlight python %}
def cut_model(model, cut_layer_name):
    # Convert Keras model to tf.Graph
    model_function = tf.function(lambda x: model(x))
    model_function = model_function.get_concrete_function(
        tf.TensorSpec(model.inputs[0].shape, model.inputs[0].dtype)
    )
    model_function = convert_variables_to_constants_v2(model_function)
    graph = model_function.graph

    # Find tensor name in graph corresponding to cut point
    outputs = get_graph_outputs(graph, model, cut_layer_name)

    # Construct new functions from copies of the graph
    @tf.function
    def model_a(inputs):
        inputs = tf.convert_to_tensor(inputs)
        y = tf.import_graph_def(
            graph.as_graph_def(),
            input_map={graph.inputs[0].name: inputs},
            return_elements=outputs,
        )
        return y[0]

    @tf.function
    def model_b(inputs):
        inputs = tf.convert_to_tensor(inputs)
        y = tf.import_graph_def(
            graph.as_graph_def(),
            input_map={outputs[0]: inputs},
            return_elements=[op.name for op in graph.outputs],
        )
        return y[0]

    return model_a, model_b
{% endhighlight %}

In this function we first construct a callable <code>model_function</code> using <code>tf.function</code> and trace its graph with <code>get_concrete_function</code>. We need to convert variables to constants, because Keras stores weights using the resource <code>dtype</code> in the graph and (presumably) fetches them via <code>feed_dict</code>. We will return to the <code>get_graph_outputs</code> function. For now it is enough to know that it returns the name of the tensor corresponding to the cut point in the graph. Finally, we construct two new callables with the help of <code>import_graph_def</code> which copies a graph (or parts thereof) with our specified inputs and outputs.

The remaining piece of the puzzle is the <code>get_graph_outputs</code> function. We need it, because there are discrepancies how Keras names its layers and how the corresponding operations and tensors are named in the graph. For example, the last operation of the <code>fc2</code> layer in the Keras model could be called <code>fc2/BiasAdd_6</code>, while the corresponding operation in the graph is called <code>sequential_4/fc2/BiasAdd</code>. The following is a fairly ad-hoc way of navigating the divide.

{% highlight python %}
def get_graph_outputs(graph, model, layer_name):
    output = model.get_layer(layer_name).output.name
    # Remove the &quot;:0&quot; part
    output = output.split(&quot;:&quot;)[0]

    # The assumption is that the layer name has an optional &quot;_X&quot;
    # compared to the graph op
    op_found = False
    for op in graph.get_operations():
        prefix = model.name + &quot;/&quot;
        if not op.name.startswith(prefix):
            continue

        # Remove the prefix, e.g., &quot;sequential_1/&quot;
        op_without_prefix = op.name[len(prefix):]
        if output.startswith(op_without_prefix):
            op_found = True
            break

    if not op_found:
        raise ValueError(&quot;Op not found in graph.&quot;)

    # Now we have the op and its outputs
    outputs = [out.name for out in op.outputs]
    return outputs
{% endhighlight %}

I don't know how robust this code is. Probably not very, but it works at least for sequential models using standard layers. With this piece in place we can now do the following

{% highlight python %}
x = tf.constant([[1., 2.]])
y1 = model(x)
y2 = model_b(model_a(x))
print(y1.numpy(), y2.numpy())
{% endhighlight %}

and we should see <code>y1</code> and <code>y2</code> holding the same values.
