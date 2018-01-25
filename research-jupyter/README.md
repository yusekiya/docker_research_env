# Research environment with python on docker

This image includes

- python (jupyter notebook, Anaconda packages, psycopg2)


## Build image

If you want to build image locally, issue the following command

``` bash
$ docker build -t "yusekiya/research-jupyter:<tag>" .
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

``` yaml
version: '3'
services:
  jupyter:
    image: yusekiya/research-python:<tag>
    volumes:
      - './src:/project/src'
      - './src/lib:/project/notebook/lib'
      - './img:/project/img'
      - './notebook:/project/notebook'
      - './data:/project/data'
    ports:
      - '8888'
    env_file:
      - './jupyter.env'
```

Make sure to append `<tag>` to the image in order to improve
the reproducibility of research.
