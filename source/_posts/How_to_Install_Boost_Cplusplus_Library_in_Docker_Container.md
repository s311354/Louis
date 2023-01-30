---
title: How to Install Boost C++ Library and Run CI/CD Workflow with Docker Container
date: 2023-01-31
categories:
- louissrliu
- features
tags:
- programming
- cpp
- docker
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/river_bridge.jpeg
---

"Docker is an open platform for developing, shipping, and running applications... A continer is a sandboxed process on our machine that is isolated from all other porcesses on the host machine...."

<!-- more -->

## Introduction ##

Docker has worked to make the capabilities of kernal namespaces and cgroups, features approachable and easy to use. Because it can be run on local machines, virtual machines or deployed to the cloud, and also builds an image which is based on the ubuntu image, but installs the Apache web server and our program or application, as well as the configuration details needed to make the program or application run.

In this post, I would like to go through how to create a container image with custom filesystem and boost c++ library shortly. In addition, running simple examples using [Boost.Regex][regex] in docker container, and setting up on continuous integration (CI) with docker.

## Docker Overview ##

<figure><center><img src="{{ site.baseurl }}/picture/docker.png" width="30%"></center></figure>

{% blockquote %}
Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers. The Docker client and daemon can run on the same system, or we can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface. Another Docker client is Docker Compose, that lets we work with applications consisting of a set of containers.
{% endblockquote %}

In order to build the container images, we can use certified DOCKER OFFICIAL IMAGE from [Docker Hub][dockerlib] or some third-party repositories. For example, we would use the base [Ubuntu 16.04][1604] image and build the image locally with following command.

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 100%">$ docker build -f Dockerfile -t ubuntu16.04:xenial
</span></code></pre></div>

Sometimes, we want to check all the images created on the local machine by following command.

    REPOSITORY              TAG                 IMAGE ID
    ubuntu16.04             latest              025cee1ce57d
    ubuntu16.04             xenial              025cee1ce57d
    ...

### Build Docker Image with Boost Library ###

We will use the other method to build the Ubuntu 16.04 LTS (Xenial Xerus) image for Boost C++ libraries. The relevant Dockerfile and the Boost-CMake C++ examples are available on [GitHub Gist][gist]. We can build the following Dockerfile on x86-64 platform.

{% codeblock %}
FROM ubuntu:16.04

MAINTAINER Louis Liu <s041978@hotmail.com>

WORKDIR /mnt/

ARG DEBIAN_FRONTEND=noninteractive

ARG CMAKE_VERSION=3.25.1

# Install package dependencies
RUN apt-get update &&\
    apt-get install -y --no-install-recommends software-properties-common build-essential \
            autoconf libtool pkg-config ca-certificates libssl-dev pkg-config \
            git wget vim cppman \
            automake g++ gcc \
            gdb valgrind && \
    apt-get clean

# We pass the boost version as argument
ARG BOOST_VERSION
ARG NUM_JOBS

ENV NUM_JOBS=${NUM_JOBS}

