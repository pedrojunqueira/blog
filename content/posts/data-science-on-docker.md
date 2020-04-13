---
title: "How to get a PySpark environment running on Jupyter"
date: 2020-04-13T21:35:16+09:30
draft: false
toc: false
images:
tags:
  - docker
  - jupyter
  - python
  - spark
  - pyspark
---

Python is a magical language. It is just import from * and then the magic start happening...

However, not always all run smoothly. Sometimes all that you want is to run code but as part of coding debugging is a constant battle until you get your code running as you wish.

Besides the debugging, you need to set up your development environment that can be very very tedious. Sometime you just want to code and get your job done and you may spend hours and hours just setting up the environnement, importing packages, creating VMs, exporting environment variables, running shell scrips, going 10x to stack overflow and still struggle to make things work fine, then finally you can start writing some code. It can be very frustrating. 

It would be nice if there is a way to just press a button and have everything up and running.

Well there is a thing and it is called Docker.

If you don't know what it is in 2020 you may be hiding under a rock.

The other day I did a course on PySpark on [Udemy](https://www.udemy.com/course/spark-and-python-for-big-data-with-pyspark/learn/lecture/6666744#overview) and I watched one of the 4 lectures 20 to 40 min each teaching how to set up Spark on your local machine. Ok you can pick one of the options but it is a long process of like 20 to 40 steps and if you want to do it along the instructor it will take over 1 hour.

I had started to play with Docker a few months ago and I though that someone somewhere made it easier on Docker. Bang!

Someone other than Jupyter did and here is the [docs](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html)

Then with just one line I got my notebook running on spark so I could cruise through the content of the course.

Given you already have Docker installed on your local machine the magic command is

``` bash
$ docker run --rm -p 8888:8888 -v "$PWD":/home/jovyan/work jupyter/pyspark-notebook
```
BANG ! You have it auto magically running on your machine on `http://<hostname>:8888/?token=<token>` and you have access to all files inside the directory you ran the above bash command.

Need a R notebook?

No problem just change `jupyter/pyspark-notebook` by `jupyter/r-notebook`

The data science Jupyter stack also have `jupyter/tensorflow-notebook` and more.

If you have not looked into [Docker](https://docs.docker.com/get-started/) I highly recommend. Not only for the Jupyter stack which is already very good on its own but also for other things like deploying web applications to production.

Before I go would recommend you to watch this [video](https://www.youtube.com/watch?v=eGz9DS-aIeY) from NetworkChuck then Docker may be a path of no return for you.

---

![Docker](https://www.docker.com/sites/default/files/social/docker_facebook_share.png)


