---
layout: post
title: "Face Recognition Research at IJCB 2020"
date: 2020-11-23 12:00:00
author: Martins Bruveris
tags: deep-learning face-recognition
---

In this post I review papers on face recognition that were presented at this year's International Joint Conference on Biometrics, <a href="https://ieee-biometrics.org/ijcb2020/">IJCB 2020</a>.

<!--more-->

### Identity Document to Selfie Face Matching Across Adolescence
by Vítor Albiero, Nisha Srinivas, Esteban Villalobos, Jorge Perez-Facuse, Roberto Rosenthal, Domingo Mery, Karl Ricanek, and Kevin W. Bowyer.

This <a href="https://arxiv.org/abs/1912.10021">paper</a> looks at a particular application of face matching: matching live images to photos from ID documents, with added complication that the photo on the ID document is from early adolescence (age 10-18), while the live photo is from later adolescence (age 18-19). Commercial off-the-shelf face matching solutions or CNNs such as ArcFace trained on MS-Celeb-1M show a significant performance drop when applied to this particular dataset. While not surprising it is another piece of evidence that all faces are not equal and that face recognition performance does depend on the dataset. Knowing how well a model performs on IJB-C is well and good, but not particularly relevant if the objective is to mach images of Chilean IDs as is done in this paper.

The authors then fine-tune the ArcFace model on the live photo/document dataset using triplet loss. Triplet loss was chosen, because their dataset only contains two images per identity: one live photo and one document photo. How much difference does fine-tuning make? For example, TAR@FAR=0.01% increases from 63% to &gt;95% when the age difference between live photo and document is 8-9 years. Given that the dataset contains only Chilean IDs of adolescents it is not possible to determine whether the reduced accuracy of off-the-shelf CNNs is caused by the subjects' age or because we are using images of documents.

### Face Quality Estimation and Its Correlation to Demographic and Non-Demographic Bias in Face Recognition
by Philipp Terhörst, Jan Niklas Kolf, Naser Damer, Florian Kirchbuchner, and Arjan Kuijper.

This <a href="https://arxiv.org/pdf/2004.01019.pdf">paper</a> looks at the connection between face matching-related image quality assessment and various kinds of biases in face matching, in particular bias with respect to pose, ethnicity and age. It is known that face matching systems exhibit various kinds of biases. And because many face quality assessment methods are either based on face matching scores or adapt to the face matching system it is deployed with, this opens the possibility for a bias transfer from face matching to face quality assessment. This bias can manifest itself in lower quality scores for certain demographic groups and thus lead to discriminatory effects during enrolment.

The authors evaluate four face quality assessment solutions: A commercial off-the-shelf method from Neurotechnology, the method by <a href="https://arxiv.org/abs/1706.09887">Best-Rowden and Jain</a>, <a href="https://arxiv.org/abs/1904.01740">FaceQnet</a> and stochastic embedding robustness (<a href="https://arxiv.org/abs/2003.09373">SER-FIQ</a>).

