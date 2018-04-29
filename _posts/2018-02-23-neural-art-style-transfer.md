---
layout: post
title: "Neural Art Style Transfer"
date: 2018-02-23 12:00:00 -0700
category: neural-networks
---

An attempt to use the [neural art transfer style](https://github.com/tensorflow/magenta/blob/master/magenta/reviews/styletransfer.md) technique to combine a style with
an image. The technique requires the minimization of a loss function
between two images, one being the "style" image while the other is the
"content" image. A pretrained deep convolution network called
[VGG-19](http://www.robots.ox.ac.uk/~vgg/research/very_deep/) is used
to extract features from both of the images. The loss function for
each image is extracted through Gram matrices, which is defined as the
inner product between each given network layer. Both of the content and style images were transformed to have the
dimensions of `400 x 300`. A total of `200` iterations produce the
generated image.

| ![](/assets/nst_example.jpg) |
|:--:|
| *Combined image pane showing the content image (left), the style image (center), and the generated image (right).* |
