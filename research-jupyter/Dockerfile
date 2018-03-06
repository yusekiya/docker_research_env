FROM continuumio/miniconda3:4.4.10
RUN echo deb http://ftp.uk.debian.org/debian jessie-backports main >> /etc/apt/sources.list &&\
    apt-get update && apt-get install -y fonts-freefont-ttf ffmpeg &&\
    apt-get clean
RUN conda config --add channels conda-canary && \
    conda update -yq conda && \
    conda config --system --add pinned_packages conda-canary::conda && \
    conda install -yq \
    python=3.6.4 \
    blaze=0.11.* \
    bokeh=0.12.* \
    bottleneck=1.2.* \
    cython=0.27.* \
    dask=0.16.* \
    dill=0.2.7.* \
    numpy=1.14.* \
    numpydoc=0.7.* \
    numexpr=2.6.* \
    scipy=1.0.0 \
    pandas=0.22.* \
    matplotlib=2.1.* \
    numba=0.36.* \
    jupyter=1.0.* \
    notebook=5.4.* \
    pathlib2=2.3.* \
    pytest=3.3.* \
    python-dateutil \
    pytz \
    pyyaml \
    sqlalchemy=1.2.* \
    psycopg2=2.7.3.* \
    sqlite=3.22.* \
# Install jupyterlab
    conda-forge::nodejs \
    conda-forge::ipywidgets \
    conda-forge::jupyterlab && \
# Cleanup
    conda clean -tipsy
# Install jupyterlab extentions
RUN jupyter labextension install -y --clean @jupyter-widgets/jupyterlab-manager
RUN mkdir -p /project/notebook
COPY jupyter_notebook_config.py /opt/conda/etc/jupyter/
WORKDIR "/project"
CMD ["/opt/conda/bin/jupyter", "notebook", "--allow-root"]
EXPOSE 8888
