# TensorFlow

To build the default TensorFlow image without GPU support, execute
```
docker build -t <tag> .
```

## GPU-enabled build
To enable GPU support, set the `BASE_IMAGE` build argument to any CUDA-enabled image that is known to work with the latest version of TensorFlow, e.g.
```
docker build \
  --build-arg BASE_IMAGE="nvidia/cuda:8.0-cudnn6-devel" \
  -t <tag> .
```

Sadly, with recent versions of TensorFlow, we need to provide the container access to the NVIDIA driver infrastructure during build time. Since Docker does not support volume mounting during build phase, and does not follow symlinks, we are forced to work around the issue by relying on `mount --bind`. Therefore, before building a container image with GPU support, ensure you execute
```
mount --bind <nvidia-docker driver mountpoint> mnt/nvidia_driver
```

The path to the mountpoint can be found by executing
```
docker volume inspect -f "{{ .Mountpoint }}" \
  $(docker volume ls -qf driver=nvidia-docker | head -n1)
```
