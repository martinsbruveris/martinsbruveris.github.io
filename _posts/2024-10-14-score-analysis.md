---
layout: post
title: Introducing score-analysis
date: 2024-10-14 06:00:00
author: Martins Bruveris
tags: statistics machine-learning
---

<!-- 
Why score-analysis?
- [ ] Fast
- [x] Flexible (score ordering)
- [ ] Handles edge cases
- [x] Vectorised
- [x] Handles arbitrary shapes
- [x] Bootstrapping CIs
- [ ] CIs for ROC curves
- [ ] Virtual samples
- [ ] Group scores 
-->

The `score-analysis` package has been designed to analyze ML model results, particularly
for binary decision problems. It contains efficient implementations for threshold
setting, bootstrapping confidence intervals and plotting the ROC curve.

<!--more-->

<!-- ## Usage

### Terminology

Sometimes, we like to work with metrics based on acceptance and rejection, such as
FAR (false acceptance rate) and FRR (false rejection rate), while the standard ML
terminology talks about positive and negative classes and FPR (false positive rate) and
FNR (false negative rate).

This library adopts the standard ML terminology. The translation is simple: just replace
"accept" with "positive" and replace "reject" with "negative" and you have a dictionary
between the two worlds.
-->

## Usage

### Constructing scores

We assume that we work with a binary classification problem in which our classifier has
predicted a score for each element in our dataset. To analyze these scores, we first
create a `Scores` object with the predicted scores and labels.

```python
from score_analysis import Scores

scores = Scores.from_labels(
    labels=[1, 1, 1, 0, 0],
    scores=[1, 2, 3, 0.5, 1.5], 
    # We specify the label of the positive class. All 
    # other labels are assigned to the negative class.
    pos_label=1,  
)
```

Internally, the `Scores` class saves the scores for the positive and negative classes as
two separate, sorted arrays. 

```python
>>> scores.pos
array([1., 2., 3.])
>>> scores.neg
array([0.5, 1.5])
```

This pre-sorting will enable fast metric computations and
thresholding operations. If we already have separated positive and negative scores, we
can construct the `Scores` object directly.

```python
scores = Scores(pos=[1, 2, 3], neg=[0.5, 1.5])
```

The `Scores` objects takes two parameters, `score_class` and `equal_class`, that define
metric computations:

- `score_class` determines whether scores indicate membership of the positive or the
  negative class. Is a higher score indicative of the sample being more positive or more
  negative?
- `equal_class` determines, how we should classify a sample, if the score is exactly
  equal to the decision threshold.

We include the `score_class` parameter in the library to decouple computing scores from
interpreting scores. When we compute scores, it is the model that determines the meaning
of scores and it is difficult to change the meaning. E.g., let's consider the face
matching problem. If we use cosine similarity to compare face
embeddings, a high score indicates a likely match: scores point towards the positive
class; but if we work with euclidean distances, then a high score indicates a non-match:
scores point towards the negative class. The `score_class` parameter allows us to keep
both the semantic meaning of positive/negative class as well as the code for experiment
analysis unchanged.

The `equal_class` parameter is perhaps a bit more exotic. In most ML applications scores
are continuous objects and scores will almost never be exactly equal to the decision
threshold. But, when writing a general purpose library we have to make a choice and
there is no canonical right decision. And there might be some applications, when scores
are sufficiently discretized that the choice does matter. Maybe the exact value of
`equal_class` doesn't matter, but it matters that we are consistently using the same
choice throughout the analysis.

The meaning of the parameters is summarized in the table

| `score_class` | `equal_class` | Positive class decision logic     |
|:-------------:|:-------------:|:---------------------------------:|
|      pos      |      pos      |      score $$\geq$$ threshold     |
|      pos      |      neg      |       score $$>$$ threshold       |
|      neg      |      pos      |      score $$\leq$$ threshold     |
|      neg      |      neg      |       score $$<$$ threshold       |

### Thresholding

We can apply a threshold to a `Scores` object to obtain a confusion matrix and then
compute metrics associated to the confusion matrix.

```python
>>> cm = scores.cm(threshold=1.2)
>>> cm.fpr()
0.5
```

For ease of use, most common metrics are implemented in the `Scores` class directly. In
the above example, we can also write

```python
>>> scores.fpr(threshold=1.2)
0.5
```

We can work with multiple thresholds at once, which leads to vectorized confusion
matrices. The `threshold` parameter accepts arbitrary-shaped arrays and all confusion
matrix operations preserve the shapes.

```python
>>> scores.fpr(threshold=[0.4, 1.2, 2.0])
array([1. , 0.5, 0. ])
```

We can also determine thresholds at specific operating points. These operations are also
fully vectorized.

