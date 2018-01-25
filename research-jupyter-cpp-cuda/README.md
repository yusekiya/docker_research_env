# Research environment with python and cpp on docker

This image includes

- python (jupyter notebook, Anaconda packages, psycopg2)
- c/c++ environment (gcc/g++, cmake, pybind11)
- CUDA

## Build image

If you want to build image locally, issue the following command

``` bash
$ docker build -t "yusekiya/research-jupyter-cpp-cuda:<tag>" .
```


## How to use

### Prepare env file

Create a file `jupyter.env` in the top directory of the project,
and define your password to login to notebook server in the file.
Each line of the env file is expected to be in `VAR=VAL` format.
Necessary variable in the env file is `NOTEBOOK_PASSWORD`.

``` ini
NOTEBOOK_PASSWORD=<your_notebook_password>
```

### Example of `docker-compose.yml`

Create volume for nvidia driver first.

``` shell
docker volume create --name=nvidia_driver_<version> -d nvidia-docker
```

``` yaml
version: '3'

volumes:
  nvidia_driver_<version>:
    external: true

services:
  jupyter:
    image: yusekiya/research-python-cpp-cuda:<tag>
    volumes:
      - './src:/project/src'
      - './src/lib:/project/notebook/lib'
      - './img:/project/img'
      - './notebook:/project/notebook'
      - './data:/project/data'
      - 'nvidia_driver_<version>:/usr/local/nvidia:ro'
    devices:
      - /dev/nvidiactl
      - /dev/nvidia-uvm
      - /dev/nvidia0
    ports:
      - '8888'
    env_file:
      - './jupyter.env'
```

Make sure to append `<tag>` to the image in order to improve
the reproducibility of research.
