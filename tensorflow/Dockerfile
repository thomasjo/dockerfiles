FROM nvidia/cuda:8.0-cudnn6-devel

LABEL maintainer="Thomas Johansen <thomasjo@gmail.com>"

SHELL ["bash", "-c"]

COPY build /tmp/build

WORKDIR /tmp

RUN echo "quiet \"2\";" | tee /etc/apt/apt.conf.d/_unattended

RUN apt-get update && \
    apt-get install \
      curl \
      python \
      software-properties-common \
      && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    apt-get install \
      python3.6 \
      python3.6-dev \
      && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN curl https://bootstrap.pypa.io/get-pip.py | python3.6

RUN echo "deb http://storage.googleapis.com/bazel-apt stable jdk1.8" | \
    tee /etc/apt/sources.list.d/bazel.list && \
    curl https://bazel.build/bazel-release.pub.gpg | apt-key add - && \
    apt-get update && \
    apt-get install \
      bazel \
      openjdk-8-jdk \
      && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
    apt-get install \
      autoconf \
      automake \
      libtool \
      unzip \
      && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN curl -L https://github.com/google/protobuf/archive/v3.4.0.tar.gz | tar -xz && \
    cd protobuf-3.4.0 && \
    ./autogen.sh && \
    ./configure && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    cd python && \
    python3.6 setup.py install --cpp_implementation

RUN pip3.6 install --no-cache-dir \
      numpy

RUN curl -L https://github.com/tensorflow/tensorflow/archive/v1.3.0.tar.gz | tar -xz && \
    cd tensorflow-1.3.0 && \
    source /tmp/build/tensorflow-env && \
    ./configure && \
    bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package && \
    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg && \
    pip3.6 install /tmp/tensorflow_pkg/tensorflow-1.3.0-cp36-cp36m-linux_x86_64.whl

RUN apt-get purge --autoremove \
      autoconf \
      automake \
      bazel \
      curl \
      libtool \
      openjdk-8-jdk \
      python \
      software-properties-common \
      unzip \
      && \
    rm -rf /etc/apt/sources.list.d/*

RUN rm /etc/apt/apt.conf.d/_unattended
RUN rm -rf /tmp/*

WORKDIR /
