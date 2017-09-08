# TensorFlow

To build the default TensorFlow image without GPU-support, run something like
```
docker build -t <tag> .
```

To enable GPU-support, set the `BASE_IMAGE` build argument to any CUDA image
that is known to work with the latest version of TensorFlow; for example
```
docker build \
  --build-arg BASE_IMAGE="nvidia/cuda:8.0-cudnn6-devel" \
  -t <tag> .
```
