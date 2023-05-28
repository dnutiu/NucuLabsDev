---
author: "Denis Nu\u021Biu"
categories:
- Linux
- Programming
- Tutorial
- Web Development
date: '2021-05-18T15:34:24+00:00'
guid: https://nuculabs.dev/?p=1376
id: 1376
layout: post
permalink: /2021/05/18/fastapi-uvicorn-logging-in-production/
tags:
- fastapi
- gunicorn
- Python
- uvicorn
- web api
title: FastAPI Uvicorn logging in Production
---
Hello 🙋‍♂️,


Running a ⏩FastAPI ⏩ application in production is very easy and fast, but along the way some Uvicorn logs are lost.


In this article I will discuss how to write a custom UvicornWorker and to centralize your logging configuration into a single file.


To keep things as simple as possible I’ve put all my code in a single Python file.


### main.py


```
import uvicorn as uvicorn
from fastapi import FastAPI, APIRouter


router = APIRouter(prefix="")




def create_app():
    fast_app = FastAPI()
    fast_app.include_router(router)
    return fast_app




@router.get("/")
def read_root():
    return {"Hello": "World"}




if __name__ == '__main__':
    app = create_app()
    uvicorn.run(app=app)
```


Running the code will return a `{"Hello": "World"}` json when you visit the root endpoint `/` at http://127.0.0.1:8000. 😁


When you check the console window, the following log lines are printed:


```
INFO:     Started server process [10276]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     127.0.0.1:53491 - "GET / HTTP/1.1" 200 OK
```


Notice the Uvicorn log **GET / HTTP/1.1″ 200 OK**.


According to Uvicorn’s [deployment docs](https://www.uvicorn.org/deployment/) we should run Uvicorn in a production settings with the following command: `gunicorn -k uvicorn.workers.UvicornWorker main:create_app`.


```
(venv2) ➜  FastAPILogging gunicorn -k uvicorn.workers.UvicornWorker main:create_app
[2021-05-17 22:10:44 +0300] [6250] [INFO] Starting gunicorn 20.1.0
[2021-05-17 22:10:44 +0300] [6250] [INFO] Listening at: http://127.0.0.1:8000 (6250)
[2021-05-17 22:10:44 +0300] [6250] [INFO] Using worker: uvicorn.workers.UvicornWorker
[2021-05-17 22:10:44 +0300] [6252] [INFO] Booting worker with pid: 6252
[2021-05-17 22:10:45 +0300] [6252] [WARNING] ASGI app factory detected. Using it, but please consider setting the --factory flag explicitly.
[2021-05-17 22:10:45 +0300] [6252] [INFO] Started server process [6252]
[2021-05-17 22:10:45 +0300] [6252] [INFO] Waiting for application startup.
[2021-05-17 22:10:45 +0300] [6252] [INFO] Application startup complete.
```


Now, if we visit the root endpoint, the console won’t print **“GET / HTTP/1.1” 200 OK** anymore/ 🤦‍♂️.


To fix it we need a custom UvicornWorker ⚙ and a logging configuration file 🗃.


Create a new file and name it logging.yaml, then paste the following contents in it:


```
version: 1
disable_existing_loggers: false
formatters:
  standard:
    format: "%(asctime)s - %(levelname)s - %(message)s"
handlers:
  console:
    class: logging.StreamHandler
    formatter: standard
    stream: ext://sys.stdout
loggers:
  uvicorn:
    error:
      propagate: true
root:
  level: INFO
  handlers: [console]
  propagate: no
```


This file will configure our root logger and our Uvicorn logger. To read more on the topic please see Python [logging configuration](https://docs.python.org/3/library/logging.config.html).


Next, we will create a custom UvicornWorker class that will set **log\_config** to the path of our logging.yaml file, to pass the logging configuration that we’ve just made to Uvicorn. 🦄


I added the following code in main.py:


```
class MyUvicornWorker(UvicornWorker):
    CONFIG_KWARGS = {
        "log_config": "/mnt/c/Users/denis/PycharmProjects/FastAPILogging/logging.yaml",
    }
```


▶ If we run the application with:


```
gunicorn -k main.MyUvicornWorker main:create_app
```


We should see the Uvicorn access logs printed in the console 🦄


```
(venv2) ➜  FastAPILogging gunicorn -k main.MyUvicornWorker main:create_app
[2021-05-17 22:31:28 +0300] [6278] [INFO] Starting gunicorn 20.1.0
[2021-05-17 22:31:28 +0300] [6278] [INFO] Listening at: http://127.0.0.1:8000 (6278)
[2021-05-17 22:31:28 +0300] [6278] [INFO] Using worker: main.MyUvicornWorker
[2021-05-17 22:31:28 +0300] [6280] [INFO] Booting worker with pid: 6280
2021-05-17 22:31:28,185 - WARNING - ASGI app factory detected. Using it, but please consider setting the --factory flag explicitly.
2021-05-17 22:31:28,185 - INFO - Started server process [6280]
2021-05-17 22:31:28,185 - INFO - Waiting for application startup.
2021-05-17 22:31:28,185 - INFO - Application startup complete.
2021-05-17 22:31:30,129 - INFO - 127.0.0.1:54004 - "GET / HTTP/1.1" 200
```


Thanks for reading! 📚


PS: If you want to support this blog, I’ve made a[ Udemy course on FastAPI](https://www.udemy.com/course/build-a-movie-tracking-api-with-fastapi-and-python/?referralCode=47430716975684E04F85). Purchasing it through my referral link will give me 96% of the sale amount. Thanks!


### requirements.txt


```
click==7.1.2
fastapi==0.65.1
gunicorn==20.1.0
h11==0.12.0
httptools==0.2.0
pydantic==1.8.2
PyYAML==5.4.1
starlette==0.14.2
typing-extensions==3.10.0.0
uvicorn==0.13.4
uvloop==0.15.2
```