# Install CMake
RUN cd /tmp && \
    wget https://aus01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2FKitware%2FCMake%2Freleases%2Fdownload%2Fv%24&data=05%7C01%7C%7C0dca2689d18b42771ee808db012a764e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638105054127172004%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=Ba4bIIOdgEf5LVWjXRXsE%2BgAQeTF6MQnzTlmXS%2F4xxU%3D&reserved=0{CMAKE_VERSION}/cmake-${CMAKE_VERSION}.tar.gz && \
    tar xzf cmake-${CMAKE_VERSION}.tar.gz && \
    cd cmake-${CMAKE_VERSION} && \
    ./bootstrap && \
    make -j${NUM_JOBS} && \
    make install && \
    rm -rf /tmp/*

ENV BOOST_VERSION=${BOOST_VERSION}
ENV BOOST_ROOT=/usr/include/boost

# Install Boost
RUN cd /tmp && \
    BOOST_VERSION_MOD=$(echo $BOOST_VERSION | tr . _) && \
    wget https://aus01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fboostorg.jfrog.io%2Fartifactory%2Fmain%2Frelease%2F%24&data=05%7C01%7C%7C0dca2689d18b42771ee808db012a764e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638105054127172004%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=VWEW0PG7CmnLkqMMf%2FX3jXkKX2G4Jb8TvZqg8C8njD8%3D&reserved=0{BOOST_VERSION}/source/boost_${BOOST_VERSION_MOD}.tar.bz2 && \
    tar --bzip2 -xf boost_${BOOST_VERSION_MOD}.tar.bz2 && \
    cd boost_${BOOST_VERSION_MOD} && \
    ./bootstrap.sh --prefix=/usr/local && \
    ./b2 --prefix=/usr/local install && \
    rm -rf /tmp/*

RUN echo ${BOOST_ROOT}
ENTRYPOINT /bin/bash
{% endcodeblock %}

To build the Boost Docker image locally, we can run the following command. In this case, we will build Boost C++ library 1.80.0.

    $ BOOST_VERSION=1.80.0
    $ NUM_JOBS=4
    $ docker build . --build-arg BOOST_VERSION=${BOOST_VERSION} --build-arg NUM_JOBS=${NUM_JOBS} --tag=louissrliu/boost:${BOOST_VERSION}

After we build Docker images from above Dockerfile, we can also mount a file or directory on the local machine into a container. The file or directory is referenced by its absolute path on the local (host) machine. By contrast, when we use a volume, a new directory is created within Docker's storage directory on the local (host) machine, and Docker manages that directory's contents.

<figure><center><img src="{{ site.baseurl }}/picture/types-of-mounts-bind.png" width="20%"></center></figure>

We can run the following command to specify the image, mount the directory, allocate a tty for the container process and interactive processes with the Docker container.

    $ docker run -it -v $(pwd)/examples:/mnt louissrliu/boost:${BOOST_VERSION} bash

In addition, we can push/pull the above to a registry with following commands.

    $ docker push louissrliu/boost:${BOOST_VERSION}
    $ docker pull louissrliu/boost:${BOOST_VERSION}

<figure><center><img src="{{ site.baseurl }}/picture/dockerhub.png" width="30%"></center></figure>

### Boost Examples ###

To show that the Boost C++ library we have built works inside the Docker container, we will build and run a example using [Boost.Regex][regex], and another C++ example in docker container, available on [Boost-Docker repo][github], while allocating a tty for the container process and interactive processes. We we can run the following commands.

    $ cmake -B build
    $ cmake --build build --config Release --parallel

Then, executting these two examples, which simply print log outputs as shown below

    $ # extractemail example
    $ ./build/extractemail/ExtractEmail  < extractemail/data/tangled.txt 
    German fairy tale

    $ # arraynexting example
    $ echo 1 2 3 4 |  ./build/arraynexting/ArrayNexting
    The longest length of a set s[k] = {nums[k], nums[nums[k]], nums[nums[nums[k]]], ... } is : 1

### Continuous Integration/Continuous Delivery (CI/CD) Workflow with Docker ###

Continuous integration (CI) is the part of the developement procee where we're looking to get our code changes merged with the main branch of this boost docker build. GitHub Actions allows us to automate our build, test, and deployment pipeline. Here we create the following yaml file and placed it in [Boost-Docker repo][github] to get started using Docker for building this image in CI.

```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:

  build:

    runs-on: ubuntu-latest
      
    steps:
    - name: Checkout
      uses: actions/checkout@v3
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2

    - name: Build and push
      uses: docker/build-push-action@v3
      with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}
          build-args: BOOST_VERSION=1.80.0,NUM_JOBS=4
```


## Reference ##

+ [Docker docs](https://docs.docker.com/get-started)
+ [Boost C++ library](https://www.boost.org)
+ [Introduction to GitHub Actions](https://docs.docker.com/build/ci/github-actions/)
+ [Continuous integration with Docker](https://docs.docker.com/build/ci/)
+ [Publishing Docker Images](https://docs.github.com/en/actions/publishing-packages/publishing-docker-images)

[regex]:https://www.boost.org/doc/libs/1_81_0/libs/regex/doc/html/index.html "https://www.boost.org/doc/libs/1_81_0/libs/regex/doc/html/index.html"

[lexical_cast]:https://www.boost.org/doc/libs/1_41_0/libs/conversion/lexical_cast.htm "https://www.boost.org/doc/libs/1_41_0/libs/conversion/lexical_cast.htm"

[dockerlib]:https://hub.docker.com "https://hub.docker.com"

[1604]:https://git.launchpad.net/cloud-images/+oci/ubuntu-base/tree/Dockerfile?h=refs/tags/dist-xenial-amd64-20210804&id=45e83e2e11f641ba2fea381e705ededbd2778f16 "https://git.launchpad.net/cloud-images/+oci/ubuntu-base/tree/Dockerfile?h=refs/tags/dist-xenial-amd64-20210804&id=45e83e2e11f641ba2fea381e705ededbd2778f16"

[lambda]:https://www.boost.org/doc/libs/1_62_0/doc/html/lambda.html "https://www.boost.org/doc/libs/1_62_0/doc/html/lambda.html"

[gist]:https://gist.github.com/s311354/29c09cecf258d1435574b0148c107d18 "https://gist.github.com/s311354/29c09cecf258d1435574b0148c107d18"

[github]:https://github.com/s311354/boost-docker "https://github.com/s311354/boost-docker"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:shirong0419@icloud.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
