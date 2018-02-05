# TensorFlow

To build the default TensorFlow image without GPU support, execute
```
docker build -t <tag> .
```

## GPU-enabled build
To enable GPU support, set the `BASE_IMAGE` build argument to any CUDA-enabled image that is known to work with the latest version of TensorFlow, e.g.
```
docker build \
  --build-arg BASE_IMAGE="nvidia/cuda:9.0-cudnn7-devel" \
  -t <tag> .
```

As a prerequisite for a smooth and streamlined experience, install [nvidia-docker2][nvidia-docker], and set it as the default container runtime by adding/changing the `default-runtime` key in `/etc/docker/daemon.json`
```
{
  "default-runtime": "nvidia"
  ...
}
```

<!--- References --->
[nvidia-docker]: https://github.com/NVIDIA/nvidia-docker/
