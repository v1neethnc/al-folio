---
layout: page
title: Data Augmentation Comparison Study
description: Project to test the efficacy of an image classification training dataset with and without GAN generated data augmentation.
importance: 1
img: assets/img/gan_comp/gen_img.png
category: academic
---

**NOTE: This project was done over a duration of 7 weeks to fulfill the Course Project requirement for CMSC 678 - Introduction to Machine Learning at University of Maryland, Baltimore County. The following is a paraphrased version of the 6 page project report that was written in conjunction with two peers: Tejaswini Manjunath, Sai Charita Thati.**

Time Period: April, 2021 -- May, 2021
<br><br>

## Contents
-----
* [Introduction](#introduction)
* [Generative Adverserial Networks](#generative-adversarial-networks)
* [Approach](#approach)
* [Implementation](#implementation)
  * [Dataset](#dataset)
  * [GAN Training](#gan-training)
  * [Classification Model Training](#classification-model-training)
* [Results](#results)
* [Conclusion](#conclusion)
<br><br>

## Introduction
-----
Any machine learning project requires a training and testing dataset, which contain data of the highest quality, and enough instances of it for the machine learning model to properly understand the dataset without developing any bias. That being said, when a model training is done on insufficient or imbalanced dataset, the neural networks perform very poorly because they do not learn enough from the training data.

This project is aimed at comparing the use of several ways to deal with insufficient data, with an increased on focus towards using Generative Adversarial Networks to augment the training dataset. We can use Generative Adverserial Networks (GANs) to generate new images, which can then be added to the training dataset in order to improve the training process. Over the course of this project, we compare different data augmentation techniques to determine whether using GANs for such a task is viable or not.
<br><br>

## Generative Adversarial Networks
-----
GANs refer to a set of frameworks in machine learning that look for patterns in the input data and learn them for the purpose of being able to generate examples or data points that could have been a part of the original dataset. To ensure that the generated samples are a good representation of the data points in the dataset, the two components of GAN (generator and discriminator) are trained in a zero-sum game. The generator reads the input data, learns the patterns, and generates examples of its own. These examples are then sent to the discriminator, which takes on the task of identifying whether the data it is reading is fake or not. If the generator wins more than 50% of the time, then the discriminator is said to be fooled and the generated data is considered as a reasonably accurate representation of the original input dataset.
<br><br>

## Approach
-----
The project contains two phases: a GAN to generate new training data, and an image classification model to use to check the impact of the generated data. The first phase involves training the generator to generate images, and training the discriminator to predict if the generated data is artificial or not. Based on the discriminator's performance, the loss is calculated and backpropagated through the generator to update the weights. The training happens until the model is reasonably confident that the generator's outputs are able to fool the discriminator. Therefore, a GAN for each class in the training dataset is created.

The image classification model will be trained multiple times, with different datasets each time to assess the impact of the difference in the datasets on the classification task. This will result in multiple sets of model statistics, which can then be contrasted against each other in order to compare the performance of the models. All these models will be tested against the same test dataset and different performance metrics will be computed. This will give a complete picture of the difference that the augmented data makes in the model’s understanding of the training dataset.
<br><br>

## Implementation
-----
### Dataset
The training and testing of the models was done on the Macbook Pro M1 Processor. The Kaggle Intel Image Classification dataset is used for this project because it is well rounded both in terms of class distribution and the number of instances in both training and validation. The training dataset contains over 14000 images, and the validation dataset contains over 3000 images. While there is a test dataset, it is unlabelled. Since the given test dataset is unlabelled, we are splitting the validation dataset in a 7:3 ratio to create a development and a test dataset respectively.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/train_distribution.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/test_distribution.png" title="Testing Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
</div>

<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Training Dataset Distribution
	</div>
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Testing Dataset Distribution
	</div>
</div>
<br>

### GAN Training
The code for the GAN was written in PyTorch using Google Colab as the development environment. The dataset is loaded in batches of 64. Originally, the idea was to train a GAN that would learn from images across all the classes, where the discriminator would also predict the class of the image. However, the training did not work as hoped, and therefore the direction of the GAN training was changed such that a GAN was generated for each class, and images were generated accordingly.

The [architecture](https://arxiv.org/pdf/1511.06434.pdf) chosen for the GAN is one that has been experimentally proven to be effective with 64x64x3 dimension image data. The input to the generator is the original image resized to 64x64x3, and the output is an image of the same dimensions that is different from the original image. The discriminator takes this as input and uses a sigmoid activation function to determine the "realness" of the image sent by the generator.

Weights are randomly initialized before the training, with the discriminator being trained first on the input dataset for an epoch. The loss is then calculated and backpropagated through the generator to update its weight, after which the generator is trained on the original input dataset. After 200 epochs of training at a learning rate of 0.0002, a thousand random images are generated for every one of the six classes. Therefore, 6000 images are generated at the end of training. However, to ensure that we have sufficient data for this project, two batches of 6000 images each were generated.
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/buildings_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/forests_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Buildings Class Training</b>
	</div>
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Forests Class Training</b>
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/glaciers_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/mountains_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Glaciers Class Training</b>
	</div>
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Mountains Class Training</b>
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/seas_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/streets_200_epochs.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Seas Class Training</b>
	</div>
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		<b>Streets Class Training</b>
	</div>
</div>
<br>

The losses for training each class are plotted on a graph. There is a clear downward trend in the loss of the generator and the discriminator during the training process, with the spikes being the result of adjustment of weights. A sample of 64 generated images at the end of the training is also presented.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/gan_comp/gen_img2.png" title="Generated Images Sample" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="caption">
	Sample of Generated Images using GAN.
</div>

### Classification Model Training
Following the superior performance of the VGG19 model from the [CNN benchmarking project](2021-09-01-benchmarking-cnns-classification.md), we decided to go with building a multi-class image classification CNN using the VGG19 architecture. The base architecture was kept non-trainable to preserve the Imagenet weights, while the new architecture on top of it was subject to the training process. The images were converted to 64x64 as the GAN generated images are 64x64 in a three color channel format. For the purposes of this experiment, the model chosen was one that went through 20 epochs of training properly. Each epoch involved training the model and also calculating the accuracy on validation dataset in order to see the progress in the model’s performance.

For data augmentation, the ImageDataGenerator from Keras was used to perform on-the-fly augmentation. It transforms the original dataset to adhere to the input specifications, thereby reducing the number of steps and the amount of time taken for preprocessing. A number of scenarios involving traditional data augmentation techniques and GAN generated data augmentation were considered and tested to gain a better understanding of the performance of both the GAN and the classification model.
<br><br>

## Results
-----
There are seven scenarios that we considered, the results of which are tabulated as follows:

| Dataset &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;	| Training Proportion &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 	| Performance 	|
|---	|---	|---	|
| Original Data 	| 14034 	| 86.38% 	|
| Traditional Augmentation (Training and Validation) 	| 14034 	| 67.07% 	|
| Traditional Augmentation (Only Training) 	| 14034 	| 79.74% 	|
| GAN generated images (one batch) 	| 6000 	| 61.94% 	|
| GAN generated images (two batches) 	| 12000 	| 69.87% 	|
| Training data + one GAN batch 	| 14034+6000 	| 84.26% 	|
| Training data + two GAN batches 	| 14034+12000 	| 84.38% 	|

<br><br>

## Conclusion
-----
While, in this case, the original dataset performs better than the GAN augmented dataset, there is hope for using GAN as a way to generate better training datasets. Given a sizeable training dataset, GANs are capable of generating reasonably good images that can be used for classification. This project could not further explore because of the significantly restrictive infrastructure and environment. If more resources and more time was given, a GAN to work with 128x128x3 or 224x224x3 dimensional image data could be created and used to generate better images for the classification models to learn from. This is because of the availability of more information in the form of higher image resolution, and therefore better convolutional blocks, which eventually leads to higher performance by the GAN.
