---
author: ["Denis Nutiu"]
categories:
- "AI"
- "Python"
- "Tutorial"
date: '2023-06-21'
layout: post
tags:
- python
- programming
- ai
- deep learning
- fast api
title: "Deploy a FastAI model with FastAPI"
permalink: /2023/06/21/fastai-model-deployment-fastapi/
---

Hello everyone 👋,

In this quick post I'll show you how to deploy a FastAI model using FastAPI.

It's a follow-up to my previous post from [here](/posts/2023/2023-06-20-fast-ai-gpu-support/) and it is partially based on this FastAI lecture:

{{< youtube F4tvM4Vb3A0 >}}

# Exporting the Model

Before we create the API, we need to export the model as a pickle file.

Add `learn.export("<path>/cat_or_dog.pkl")` with the full absolute export path at the end of the script:

```python
# ...
learn = vision_learner(dls, resnet34, metrics=error_rate)
learn.fine_tune(1)

learn.export("C:\\Users\\nutiu\PycharmProjects\\fastaiIntro\cat_or_dog.pkl")
```

# Deployment with FastAPI

Let's create a new project and install the required dependencies:

```bash
pip install fastai
pip install fastapi
pip install python-multipart
pip install "uvicorn[standard]"
```

Now create a `main.py` Python file that we'll use to define our FastAPI app. Note that this is just a quick tutorial,
it's not meant to be a full introduction to FastAPI, you can find more details about it [here](https://fastapi.tiangolo.com/).

The FastAPI handler is a simple post endpoint that accepts a file and returns a JSON response with the prediction, in
case the file is not an image, it will return a 400 response.

The `is_cat` function is required for importing the model, if it's not defined the model will not be loaded correctly.

```python
import PIL
from fastai.learner import load_learner
from fastapi import FastAPI, UploadFile, HTTPException

app = FastAPI()


def is_cat(x):
    return x[0].isupper()


cat_or_dog_model = load_learner(open("cat_or_dog.pkl", "rb"))


@app.post("/predict/cat-or-dog")
async def post_predict_cat_or_dog(file: UploadFile):
    try:
        file_contents = await file.read()
        is_cat, _, confidence = cat_or_dog_model.predict(file_contents)
        return {
            "is_cat": True if is_cat == "True" else False,
            "confidence": float(confidence[1])
        }
    except PIL.UnidentifiedImageError as e:
        raise HTTPException(status_code=400, detail="File is not an image")

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

That's it, you can now run the app using `python main.py` and test it using curl:

```bash
curl -X 'POST' \
  'http://localhost:8000/predict/cat-or-dog' \
  -H 'accept: application/json' \
  -H 'Content-Type: multipart/form-data' \
  -F 'file=@photo-1608848461950-0fe51dfc41cb.jfif;type=image/jpeg'
```

You should get a response similar to this:

```json
{
  "is_cat": true,
  "confidence": 1
}
```

I get around 70ms response time on my machine.

# Conclusion

I've showcased a simple way to deploy a FastAI model using FastAPI, by exporting it to a pickle file and then loading it in the API.

Another more advanced way to deploy the model would be to use the [ONNX Runtime](https://onnxruntime.ai/). 
Other ways to deploy the model are also showcased by Jeremy in the video above.

Thank you for reading! 📚🫶

If you want to be notified when I post new content, please subscribe to my [newsletter](https://nuculabs.dev/) 📰.
