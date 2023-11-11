---
title: "Using an ONNX Resnet model in Java"
author: ["Denis Nutiu"]
date: '2023-07-04'
categories:
- "AI"
- "Programming"
- "Tutorial"
permalink: /2023/07/04/using-an-onnx-resnet-model-in-java/
tags:
- java
- programming
- ai
- deep learning
- onnx
layout: post
---

Hello everyone! 👋

This is a follow-up post to the previous one, long story short I started taking the course from [fast.ai](https://course.fast.ai/)
to learn more about Deep Learning. I've built a simple Image Tagging model using the [Resnet](https://arxiv.org/abs/1512.03385) architecture
(don't worry about the paper I did not read it, GitHub Copilot suggested it while writing this blog post).

FastAI is a high-level Python library that allows you to train complex ML models really fast and efficiently. 
After training my model, I've exported it into the [ONNX](https://onnx.ai/) format and in this post we're using the model in a Java application.

The model will do image keywording. You can give it an image, and it will give you some keywords that describe the image.
If you're a photographer, you can use this model to tag your images and make them easier to find. 📸

# Java ONNX Runtime

Create a new Java with a Maven project and add the following dependency:

```xml
    <dependency>
        <groupId>com.microsoft.onnxruntime</groupId>
        <artifactId>onnxruntime</artifactId>
        <version>1.15.1</version>
    </dependency>
```

This is the only dependency that we need.

# The Model

If you want to follow along, you can grab the model from [here](https://github.com/dnutiu/image-tagger-vue/blob/main/public/resnet34_10_epochs.onnx)

The model classes are available [here](https://github.com/dnutiu/image-tagger-vue/blob/main/src/model/classes.js). 
You will need to convert them into a Java array or list.

I did save my categories in the following Categories class in the following format: `Categories.java`:

```java
package main.java;

import java.util.Arrays;
import java.util.List;

public class Categories {
    public static List<String> CATEGORIES = Arrays.asList(
        "1",
        "2",
        "3",
    );
}
```

# The Code

In order to obtain the predictions from an image we need `the model`, the `categories`, and the image that we're going to keyword. 
We'll need to do the following steps:

1. Load the model
2. Create an input tensor
3. Run the model
4. Get the output tensor
5. Get the predictions

The code that does this is the following:

```java
    public void run() throws OrtException {
        System.out.println("Onnx Runtime Java Image Prediction");

        // 1. Load model.
        var env = OrtEnvironment.getEnvironment();
        var session = env.createSession(this.modelPath, new OrtSession.SessionOptions());

        // Get input and output names
        var inputName = session.getInputNames().iterator().next();
        var outputName = session.getOutputNames().iterator().next();

        // 2. Create input tensor
        OnnxTensor inputTensor = OnnxTensor.createTensor(env, processImage(this.imagePath));

        // 3. Run the model.
        var inputs = Map.of(inputName, inputTensor);
        var results = session.run(inputs);

        // 4. Get output tensor
        var outputTensor = results.get(outputName);

        if (outputTensor.isPresent()) {
            // 5. Get prediction results
            float[][] floatBuffer = (float[][]) outputTensor.get().getValue();

            ArrayList<String> predictions = new ArrayList<String>();

            // filter buffer by treashold
            for (int i = 0; i < floatBuffer[0].length; i++) {
                if (floatBuffer[0][i] > -0.5) {
                    predictions.add(String.format("%s: %f - %s", i, floatBuffer[0][i], Categories.CATEGORIES.get(i)));
                }
            }

            // Print results
            System.out.println("Predictions: ");
            for (String prediction : predictions) {
                System.out.println(prediction);
            }
        } else {
            System.out.println("Failed to predict!");
        }
    }
```

☕

And of course step two: `2. Create an input tensor` is not included in the code from above because 
it's a little bit complicated. The code is in the `processImage(this.imagePath)` method.

If you run the program, you'll get the following output, depending on the image that you're using:

```text
Onnx Runtime Java Image Prediction
Predictions: 
25: -0.032844 - architecture
150: 0.288728 - day
381: -0.130801 - nature
387: 0.343386 - no people
404: 1.527045 - outdoors
```

# Processing the image

This section describes the code from the `processImage` method and the second step: `2. Create an input tensor`.

The input tensor is a 4-dimensional array of floats. The dimensions are: `1 x 3 x 224 x 224`.

The first dimension is the batch size. In our case, we're only going to predict one image at a time so the batch size is 1.

The second dimension is the number of channels. We're using RGB images, so we have 3 channels.

The last two dimensions are the width and height of the image. In our case, we're using 224x224 images.

*Note: I had no idea that the first dimension is the batch size, GitHub copilot generated that paragraph 😲.*

The process image code does the following things:

1. Read the image
2. Crop the image
3. Resize the image to 224x224
4. Computes the tensor values from the RGB.

```java
    public float[][][][] processImage(String imagePath) {
        try {
            float[][][][] tensorData = new float[1][3][224][224];
            var mean = new float[] { 0.485f, 0.456f, 0.406f };
            var standardDeviation = new float[] { 0.229f, 0.224f, 0.225f };

            // Read image
            File imageFile = new File(imagePath);
            var image = ImageIO.read(imageFile);

            // crop image
            int width = image.getWidth();
            int height = image.getHeight();
            int startX = 0;
            int startY = 0;
            if (width > height) {
                startX = (width - height) / 2;
                width = height;
            } else {
                startY = (height - width) / 2;
                height = width;
            }
            image = image.getSubimage(startX, startY, width, height);

            // DEBUG save image to disk
            // ImageIO.write(image, "jpg", new File("C:\\Users\\NUCULABS\\IdeaProjects\\untitled\\src\\test\\java\\main\\resources\\debug.jpg"));

            // Resize image
            var resizedImage = image.getScaledInstance(224, 224, 4);

            // Process image
            BufferedImage scaledImage = new BufferedImage(224, 224, BufferedImage.TYPE_INT_ARGB);

            scaledImage.getGraphics().drawImage(resizedImage, 0, 0, null);

            for (var y = 0; y < scaledImage.getHeight(); y++) {
                for (var x = 0; x < scaledImage.getWidth(); x++) {
                    int pixel = scaledImage.getRGB(x,y);

                    // Get RGB values
                    tensorData[0][0][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[0]) / standardDeviation[0];
                    tensorData[0][1][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[1]) / standardDeviation[1];
                    tensorData[0][2][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[2]) / standardDeviation[2];
                }
            }

            return tensorData;
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
```

# Conclusion

This is how you can use the ONNX Runtime Java API to make predictions from images.

Thank you for reading! If you have any questions, please leave a comment below. ❤️

### Full code:

Full code for the `Program.java` file:

```java
package main.java;

import ai.onnxruntime.OnnxTensor;
import ai.onnxruntime.OrtEnvironment;
import ai.onnxruntime.OrtException;
import ai.onnxruntime.OrtSession;
import javax.imageio.ImageIO;

import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Map;

public class Program {
    String modelPath = "C:\\Users\\nutiu\\IdeaProjects\\untitled\\src\\test\\java\\main\\resources\\resnet34_10_epochs.onnx";
    String imagePath = "C:\\Users\\nutiu\\Downloads\\4.jpg";


    public float[][][][] processImage(String imagePath) {
        try {
            float[][][][] tensorData = new float[1][3][224][224];
            var mean = new float[] { 0.485f, 0.456f, 0.406f };
            var standardDeviation = new float[] { 0.229f, 0.224f, 0.225f };

            // Read image
            File imageFile = new File(imagePath);
            BufferedImage bufferedImage = ImageIO.read(imageFile);

            // Crop image
            int width = image.getWidth();
            int height = image.getHeight();
            int startX = 0;
            int startY = 0;
            if (width > height) {
                startX = (width - height) / 2;
                width = height;
            } else {
                startY = (height - width) / 2;
                height = width;
            }
            var image = image.getSubimage(startX, startY, width, height);
            // ImageIO.write(image, "jpg", new File("C:\\Users\\nutiu\\IdeaProjects\\untitled\\src\\test\\java\\main\\resources\\debug.jpg"));

            // Resize image
            var resizedImage = image.getScaledInstance(224, 224, 4);

            // Process image
            BufferedImage scaledImage = new BufferedImage(224, 224, BufferedImage.TYPE_4BYTE_ABGR);
            scaledImage.getGraphics().drawImage(resizedImage, 0, 0, null);



            for (var y = 0; y < scaledImage.getHeight(); y++) {
                for (var x = 0; x < scaledImage.getWidth(); x++) {
                    int pixel = scaledImage.getRGB(x,y);

                    // Get RGB values
                    tensorData[0][0][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[0]) / standardDeviation[0];
                    tensorData[0][1][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[1]) / standardDeviation[1];
                    tensorData[0][2][y][x] = (((pixel >> 16) & 0xFF) / 255f - mean[2]) / standardDeviation[2];
                }
            }


            return tensorData;
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public void run() throws OrtException {
        System.out.println("Onnx Runtime Java Image Prediction");

        // 1. Load model.
        var env = OrtEnvironment.getEnvironment();
        var session = env.createSession(this.modelPath, new OrtSession.SessionOptions());

        // Get input and output names
        var inputName = session.getInputNames().iterator().next();
        var outputName = session.getOutputNames().iterator().next();

        // 2. Create input tensor
        OnnxTensor inputTensor = OnnxTensor.createTensor(env, processImage(this.imagePath));

        // 3. Run the model.
        var inputs = Map.of(inputName, inputTensor);
        var results = session.run(inputs);

        // 4. Get output tensor
        var outputTensor = results.get(outputName);

        if (outputTensor.isPresent()) {
            // 5. Get prediction results
            float[][] floatBuffer = (float[][]) outputTensor.get().getValue();

            ArrayList<String> predictions = new ArrayList<String>();

            // filter buffer by treashold
            for (int i = 0; i < floatBuffer[0].length; i++) {
                if (floatBuffer[0][i] > -0.5) {
                    predictions.add(String.format("%s: %f - %s", i, floatBuffer[0][i], Categories.CATEGORIES.get(i)));
                }
            }

            // Print results
            System.out.println("Predictions: ");
            for (String prediction : predictions) {
                System.out.println(prediction);
            }
        } else {
            System.out.println("Failed to predict!");
        }
    }
}
```