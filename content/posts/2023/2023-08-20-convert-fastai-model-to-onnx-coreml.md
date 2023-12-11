---
title: "Convert a FastAI model to ONNX and CoreML"
author: [ "Denis Nutiu" ]
date: '2023-08-20'
categories:
  - "AI"
  - "Tutorial"
permalink: /2023/08/20/convert-fastai-model-to-onnx-coreml/
tags:
  - fast ai
  - onnx
  - coreml
  - machine learning
  - deep learning
layout: post
---

![coreml convert](/hugo-content/2023-07/coreml-convert.png)

Hello there 👋,

In this post, I will show you how to convert a FastAI model to ONNX and CoreML.

# Convert a FastAI model to Pytorch

Before we can convert the model to ONNX format or CoreML, we need to convert it to Pytorch first.

This is a simple process. Assuming that the model is trained, we need to call `torch.save` to export the model:

```python
from fastai.vision.all import *

# learn is your FastAI Learner

torch.save(learn.model, "/models/model.pth")
```

# Convert a FastAI model to ONNX

ONNX is a standard for machine learning models. It allows you to convert a model from a framework like PyTorch or
TensorFlow to its format in order to be used in different runtimes, for exampl Windows, Android, Linux, MacOS and iOS
and from various programming languages like C#, C++, Java, JavaScript and Python.

To convert your FastAI model to ONNX, follow these steps:

Assuming that you have the `torch` package installed, to convert the model to ONNX you need to run the following:

```python
import torch


model_path = "/models/model.pth"

model = torch.load(model_path, map_location=torch.device('cpu'))
model.eval()

dummy_input = torch.randn(1, 3, 224, 224)
torch.onnx.export(model, dummy_input, "models/model.onnx", export_params=True)
```

I'm using `torch.randn` to create a dummy input. This is required by the `torch.onnx.export` function.

Since I am exporting a image classification model, the input shape is `(1, 3, 224, 224)`. The first dimension is the
batch size, the second is the number of channels, and the last two are the width and height of the image.

# Convert a FastAI model to CoreML

CoreML is a framework developed by Apple that allows you to run machine learning models on the Apple ecosystem, 
which includes: iOS, MacOS, iPadOS, watchOS and tvOS.

**According to the CoreML documentation, you will need a MacOS or a Linux machine to convert your model to CoreML.
I tried to convert the model on Windows, but I got an error.**

To convert your FastAI model to CoreML, follow these steps, in a new project:

1. Create a new Python project with Python 3.10 and install the following packages:

```bash
fastai==2.7.12
torch==2.0.0
torchvision==0.15.1
scikit-learn==1.1.0
coremltools==6.3.0
```

2. Create a new file called `main.py` and add the following code:

```python
# convert torch model torchscript traced

import torch
import coremltools as ct

model_name = "resnet151"
model_path = f"./{model_name}.pth"

model = torch.load(model_path, map_location=torch.device("cpu"))
model.eval()

# Trace the model with random data.
dummy_input = torch.rand(1, 3, 224, 224)
traced_model = torch.jit.trace(model, dummy_input)

# Load the class labels.
class_labels = []
with open("categories.txt", "r") as f:
    class_labels = [line.strip() for line in f.readlines()]

# Using image_input in the inputs parameter:
# Convert to Core ML program using the Unified Conversion API.
scale = 1 / (0.226 * 255.0)
bias = [-0.485 / (0.229), -0.456 / (0.224), -0.406 / (0.225)]

model = ct.convert(
    traced_model,
    convert_to="mlprogram",
    inputs=[
        ct.ImageType(
            name="input_image",
            shape=(1, 3, 224, 224),
            color_layout=ct.colorlayout.RGB,
            scale=scale,
            bias=bias,
        )
    ],
    classifier_config=ct.ClassifierConfig(class_labels)
)

# Save the converted model.
model.save(f"{model_name}.mlpackage")

```

3. Run the script:

```bash
python main.py
```

You should get a file called `resnet151.mlpackage`. This is the CoreML model.

That's it! Thank you for reading! 📚
