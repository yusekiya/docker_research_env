FROM yusekiya/research-python
RUN apt-get update && apt-get install -y build-essential cmake pkg-config unzip &&\
    apt-get clean &&\
    # Install fmtlib/fmt
    /usr/bin/curl -fsSL -o /tmp/fmt-4.0.0.zip https://github.com/fmtlib/fmt/releases/download/4.0.0/fmt-4.0.0.zip &&\
    /usr/bin/unzip -qq /tmp/fmt-4.0.0.zip -d /tmp &&\
    mv /tmp/fmt-4.0.0/fmt /usr/local/include/ &&\
    # Install pybind11
    conda install -y --quiet -c conda-forge pybind11 &&\
    # Clean up
    rm -rf /tmp/*
