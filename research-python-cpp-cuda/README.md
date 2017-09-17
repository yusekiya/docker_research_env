# Research environment with python and cpp on docker

This image includes

- python (jupyter notebook, Anaconda packages, psycopg2)
- c/c++ environment (gcc/g++, cmake, pybind11)
- CUDA

## Build image

If you want to build image locally, issue the following command

``` bash
$ docker build -t "yusekiya/research-python-cpp-cuda:<tag>" .
```


## Setup environment without database

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


## Setup environment with database

### Prepare env files

Create the `jupyter.env` file described [above](#setup-environment-without-database).
In addition, create `db.env` file which defines `POSTGRES_PASSWORD`, `POSTGRES_USER`,
`POSTGRES_DB`, and `PGDATA`.

``` ini
POSTGRES_PASSWORD=<your_db_password>
POSTGRES_USER=<your_db_user_name>
POSTGRES_DB=<your_db_name>
# Database directory in container
PGDATA=/var/lib/postgresql/data/pgdata
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
      - './db.env'
    depends_on:
      - db
  db:
    image: 'postgres:9.6.2'
    volumes:
      - './db:/var/lib/postgresql/data'
    env_file:
      - './db.env'
    ports:
      - '5432'
  pgweb:
    image: 'sosedoff/pgweb'
    ports: 
      - '8081' 
    env_file:
      - './db.env'
    command: ['/usr/bin/pgweb', '--bind=0.0.0.0', '--listen=8081', '--ssl', 'disable']
    links:
      - 'db:db'
    depends_on:
      - db
```