```python
>>> scores.threshold_at_fnr(fnr=0.3)  # Single threshold
1.9
>>> scores.threshold_at_fnr(fnr=[0.3, 0.6, 0.9])  # Vectorized thresholding
array([1.9, 2.8, 3. ])
```

Determining thresholds a fixed operating points requires interpolation, since
with a finite dataset we can measure only finitely many different values for FPR, etc.
This library uses linear interpolation for threshold setting, although this can be
changed using the `method` parameter.

### ROC curve

We can plot an ROC curve using the `roc` function.

```python
from score_analysis import roc

scores = Scores(
  pos=np.random.normal(1.0, 0.8, 1_000)
  neg=np.random.normal(0.0, 0.8, 1_000)
)

roc_curve = roc(scores, nb_points=200)

plt.plot(roc_curve.fpr, roc_curve.tpr)
```

[... From here on it is work in progress ...]


### Benefits

Why does the world need another library for analyzing ML model results? 

### Vectorised operations

All operations are, as far as possible, vectorized. For example, we can plot the ROC
curve in the following way.

```python
import matplotlib.pyplot as plt

fpr = np.linspace(0., 1., n=100, endpoint=True)  # ROC point on x-axis
threshold = scores.threshold_at_fpr(fpr=fpr)  # Corresponding thresholds
tpr = scores.tpr(threshold)  # ROC points on y-axis

plt.plot(fpr, tpr)
```

We can also compute and plot confidence intervals around all TPR values, by
bootstrapping confidence intervals on a vectorized metric.

```python
fpr_ci = scores.bootstrap_ci(metric="fpr", threshold=threshold)

plt.fill_between(fpr, fpr_ci[..., 0], fpr_ci[..., 1], alpha=0.3)
```

Here, `bootstrap_ci` will pass the `threshold` parameter when evaluating the metric. If
`threshold` is a vector of shape `(n,)`, then `fpr_ci` will be an array of shape `(n,
2)`.

Vectorization works with arrays of arbitrary shape. E.g., we can compute the thresholds
and FNR corresponding to the endpoints of the FPR CI, simply via

```python
threshold_at_ci = scores.threshold_at_fpr(fpr_ci)
fnr_at_ci = scores.fnr(threshold_at_ci)
```

The implementation of vectorized operations relies as far as possible on numpy
vectorization and thus should be significantly faster than the for loop version.

### Confusion matrices

Most metrics that we use are defined via confusion matrices. We can create a confusion
matrix either from vectors with labels and predictions or directly from a matrix.

```python
>>> labels      = [2, 0, 2, 2, 0, 1, 1, 2, 2, 0, 1, 2]
>>> predictions = [0, 0, 2, 1, 0, 2, 1, 0, 2, 0, 2, 2]
>>> cm = ConfusionMatrix(labels=labels, predictions=predictions)
>>> cm.classes
[0, 1, 2]
>>> cm.matrix
array([[3, 0, 0],
       [0, 1, 2],
       [2, 1, 3]])
```

A binary confusion matrix is a special case of a `ConfusionMatrix`, with specially
designated positive and negative classes. The convention is that the classes are
ordered `classes = [pos, neg]`. It can be created with the parameter `binary=True`.

For binary confusion matrices all metrics such as TPR are scalar. Since we have defined
which is the positive class, there is no need to use the one-vs-all strategy.

A binary confusion matrix is different from a regular confusion matrix with two classes,
since the latter does not have designated positive and negative classes.

```python
>>> cm = ConfusionMatrix(matrix=[[1, 4], [2, 3]], binary=True)
>>> cm.tpr()
0.2
>>> cm = ConfusionMatrix(matrix=[[1, 4], [2, 3]])
>>> cm.tpr()  # True positive rate for each class
array([0.2, 0.6])
```

#### Vectorized operations for confusion matrices

Care has been taken to ensure consistent handling of matrix shapes for vectorized
confusion matrices.

- A vectorized confusion matrix has shape `(X, N, N)`, `N` is the number of classes,
  and where `X` can be an arbitrary shape, including the empty shape, `X=()`.
- Calculating a metric results in an array of shape `(X, Y)`, where `Y` is the shape of
  the metric. For binary confusion matrices most metrics, such as accuracy, are scalar,
  i.e., `Y=()`, while for `N` classes the one-vs-all strategy is used, so the metric has
  shape `Y=(N,)`.
- An `N`-class confusion matrix can be converted to a vector of binary confusion
  matrices using the one-vs-all strategy by calling `cm.one_vs_all()`. This results in a
  binary confusion matrix of shape `(X, N, 2, 2)`.
- Whenever a result is a scalar, we return it as such. This is, e.g., the case when
  computing a scalar metric on a single confusion matrix, i.e., `X=Y=()`.

#### Available metrics

Basic parameters

