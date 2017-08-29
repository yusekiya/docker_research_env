FROM yusekiya/research-python
RUN apt-get update && apt-get install -y build-essential cmake pkg-config &&\
    apt-get clean &&\
    conda install -y --quiet -c conda-forge pybind11 &&\
