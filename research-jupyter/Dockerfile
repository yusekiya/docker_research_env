FROM continuumio/miniconda3:4.4.10
RUN echo deb http://deb.debian.org/debian stretch contrib non-free >> /etc/apt/sources.list &&\
    apt-get update && apt-get install -y fonts-freefont-ttf fonts-firacode ffmpeg &&\
    apt-get clean
RUN conda config --add channels conda-canary && \
    conda update -yq conda && \
    conda config --system --add pinned_packages conda-canary::conda && \
    conda install -yq \
    python=3.6.* \
    blaze=0.11.* \
    bokeh=0.13.* \
    bottleneck=1.2.* \
    cython=0.28.* \
    dask=0.18.* \
    dill=0.2.7.* \
    joblib=0.12.* \
    pyviz::holoviews=1.10.* \
    numpy=1.14.* \
    numpydoc=0.8.* \
    numexpr=2.6.* \
    scipy=1.1.0 \
    pandas=0.23.* \
    matplotlib=2.2.* \
    mkl-service=1.1.* \
    numba=0.39.* \
    jupyter=1.0.* \
    notebook=5.4.* \
    pathlib2=2.3.* \
    pytest=3.6.* \
    pytest-mock=1.10.* \
    python-dateutil=2.7.* \
    pytz=2018.3 \
    pyyaml=3.12 \
    sqlalchemy=1.2.* \
    psycopg2=2.7.3.* \
    sqlite=3.22.* \
    scikit-learn=0.19.* \
# Install jupyterlab
    conda-forge::nodejs=9.9.* \
    conda-forge::ipywidgets=7.2.* \
    conda-forge::jupyterlab=0.34.* && \
# Cleanup
    conda clean -tipsy
# Install jupyterlab extentions
RUN jupyter labextension install -y --clean @jupyter-widgets/jupyterlab-manager@0.37 jupyterlab_bokeh@0.6 @pyviz/jupyterlab_pyviz
# Install julia
ENV JULIA_PATH /opt/julia
ENV PATH $JULIA_PATH/bin:$PATH
RUN curl -fL -o julia.tar.gz https://julialang-s3.julialang.org/bin/linux/x64/1.0/julia-1.0.0-linux-x86_64.tar.gz && \
    mkdir "$JULIA_PATH" && \
    tar -xzf julia.tar.gz -C "$JULIA_PATH" --strip-components 1 && \
    rm julia.tar.gz
RUN julia -e 'using Pkg; Pkg.add("IJulia"); Pkg.add("PyCall"); using IJulia' && \
    pip install julia 
# Configure jupyter
RUN mkdir -p /project/notebook
COPY jupyter_notebook_config.py /opt/conda/etc/jupyter/
COPY user-settings /root/.jupyter/lab/user-settings/
WORKDIR "/project"
CMD ["/opt/conda/bin/jupyter", "lab", "--allow-root"]
EXPOSE 8888
