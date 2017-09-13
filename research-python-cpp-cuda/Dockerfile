FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04

# Install requirements
RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion \
    curl grep sed dpkg build-essential cmake pkg-config unzip software-properties-common && \
    # Install tini
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && rm tini.deb && \
    apt-get clean

# Install packages for C++ env
WORKDIR "/tmp"
## Install firearray
RUN apt-get update --fix-missing && \
    apt-get install -y libfreeimage-dev libopenblas-dev libfftw3-dev liblapack-dev liblapacke-dev \
    libglfw3 libglfw3-dev libfontconfig1-dev \
    libboost-all-dev opencl-headers ocl-icd-opencl-dev clinfo  && \
    apt-get clean
RUN ln -s /usr/local/cuda/lib64/stubs/libcuda.so /usr/lib/libcuda.so.1 && \
    ln -s /usr/lib/libcuda.so.1 /usr/lib/libcuda.so && \
    mkdir -p /etc/OpenCL/vendors && \
    echo "libnvidia-opencl.so.1" > /etc/OpenCL/vendors/nvidia.icd && \
    echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && \
    echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf
ENV PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ARG COMPILE_GRAPHICS=OFF
RUN git clone --recursive https://github.com/arrayfire/arrayfire.git -b master && \
    cd arrayfire && mkdir build && cd build && \
    cmake .. -DCMAKE_BUILD_TYPE=Release \
             -DBUILD_CPU=ON \
             -DBUILD_CUDA=ON \
             -DBUILD_OPENCL=ON \
             -DBUILD_UNIFIED=ON \
             -DBUILD_GRAPHICS=${COMPILE_GRAPHICS} \
             -DBUILD_NONFREE=OFF \
             -DBUILD_EXAMPLES=ON \
             -DBUILD_TEST=ON \
             -DBUILD_DOCS=OFF \
             -DINSTALL_FORGE_DEV=ON \
             -DUSE_FREEIMAGE_STATIC=OFF && \
    make -j8 && make install && \
    echo "/usr/local/cuda/nvvm/lib64" >> /etc/ld.so.conf.d/arrayfire.conf && \
    ldconfig && \
    cd /tmp && rm -rf /tmp/*

## Install fmt and eigen3
RUN \
    # Install fmtlib/fmt
    /usr/bin/curl -fsSL -o /tmp/fmt-4.0.0.zip https://github.com/fmtlib/fmt/releases/download/4.0.0/fmt-4.0.0.zip && \
    cd /tmp && /usr/bin/unzip -qq fmt-4.0.0.zip && cd fmt-4.0.0 && \
    mkdir build && cd build && cmake ../ && make install && cd /tmp && \
    # Install Eigen3
    /usr/bin/curl -fsSL -o /tmp/eigen3.tar.gz2 http://bitbucket.org/eigen/eigen/get/3.3.4.tar.bz2 && \
    mkdir -p /tmp/eigen3/build && tar xf /tmp/eigen3.tar.gz2 -C /tmp/eigen3 --strip-components 1 && \
    cd /tmp/eigen3/build && cmake ../ && make install && cd /tmp &&\
    # Clean up
    rm -rf /tmp/*

# Install anaconda3
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8 CONDAVERSION=4.4.0
RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet https://repo.continuum.io/archive/Anaconda3-${CONDAVERSION}-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh
ENTRYPOINT [ "/usr/bin/tini", "--" ]

# Setup conda env and jupyter notebook
ENV PATH /opt/conda/bin:$PATH
RUN mkdir -p /project/notebook
COPY jupyter_notebook_config.py /opt/conda/etc/jupyter/

# Install packages to improve jupyter notebook
RUN add-apt-repository -y ppa:jonathonf/ffmpeg-3 && \
    apt-get update --fix-missing && apt-get install -y fonts-freefont-ttf ffmpeg && \
    apt-get clean

# Install conda packages
RUN conda install -yq jupyter psycopg2 && \
    pip install -q arrayfire && \
    # Install pybind11
    conda install -yq -c conda-forge pybind11=2.2.0 && \
    conda install -yq libgcc=5.2.0

ENV LD_LIBRARY_PATH /usr/local/lib:$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/nvvm/lib64

WORKDIR "/project"
CMD ["/opt/conda/bin/jupyter", "notebook", "--allow-root"]
EXPOSE 8888
