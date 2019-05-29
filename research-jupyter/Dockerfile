FROM continuumio/miniconda3:4.5.11

RUN echo deb http://deb.debian.org/debian stretch contrib non-free >> /etc/apt/sources.list &&\
    apt-get update && apt-get install -y fonts-freefont-ttf fonts-firacode ffmpeg hdf5-tools &&\
    apt-get clean

RUN conda config --add channels conda-canary && \
    conda update -yq conda && \
    conda config --system --add pinned_packages conda-canary::conda && \
    conda install -yq \
    python=3.7.* \
    blaze=0.11.* \
    bokeh=1.1.* \
    bottleneck=1.2.* \
    cython=0.29.* \
    dask=1.2.* \
    dill=0.2.* \
    joblib=0.13.* \
    pyviz::holoviews=1.12.* \
    numpy=1.16.* \
    numpydoc=0.9.* \
    numexpr=2.6.* \
    scipy=1.2.0 \
    pandas=0.24.* \
    matplotlib=3.0.* \
    mkl-service=1.1.* \
    numba=0.43.* \
    jupyter=1.0.* \
    notebook=5.7.* \
    pathlib2=2.3.* \
    pytest=4.0.* \
    pytest-mock=1.10.* \
    python-dateutil=2.7.* \
    pytz=2018.7 \
    pyyaml=3.13 \
    sqlalchemy=1.3.* \
    psycopg2=2.7.* \
    sqlite=3.26.* \
    scikit-learn=0.20.* \
# Install jupyterlab
    conda-forge::nodejs=9.9.* \
    conda-forge::ipywidgets=7.4.* \
    conda-forge::jupyterlab=0.35.* && \
# Cleanup
    conda clean -tipsy

# Install jupyterlab extentions
RUN jupyter labextension install -y --clean @jupyter-widgets/jupyterlab-manager@0.38 jupyterlab_bokeh@0.6.3 @pyviz/jupyterlab_pyviz@0.7.2

# install Julia packages in /opt/julia instead of $HOME
ENV JULIA_DEPOT_PATH=/opt/julia
ENV JULIA_PKGDIR=/opt/julia
ENV JULIA_VERSION=1.1.1
ENV CONDA_DIR=/opt/conda

RUN mkdir /opt/julia-${JULIA_VERSION} && \
    cd /tmp && \
    wget -q https://julialang-s3.julialang.org/bin/linux/x64/`echo ${JULIA_VERSION} | cut -d. -f 1,2`/julia-${JULIA_VERSION}-linux-x86_64.tar.gz && \
    echo "f0a83a139a89a2ccf2316814e5ee1c0c809fca02cbaf4baf3c1fd8eb71594f06 *julia-${JULIA_VERSION}-linux-x86_64.tar.gz" | sha256sum -c - && \
    tar xzf julia-${JULIA_VERSION}-linux-x86_64.tar.gz -C /opt/julia-${JULIA_VERSION} --strip-components=1 && \
    rm /tmp/julia-${JULIA_VERSION}-linux-x86_64.tar.gz
RUN ln -fs /opt/julia-*/bin/julia /usr/local/bin/julia

# Show Julia where conda libraries are \
RUN mkdir /etc/julia && \
    echo "push!(Libdl.DL_LOAD_PATH, \"$CONDA_DIR/lib\")" >> /etc/julia/juliarc.jl && \
    # Create JULIA_PKGDIR \
    mkdir $JULIA_PKGDIR

RUN julia -e 'import Pkg; Pkg.update()' && \
    (test $TEST_ONLY_BUILD || julia -e 'import Pkg; Pkg.add("HDF5")') && \
    julia -e "using Pkg; pkg\"add Gadfly RDatasets IJulia Plots GR BenchmarkTools InstantiateFromURL\"; pkg\"precompile\"" && \ 
    # install library for python interface
    julia -e "ENV[\"PYTHON\"]=ENV[\"CONDA_DIR\"]*\"/bin/python\"; using Pkg; pkg\"add PyCall PyPlot\"; pkg\"precompile\""

# Configure jupyter
RUN mkdir -p /project/notebook
COPY jupyter_notebook_config.py /opt/conda/etc/jupyter/
COPY user-settings /root/.jupyter/lab/user-settings/
WORKDIR "/project"
CMD ["/opt/conda/bin/jupyter", "lab", "--allow-root"]
EXPOSE 8888
