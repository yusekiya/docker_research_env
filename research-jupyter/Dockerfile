FROM continuumio/miniconda3:4.5.11
RUN echo deb http://deb.debian.org/debian stretch contrib non-free >> /etc/apt/sources.list &&\
    apt-get update && apt-get install -y fonts-freefont-ttf fonts-firacode ffmpeg &&\
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
# Install julia
ENV JULIA_PKGDIR=/opt/julia
ENV JULIA_VERSION=1.1.0
RUN mkdir /opt/julia-${JULIA_VERSION} && \
    cd /tmp && \
    curl -fL -o julia-${JULIA_VERSION}-linux-x86_64.tar.gz https://julialang-s3.julialang.org/bin/linux/x64/`echo ${JULIA_VERSION} | cut -d. -f 1,2`/julia-${JULIA_VERSION}-linux-x86_64.tar.gz && \
    echo "80cfd013e526b5145ec3254920afd89bb459f1db7a2a3f21849125af20c05471 *julia-${JULIA_VERSION}-linux-x86_64.tar.gz" | sha256sum -c - && \
    tar xzf julia-${JULIA_VERSION}-linux-x86_64.tar.gz -C /opt/julia-${JULIA_VERSION} --strip-components=1 && \
    rm /tmp/julia-${JULIA_VERSION}-linux-x86_64.tar.gz && \
    ln -fs /opt/julia-*/bin/julia /usr/local/bin/julia
# Show Julia where conda libraries are \
RUN mkdir /etc/julia && \
    echo "push!(Libdl.DL_LOAD_PATH, \"/opt/conda/lib\")" >> /etc/julia/juliarc.jl && \
    # Create JULIA_PKGDIR \
    mkdir $JULIA_PKGDIR
RUN julia -e 'using Pkg; Pkg.add("IJulia"); Pkg.add("PyCall"); using IJulia' && \
    pip install julia==0.2.*
# Configure jupyter
RUN mkdir -p /project/notebook
COPY jupyter_notebook_config.py /opt/conda/etc/jupyter/
COPY user-settings /root/.jupyter/lab/user-settings/
WORKDIR "/project"
CMD ["/opt/conda/bin/jupyter", "lab", "--allow-root"]
EXPOSE 8888
