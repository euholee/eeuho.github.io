---
layout: post
title: "How to install fast-api on ubuntu 18.04"
data: 2019-10-31
categories: fastapi
---

During installing fast-api, I stuck by some issues, and I fixed.
And I want to share my trouble shooting.

## 0. running python virtual environment(optional)

as you know, it isn't necessary step. i don't want to install stuffs on my entire
i prefer env

```
$ mkdir <your-project-folder>
$ cd <your-project-folder>

$ python -m venv <virtual-environment-name>
$ source <virtual-environment-name>/bin/activate
```

you might see virtual environment name in your shell like: ```(<virtual-environment-name> $)```


## 1. install requirements

```
$ sudo apt-get install python3-dev
$ pip install --upgrade setuptools
```

in my case, fast-api installation failed without the installation above.

and following [fast-api requirements](https://github.com/tiangolo/fastapi#requirements), we need to install starlette.

```
$ pip3 install starlette
$ pip3 install fast-api
```

and to run fast-api web server, we need asynchronus server gateway interface like uvicorn.

```
$ pip3 install uvicorn
```

## 2. test fast-api running

write a test source for testing.

```
# main.py

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

```

and run it.

```
$ uvicorn main:app --reload
```

now you can check hello world message at localhost:8000 on your browser.