1. TP (true positive)
2. TN (true negative)
3. FP (false positive)
4. FN (false negative)
5. P (condition positive)
6. N (condition negative)
7. TOP (test outcome positive)
8. TON (test outcome negative)
9. POP (population)

Class metrics

1. TPR (true positive rate) + confidence interval
2. TNR (true negative rate) + confidence interval
3. FPR (false positive rate) + confidence interval
4. FNR (false negative rate) + confidence interval
5. TOPR (test outcome positive rate)
6. TONR (test outcome negative rate)
7. PPV (positive predictive value)
8. NPV (negative predictive value)
9. FDR (false discovery rate)
10. FOR (false omission rate)
11. Class accuracy
12. Class error rate

Overall metrics

1. Accuracy
2. Error rate

### Bootstrapping confidence intervals

The library uses bootstrapping to compute confidence intervals for arbitrary metrics,
scalar or vector-valued. A metric is simply a function that takes a `Scores` object as
input.

```python
def mean_score(scores: Scores) -> np.ndarray:
    """Metric computes the mean of positive and negative scores."""
    return np.array([np.mean(scores.pos), np.mean(scores.neg)])
```

With the metric defined, we can compute its confidence intervals using `bootstrap_ci()`.

```python
scores = Scores(
  pos=np.random.normal(1., 0.5, 100),
  neg=np.random.normal(0., 0.5, 100)]
)
ci = scores.bootstrap_ci(metric=metric, alpha=0.05)

# Metrics that are attributes of the Scores class can be accessed by name.
# Any kwargs, here `threshold, will be passed on to the metric.
ci = scores.bootstrap_ci(metric="fnr", threshold=0.3)

print(f"FNR 95%-CI: ({ci[0]:.3%}, {ci[1]:.3%})")
```

#### What is bootstrapping?

Let us assume that we have a dataset $$\mathbf{x} = (x_1, \ldots, x_n)$$ and a metric
$$m(\mathbf{x})$$ for which we want to compute confidence intervals. Bootstrapping does
the following:

- From our dataset $$\mathbf{x} = (x_1, \ldots, x_n)$$, we sample with replacement a new
  dataset $$\mathbf{x'} = (x'_1, \ldots, x'_n)$$ of the same size and we compute the
  metric $$m(\mathbf{x}')$$ on the sampled dataset;
- We repeat the process $$N$$ times (usually $$N=1,\!000$$) and obtain $$N$$
  measurements of the metric $$(m_1, \ldots, m_N)$$;
- The 95%-CI for $$m(\mathbf{x})$$ is given by the 2.5%- and 97.5%-quantiles of the set
  of bootstrapped measurements $$\mathbf{x'} = (x'_1, \ldots, x'_n)$$.

The beauty of this procedure is that while we will apply it to the accuracy or the
difference of two accuracies, it can be applied without change to any metric $$m$$
computed from a dataset. While there are explicit algorithms to compute confidence
intervals for metrics with known distribution, such as FNR, FPR, etc., we can use
bootstrapping to also compute confidence intervals for Equal Error Rate (EER) as
well as the threshold at which EER is attained.

```python
ci = scores.bootstrap_ci(metric="eer") # eer() returns a tuple `(threshold, eer)`.
print(f"EER 95%-CI: ({ci[1, 0]:.3%}, {ci[1, 1]:.3%})")
print(f"Threshold 95%-CI: ({ci[0, 0]:.4f}, {ci[0, 1]:.4f})")
```

#### Customizing bootstrapping

The `score-analysis` library provides several tools to customize the bootstrapping
algorithm.

When sampling the bootstrap sample, the default method is to use sampling with
replacement. For efficiency reasons, the `Scores` class stores positive and negative
scores separately as sorted arrays. Sampling with replacement returns an unsorted array,
which needs to be sorted before we can perform thresholding or metric computations. 

To speed up bootstrapping, we implement a single pass sampling method, which is an
approximation to sampling with replacement. When performing sampling with replacement
the number of times each data point is included in a particular sample follows the
Binomial distribution. So we can instead sample for each datapoint, how often to include
it in the bootstrap sample. If the dataset has more than 100 samples, we approximate the
Binomial distribution with a Poisson distribution. This method allows us to sample an
already sorted bootstrap sample, but the resulting sample won't have exactly the same
size as the original dataset.

The slight variations in the size of the bootstrap sample are not a problem if the
dataset is large enough. And if the dataset is small, then sorting is fast in any case.
Thus, `score-analysis` implements as default a dynamic choice of sampling strategy: for
`Scores` objects with more than 100 positive and negative scores we use single pass
sampling and otherwise revert back to sampling with replacement,

On top of using quantiles of the bootstrapped measurements, the library implements *bias
correction (bc)* with and without *acceleration (bca)* to improve accuracy for metrics
with skewed non-normal value distributions. See Chapter 11 of [Computer Age Statistical
Inference](https://hastie.su.domains/CASI_files/PDF/casi.pdf) by Efron and Hastie for
details.

<!-- 
### Showbias

The `showbias` function can be used to measure how a user-specified metric differs
across groups of data. Typically, we would be interested in knowing how, for example,
FRR differs across different ethnicities, which would help us to understand if our
product is biased and performs better for some ethnicities than for others. However, the
function should be general enough to allow you to measure any variations in a metric
across different groups: You could, for example, use it to measure accuracy across
different document types or flagging rates across different SDK platforms. You could
even measure how Dogfido's FRR differs across different dog breeds:

![image info](images/showbias.png)

In its simplest case, the `showbias` function assumes that you have a pandas dataframe
with three columns:
- A `group` column that indicates group membership for every row, e.g.
  `female` and `male` values in a column called `gender`
- A `scores` column that contains the predicted scores (e.g. by a model)
- A `labels` column that contains the ground truth using integers

Imagine that you have a dataframe `df` that contains model predictions and
ground truth labels along with gender data.
```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'gender': np.random.choice(['female', 'male'], size=1000),
    'labels': np.random.choice([0, 1], size=1000),
    'scores': np.random.uniform(0.0, 1.0, 1000)
})
```

You can then just run the following to measure FRR per gender:
```python
from score_analysis import showbias

