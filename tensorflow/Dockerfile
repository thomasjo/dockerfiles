ARG   BASE_IMAGE="ubuntu:16.04"
FROM $BASE_IMAGE

LABEL maintainer="Thomas Johansen <thomasjo@gmail.com>"

COPY build /tmp/build
WORKDIR /tmp

SHELL ["bash", "-c"]

RUN apt-get update && apt-get install -y \
      ca-certificates \
      curl \
      software-properties-common \
      && \
    rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y python3 && \
    rm -rf /var/lib/apt/lists/* && \
    curl https://bootstrap.pypa.io/get-pip.py | python3

# Build dependencies for protobuf.
RUN apt-get update && apt-get install -y \
      autoconf \
      automake \
      build-essential \
      libtool \
      python3-dev \
      unzip \
      && \
    rm -rf /var/lib/apt/lists/*

RUN curl -L https://github.com/google/protobuf/archive/v3.4.0.tar.gz | tar -xz && \
    cd protobuf* && \
    ./autogen.sh && \
    ./configure && \
    make install -j$(nproc) && \
    cd python && \
    python3 setup.py install --cpp_implementation

# Build dependencies for TensorFlow.
RUN echo "deb http://storage.googleapis.com/bazel-apt stable jdk1.8" | \
    tee /etc/apt/sources.list.d/bazel.list && \
    curl https://bazel.build/bazel-release.pub.gpg | apt-key add - && \
    apt-get update && apt-get install -y \
      bazel \
      openjdk-8-jdk \
      python \
      && \
    rm -rf /var/lib/apt/lists/*

RUN curl -L https://github.com/tensorflow/tensorflow/archive/v1.3.0.tar.gz | tar -xz && \
    cd tensorflow* && \
    # HACK: Temporarily remove SHA256 checksums because reasons.
    # ¯\_(ツ)_/¯ https://github.com/tensorflow/tensorflow/issues/12979
    sed -ri "/^\W+sha256 = \"[^\"]+\"\W+$/d" tensorflow/workspace.bzl && \
    pip3 install --no-cache-dir numpy && \
    source /tmp/build/tensorflow-env && ./configure && \
    bazel build \
      --config=opt \
      # Build with CUDA support when using a CUDA base image.
      ${CUDA_VERSION:+--config=cuda} \
      tensorflow/tools/pip_package/build_pip_package && \
    bazel-bin/tensorflow/tools/pip_package/build_pip_package $PWD && \
    pip3 install "$(ls -1 tensorflow*.whl | head -n 1)"

# Ensure compatible combination of bleach and html5lib.
RUN pip3 install --no-cache-dir \
      bleach>=2.0 \
      html5lib>=0.99999999

RUN apt-get purge --autoremove -y \
      autoconf \
      automake \
      bazel \
      build-essential \
      ca-certificates \
      curl \
      libtool \
      openjdk-8-jdk \
      python \
      python3-dev \
      software-properties-common \
      unzip \
      && \
    rm -rf /etc/apt/sources.list.d/*

RUN rm -rf /tmp/*

WORKDIR /
