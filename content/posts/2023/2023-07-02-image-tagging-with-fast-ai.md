---
author: ["Denis Nutiu"]
categories:
- "AI"
- "Python"
- "Tutorial"
date: '2023-07-02'
layout: post
tags:
- python
- programming
- ai
- deep learning
- fast api
- onnx
title: "Image tagging with Deep Learning"
---

![image](/hugo-content/2023-07/image-tagger.png)

Hello everyone 👋,

I've been playing with FastAI and deep learning for a week or so and I've decided the combine my passion for software
with my passion for photography. I'm working on an app or maybe a library that allows you to generate tags for a photo.
For an amateur photographer like me it's quite useful as I don't always have inspiration to write tags for my photos and sometimes
the tags I write are inconsistent.

I already wrote an image scrapper using Python and I'm building my dataset as we speak. For now since I don't have any other
knowledge with another library I'm using FastAI for the image classification. I'm planning to use a different library when
I'll learn more about deep learning.

In this post I'll write about building a multi-label image classifier using FastAI.

# The Dataset

The dataset has the following structure:

```bash
dataset/image1.jpg
dataset/image2.jpg
train.ndjson
```

The `train.ndjson` file contains the labels for each image in the dataset amd the image filename:

*The .ndjson file is a JSON file where each line is a JSON object.*

```text
{"image": "image1.jpg", "labels": ["tag1", "tag2"]}
{"image": "image2.jpg", "labels": ["tag3", "tag4"]}
```

Since I'm not planning to publish the dataset you can try to build your own using the structure above.

# The Model

The model is a simple Resnet18 model, I'm using it for experimentation. Later I'll use a different model.

```python
from fastai.vision.all import *

def get_x(r):
    return Path("C:/Users/nutiu/PycharmProjects/imageKeyworderDataset/dataset/dataset") / r['filename']


def get_y(r):
    return r['tags']


# Datablock Object
dblock = DataBlock(
    blocks=(ImageBlock, MultiCategoryBlock),
    get_x=get_x,
    get_y=get_y,
    splitter= RandomSplitter(valid_pct=0.2, seed=42),
    item_tfms=RandomResizedCrop(224, min_scale=0.35)
)
```

The `get_x` and `get_y` functions are used to get the image path and the labels for each image.

The `DataBlock` object is used to build the `DataLoaders` object which is used to train the model.

The following code will load the dataset:

```python
data = []
with open("C:/Users/nutiu/PycharmProjects/imageKeyworderDataset/dataset/normalized_train.ndjson", "r") as f:
    for line in f:
        data.append(json.loads(line))

```

And the following code will create the `DataLoaders` object:

```python
# Create dataloaders by spcifying the batch_size and data source
dls = dblock.dataloaders(data, bs=64)

# Visualize some samples in a batch
dls.show_batch(nrows=1, ncols=4)

# Create a learner with resnet152, resnet50, or resnet18, densenet161 ; base_lr=3e-3
learn = cnn_learner(dls, resnet18, metrics=partial(accuracy_multi, thresh=0.2))
# only one epoch needed to bring up the weights freeze_epoch=1
learn.fine_tune(3, freeze_epochs=4)
```

The `learn.fine_tune(3, freeze_epochs=4)` will train the model for 3 epochs and freeze the first 4 epochs.

# The Results

The results are quite good, my dataset consisted of ~7k images and there's room for improvement.

Given the following image, the model will predict it's tags.

![image](/hugo-content/2023-07/image-tagger.png)

I converted the model into a Pytorch Model and then into an ONNX model. I've used the ONNX Runtime to run the model
inside a web browser on the client side.

You can use the app here:  https://beautiful-tartufo-6d25b3.netlify.app/

# Conclusion

Training a model with FastAI is quite easy and the results are quite good. You can also convert it to Pytorch and then
to ONNX Runtime in order to run it on a mobile device or on a web browser. So far It's been really fun.

Thank you for reading! 🙏