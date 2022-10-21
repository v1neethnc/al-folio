---
layout: page
title: Benchmarking CNNs on Image Classification Task
description: Project to compare the performance of VGG16, VGG19, ResNet50, and ResNet101 on an image classification task.
importance: 4
img: assets/img/vgg_resnet/wandb_stats.png
category: academic
---

**NOTE: This project was done over a duration of 12 weeks to fulfill the Course Project requirement for CMSC 611 - Advanced Computer Architecture at University of Maryland, Baltimore County. The following is a paraphrased version of the 4 page project report that was written in conjunction with two peers: Tejaswini Manjunath, Gaurav Baser.**

Time Period: September, 2021 -- December, 2021
<br><br>

## Contents
-----
* [Introduction](#introduction)
* [Dataset](#dataset)
* [Project Approach](#project-approach)
* [Experiment](#experiment)
  * [ResNet](#resnet)
  * [VGG](#vgg)
* [Results](#results)
  * [Accuracy](#accuracy)
  * [Resource Utilization](#resource-utilization)
* [Conclusion](#conclusion)
<br><br>


## Introduction
-----
This project is aimed at training different popular CNNs for a multi-class image classification task and check their performance in order to find out the most viable CNN for a given use case in the presence of a specific, limited hardware resources. Benchmarking the performance of CNNs will help in establishing a baseline metric to judge the capabilities of the chosen CNNs. The CNNs chosen for this project are: VGG16, VGG19, ResNet50, ResNet101.
<br><br>

## Dataset
-----
The training and testing of the models was done on the default architecture that Google Colab provides. To performance a comprehensive evaluation, the ideal dataset is one that contains multiple classes, and has a sizeable training and testing dataset. To this end, the [Kaggle Intel Image Classification](https://www.kaggle.com/datasets/puneet6060/intel-image-classification) dataset seemed like the perfect match. This dataset over 14000 training images, and the validation dataset contains over 3000 images. There is a healthy class distribution of the six different classes. For the purposes of this experiment, the batch size was kept constant throughout the training and validation of all the models at 32 images per batch.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/training_distribution.png" title="Training Distribution" class="img-fluid rounded z-depth-1" %}
	</div>
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/validation_distribution.png" title="Testing Distribution" class="img-fluid rounded z-depth-1" %}
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
<br><br>

## Project Approach
-----
To find the best among the aforementioned CNNs, the evaluation is done on two fronts: performance of classification, and the resource utilization metrics. Because of limited resource availability, the training, testing, and the benchmarking are done on Google Colab, where GPU is freely available. The classification performance will include calculating metrics like accuracy, and the resource utilization statistics are computed by wandb.


## Experiment
-----
### ResNet
With a fixed batch size of 32, and images rescaled to 150 x 150, the ResNet50 and ResNet101 models were trained. The accompanying graph shows that the training set loss starts dropping to close to zero. Before the validation loss increases drastically, which signifies that the model is overfitting the training data, the training is stopped. That is why each model is trained for 20 epochs only. The training speed for ResNet50 and ResNet101 is observed to be 433ms/step and 772ms/step respectively.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/resnet50_training_stats.png" title="ResNet50 Training" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		ResNet50 Training
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/resnet101_training_stats.png" title="ResNet101 Training" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class=row>
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		ResNet101 Training
	</div>
</div>

### VGG
The base architecture was kept non-trainable to preserve the Imagenet weights, while new architecture on top of it was subject to the training process. The training images were converted to the standard VG616 sizes, which is 224x224 in 3 color channels. In order to make the best use of time and to avoid overfitting, the training was limited to 20 epochs. During training, it was observed that the training times for VGG16 and VGG19 was 122sec/epoch, where each epoch involved training the model, and calculating loss on validation dataset.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/vgg16_training_graphs.png" title="VGG16 Training" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		VGG16 Training
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/vgg19_training_graphs.png" title="VGG19 Training" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		VGG19 Training
	</div>
</div>
<br><br>


## Results
-----
### Accuracy
The accuracy of both the ResNet models is similar with an accuracy of 91.60% for ResNet50 and an accuracy of 92.82% for ResNet101. The accuracy of VGG models are comparable to ResNet with an accuracy of 92.37% for VGG16 and an accuracy of 92.80% for VGG19. The difference in accuracy between the ResNet and VGG models is not very significant.

### Resource Utilization
The first stand out observation is that ResNet101 is the longest running model of the lot. The training and validation of the model for 20 epochs takes about 100 minutes, compared to the 40 minutes it takes to train VGG16, 57 minutes to train VGG19, and 71 minutes to train ResNet50. ResNet101 is by far the most complex model in terms of architecture, and consequently takes far more time to train.

There is remarkable difference in the ways the models built on these architectures use the resources that they are provided with. The VGG family show a significant variance in GPU usage statistics. While training, the models often have shorts bursts of extreme GPU usage, followed by short spans of zero GPU usage. On the other hand, the ResNet models have a consistent usage of the GPU until the training and the validation process is done. The variance in GPU usage is far less diverse. This is further corroborated by GPU temperature statistics, where the temperature of the GPU is plotted against time taken to train the models. The GPUs are at a consistently higher temperature for the ResNet lines on the graph, while there are significant peaks and lows for the VGG models. 

While variance in the GPU utilization between VGG and ResNet is already documented, it can be observed that the short bursts of VGG models’ GPU usage often results in a higher amount of GPU being used, while ResNet has a consistent GPU utilization, with their highest being lower than VGG models’ highest.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/vgg_resnet/wandb_stats.png" title="Resource Consumption Statistics" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Resource Consumption Statistics
	</div>
</div>
<br><br>

## Conclusion
-----
The results show that compared to the ResNet, VGG has better performance in terms of GPU utilization and time. This can be seen in the fact that they are slightly more accurate than the ResNet models while taking lesser execution time. They show higher GPU utilization statistics, which is the desirable scenario when it comes to using GPU for image processing.

Such high performance, while being easy-to-use, makes models built on VGG architectures more desirable for a normal end user looking to perform simple image classification tasks.