![]({{
'/assets/images/2020/11/face_quality_bias_fig_1.png' | relative_url }})
{: style="width:100%;" class="center"}
Figure 1 from the [paper](https://arxiv.org/pdf/2004.01019.pdf) showing error versus reject curves at FAR=0.1%.
{:.image-caption}

This figure shows how quickly the false non-match rate (FNMR) drops when a certain percentage of images with the lowest quality scores is removed. We can see that SER-FIQ is the best performing method across both models (ArcFace and FaceNet) and datasets (ColorFeret and Adience). Next, the authors turn their attention to bias: are image quality scores correlated with pose, ethnicity and age?

![]({{
'/assets/images/2020/11/face_quality_bias_fig_3.png' | relative_url }})
{: style="width:100%;" class="center"}
Figure 3 from the [paper](https://arxiv.org/pdf/2004.01019.pdf) showing the dataset composition at a given reject rate for ArcFace.
{:.image-caption}

These graphs for the ArcFace model show the composition of the dataset changes after removing a percentage of images with the lowest quality scores. If image quality scores were not correlated with these attributes, the dataset composition would remain constant. Here we see, for example, that profile faces are associated with lower image quality scores. This is not overly surprising. However, seeing correlation between image quality and ethnicity or image quality and age does raise questions. Or rather, it does confirm that face quality assessment is not free from bias.

### AdvFaces: Adversarial Face Synthesis
by Debayan Deb, Jianbang Zhang and Anil K. Jain.

Face recognition systems are known to be vulnerable to adversarial attacks. In this <a href="https://arxiv.org/abs/1908.05008">paper</a> the authors propose a GAN-method to generate adversarial examples with only minimal perturbations. The paper addresses both impersonation and obfuscation attacks.

![]({{
'/assets/images/2020/11/advfaces_fig_4.png' | relative_url }})
{: style="width:100%;" class="center"}
Figure 4 from the [paper](https://arxiv.org/abs/1908.05008).
{:.image-caption}

The method is as follows: They train a generator $$G$$ as an additive mask, i.e., for a given image $$x$$ the adversarial image is $$x + G(x)$$, and a discriminator $$D$$. They use a face matcher $$F$$ that outputs a similarity score $$F(x, y)$$ for any pair of images. A perturbation hinge loss,

$$\mathcal{L}_{\text{perturbation}} = \max(\varepsilon, \|G(x)\|_2 )\,,$$

encourages the perturbed image to be close to the original. An identity loss (for obfuscation),

$$\mathcal{L}_{\text{identity}} = \mathcal F(x, x + G(x))\,,$$

where $$\mathcal F$$ is cosine-similarity encourages identity-preservation of the perturbed image.

### Is Warping-based Cancellable Biometrics (still) Sensible for Face Recognition?
by Simon Kirchgasser, Andreas Uhl, Yoanna Martínez-Díaz and Heydi Mendez-Vazquez.

What are cancellable biometrics? Because biometric characteristics are immutable, storing them for the purpose of identification or verification creates long-term privacy risks in case the storage is compromised. A <em>cancellable </em>template is a transformation of the original template, generated with the help of a key, that is stored and used instead of the original template. In the context of face recognition, the transformation can be applied either in the image domain or in the face embedding domain and the method considered in this paper works in the image domain.

![]({{
'/assets/images/2020/11/cancellable_salting.jpg' | relative_url }})
{: style="width:60%;" class="center"}

A cancellable biometric scheme needs to satisfy the following <a href="http://www.scholarpedia.org/article/Cancelable_biometrics">properties</a>:
<ul>
 	<li>Different keys should generate non-commensurable templates, i.e., it should not be possible to match a template transformed with two different keys.</li>
 	<li>It should not be possible to reverse the transformation process, even with knowledge of the key.</li>
</ul>
Cancellable biometrics are related to <a href="https://en.wikipedia.org/wiki/Homomorphic_encryption">homomorphic encryption</a>: creating a cancellable template is the same as encrypting data; we can use the cancellable template to perform verification in that same way as homomorphic encryption allows us to perform computations on encrypted data without having to decrypt it; finally, we can only compare cancellable templates generated using the same key just as we can combine two pieces of encrypted data in a computation, only if the data has been encrypted with the same key.

![]({{
'/assets/images/2020/11/cancellable_fig_1.png' | relative_url }})
{: style="width:60%;" class="center"}

In this <a href="http://wavelab.at/papers/Kirchgasser20b.pdf">paper</a>, the authors investigate how safe a 20 year old warping-based method for face biometrics is considering the advances in face recognition. One can view cancellable face biometrics that work in the image domain, such as the warping method, as a special case of face obscuration. It is a special case, because the obscured image has to be unrecognisable, but still useable for verification, which makes it a much more difficult problem. And considering recent <a href="https://martinsbruveris.com/2020/11/face-recognition-research-at-fg-2020/#obscuration">work</a> in successfully reversing several obscuration algorithms, it is no big surprise that the authors showed that warping-based cancellable face images are not able to guarantee privacy and therefore should not be used.