bias_frame = showbias(
    data=df,
    group_columns="gender",
    label_column="labels",
    score_column="scores",
    metric="fnr",
    threshold=[0.5]
)
print(bias_frame.to_markdown())
```
which should result in a table like this:
| gender   |   0.5 |
|:---------|------:|
| female   | 0.508 |
| male     | 0.474 |

Above, we have been passing a threshold of `0.5` (as also indicated by the column name).
You can pass several thresholds all at once, like so:
```python
bias_frame = showbias(
    data=df,
    group_columns="gender",
    label_column="labels",
    score_column="scores",
    metric="fnr",
    threshold=[0.3, 0.5, 0.7]
)
print(bias_frame.to_markdown())
```
which will result in several columns, one for every threshold:
| gender   |   0.3 |   0.5 |   0.7 |
|:---------|------:|------:|------:|
| female   | 0.311 | 0.508 | 0.705 |
| male     | 0.252 | 0.474 | 0.697 |

You can obtain metrics that are normalised. For example, you can normalise to the metric
measured across the entire dataset by passing `normalise="by_overall"` argument, like so:
```python
bias_frame = showbias(
    data=df,
    group_columns="gender",
    label_column="labels",
    score_column="scores",
    metric="fnr",
    threshold=[0.5],
    normalise="by_overall"
)
```

You can obtain confidence intervals by setting the `nb_samples` in the `BootstrapConfig`
to a value greater than `0`:
```python
from score_analysis import BootstrapConfig

bias_frame = showbias(
    data=df,
    group_columns="gender",
    label_column="labels",
    score_column="scores",
    metric="fnr",
    threshold=[0.5],
    bootstrap_config=BootstrapConfig(
        nb_samples=500,
        stratified_sampling="by_group"
    ),
    alpha_level=0.05
)
print(bias_frame.to_markdown())
```
In this case, `bias_frame` will have 4 properties:
- `bias_frame.values` contains the observed values
- `bias_frame.alpha` contains the alpha level
- `bias_frame.lower` contains lower bound of the CI
- `bias_frame.upper` contains upper bound of the CI

Imagine that you didn't only collect gender data in `df` but also age group data.
```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'gender': np.random.choice(['female', 'male'], size=1000),
    'age_group': np.random.choice(['<25', '25-35', '35-45', '45-55', '>55'], size=1000),
    'labels': np.random.choice([0, 1], size=1000),
    'scores': np.random.uniform(0.0, 1.0, 1000)
})
```

You can then just run the following to measure FRR per gender x age group combination:
```python
bias_frame = showbias(
    data=df,
    group_columns=["gender", "age_group"],
    label_column="labels",
    score_column="scores",
    metric="fnr",
    threshold=[0.5]
)
print(bias_frame.to_markdown(reset_display_index=True))
```
which should result in a table like this:
| gender   | age_group   |   0.5 |
|:---------|:------------|------:|
| female   | 25-35       | 0.514 |
| female   | 35-45       | 0.571 |
| female   | 45-55       | 0.52  |
| female   | <25         | 0.517 |
| female   | >55         | 0.509 |
| male     | 25-35       | 0.525 |
| male     | 35-45       | 0.435 |
| male     | 45-55       | 0.414 |
| male     | <25         | 0.529 |
| male     | >55         | 0.562 | 
-->
