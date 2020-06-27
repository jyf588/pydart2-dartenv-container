# references:
# https://gitlab.com/nvidia/container-images/samples/blob/master/opengl/ubuntu16.04/glxgears/Dockerfile
# https://github.com/nvidia/nvidia-docker/wiki/Installation-(Native-GPU-Support)
# INSTALL sudo apt-get install -y nvidia-container-toolkit on your HOST machine first before using this dockerfile!

# running instructions:
# docker build -t openrave-ha:v0 .
# xhost +si:localuser:root
# docker run --gpus=all -ti --rm -e DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix openrave-ha:v0 /bin/bash
# inside contrainer, try running glxgears to test GUI forwarding

# FROM nvidia/opengl:1.0-glvnd-runtime-ubuntu18.04
FROM nvidia/cudagl:10.0-devel-ubuntu18.04

ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES},display

# essentials
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        mesa-utils \
        git \
        g++ \
        cmake \
        pkg-config \
        lsb-core \
        wget \
        mlocate \
	vim \
        openmpi-bin \
        && rm -rf /var/lib/apt/lists/*

# install Dart dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        libeigen3-dev \
        libassimp-dev \
        libccd-dev \
        libfcl-dev \
        libboost-regex-dev \
        libboost-system-dev \
        libopenscenegraph-dev \
        libbullet-dev \
        liburdfdom-dev \
        libnlopt-dev \
        libxi-dev \
        libxmu-dev \
        freeglut3-dev \
        libode-dev \
        libtinyxml2-dev \
        libblacs-mpi-dev \
        && rm -rf /var/lib/apt/lists/*

# python
# 18.04 comes with python3 by default
RUN apt-get update && apt-get install -y --no-install-recommends \
        software-properties-common
RUN add-apt-repository universe
RUN apt-get update && apt-get install -y --no-install-recommends \
        python3-dev \
        python3-pip \
        swig \
        python3-setuptools \
        python3-wheel \
        python3-pyqt5 \
        python3-pyqt5.qtopengl

WORKDIR /root
RUN git clone git://github.com/dartsim/dart.git
WORKDIR /root/dart
RUN git checkout tags/v6.3.0
COPY ./patches/lcp.cpp /root/dart/dart/external/odelcpsolver/lcp.cpp
WORKDIR /root/dart
RUN mkdir build
WORKDIR /root/dart/build
RUN cmake ..
RUN make -j$(nproc)
RUN make install
WORKDIR /root
ENV LD_LIBRARY_PATH="/usr/local/lib:${LD_LIBRARY_PATH}"

RUN pip3 install numpy==1.16
RUN pip3 install joblib
WORKDIR /root
RUN git clone git://github.com/sehoonha/pydart2.git
WORKDIR /root/pydart2
COPY ./patches/setup.py /root/pydart2/setup.py
COPY ./patches/pydart2_draw.cpp /root/pydart2/pydart2/pydart2_draw.cpp
WORKDIR /root/pydart2
RUN python3 setup.py build build_ext
RUN python3 setup.py develop
ENV PYTHONPATH="/root/pydart2"
WORKDIR /root



