# https://github.com/kubeflow/kubeflow/blob/master/components/tensorflow-notebook-image/Dockerfile

#FROM jupyter/datascience-notebook
#FROM gcr.io/kubeflow-images-public/tensorflow-2.1.0-notebook-gpu:1.0.0
FROM gcr.io/kubeflow-images-public/tensorflow-2.1.0-notebook-cpu:1.0.0
USER root

#### INSTALL CONDA (root)
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion

RUN wget --quiet https://repo.anaconda.com/archive/Anaconda3-5.3.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

RUN conda update -n root conda && conda update --all
### end conda

RUN conda update jupyterlab

# Geomatics
RUN conda install -c conda-forge rasterio gdal 
RUN conda install -c conda-forge geopandas 
RUN conda install -c conda-forge pytorch
#RUN conda install -c conda-forge tensorflow

# XGBoost
RUN conda install -c conda-forge xgboost

# Extensions
RUN conda install -c conda-forge jupyter_contrib_nbextensions
RUN jupyter nbextension enable codefolding/main

# Language Server
#RUN jupyter labextension install @krassowski/jupyterlab-lsp 

# IPyWidgets
RUN conda install -c conda-forge ipywidgets
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager

# Dash
RUN conda install -c conda-forge jupyterlab-dash

# Variable Inspector
RUN jupyter labextension install @lckr/jupyterlab_variableinspector

# Tensorboard
RUN pip install -U jupyter-tensorboard
RUN jupyter labextension install jupyterlab_tensorboard


# Matplotlib integration
RUN conda install -c conda-forge ipympl nodejs
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager jupyter-matplotlib

# Plotly
RUN jupyter labextension install jupyterlab-plotly

# Spellcheck
RUN jupyter labextension install @ijmbarr/jupyterlab_spellchecker

# Jump to def
#RUN jupyter labextension install @krassowski/jupyterlab_go_to_definition

# Table of Contents
RUN jupyter labextension install @jupyterlab/toc

# Geojson
RUN jupyter labextension install @jupyterlab/geojson-extension

# Google Drive
RUN jupyter labextension install @jupyterlab/google-drive

# Comments
RUN pip install jupyterlab-commenting-service
RUN jupyter labextension install @jupyterlab/commenting-extension

# Natural Language Processing
RUN conda install -c anaconda nltk 

### R STUFF
COPY install2.r /bin/
RUN chmod +x /bin/install2.r

ARG R_VERSION=3.6.3
ARG OS_IDENTIFIER=ubuntu-1804

# Install R
ENV R_LIBS_USER=/opt/R/3.6.3/lib/ 
ENV R_LIBS=/opt/R/3.6.3/lib/ 

RUN wget https://cdn.rstudio.com/r/${OS_IDENTIFIER}/pkgs/r-${R_VERSION}_1_amd64.deb && \
    apt-get update -qq && \
    DEBIAN_FRONTEND=noninteractive apt-get install -f -y ./r-${R_VERSION}_1_amd64.deb && \
    ln -s /opt/R/${R_VERSION}/bin/R /usr/bin/R && \
    ln -s /opt/R/${R_VERSION}/bin/Rscript /usr/bin/Rscript && \
    ln -s /opt/R/${R_VERSION}/lib/R /usr/lib/R && \
    rm r-${R_VERSION}_1_amd64.deb && \
    rm -rf /var/lib/apt/lists/*

RUN /usr/bin/Rscript -e 'install.packages("docopt",  repos="http://cran.rstudio.com")'

## tidyverse
#ENV R_LIBS_USER=/opt/R/3.6.3/lib/ 
#ENV R_LIBS=/opt/R/3.6.3/lib/ 
#RUN install2.r --error \
#    --libloc $R_LIBS \
#    --repos "http://cran.rstudio.com" \
#    --deps TRUE \
#    tidyverse \
#    dplyr \
#    devtools \
#    formatR \
#    remotes \
#    selectr \
#    caTools \
#    BiocManager

# geospatial
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    lbzip2 \
    libfftw3-dev \
    libgdal-dev \
    libgeos-dev \
    libgsl0-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    libhdf4-alt-dev \
    libhdf5-dev \
    libjq-dev \
    liblwgeom-dev \
    libpq-dev \
    libproj-dev \
    libprotobuf-dev \
    libnetcdf-dev \
    libsqlite3-dev \
    libssl-dev \
    libudunits2-dev \
    netcdf-bin \
    postgis \
    protobuf-compiler \
    sqlite3 \
    tk-dev \
    unixodbc-dev

RUN install2.r --error \
    --libloc $R_LIBS \
    --repos "http://cran.rstudio.com" \
    RColorBrewer \
    RandomFields \
    # RNetCDF \
    classInt \
    deldir \
    gstat \
    #hdf5r \
    #lidR \
    mapdata \
    maptools \
    # mapview \
    ncdf4 \
    proj4 \
    raster \
    #rgdal \
    rgeos \
    rlas \
    # sf \
    sp \
    spacetime \
    spatstat \
    #spdep \
    geoR \
    geosphere 
    ## from bioconductor

RUN install2.r --error \
    --libloc $R_LIBS \
    --repos "http://cran.rstudio.com" \
    IRkernel

# Register R Kernel
RUN Rscript -e 'IRkernel::installspec()'

# Grab dplyr
RUN Rscript -e 'install.packages("dplyr", repo="http://cran.rstudio.com")'

# Grab rgdal
# RUN ACCEPT_USE_OF_DEPRECATED_PROJ_API_H=1 Rscript -e 'install.packages("rgdal", repo="http://cran.rstudio.com")'

# Oops, I missed Keras
RUN conda install -c conda-forge keras



RUN chown -R jovyan:users /home/jovyan
USER jovyan
CMD ["sh","-c", "jupyter notebook --notebook-dir=/tf --ip=0.0.0.0 --no-browser --allow-root --port=8888 --NotebookApp.token='blair' --NotebookApp.password='badhorse' --NotebookApp.default_url='/lab' --NotebookApp.allow_origin='*' --NotebookApp.base_url=${NB_PREFIX}"]
