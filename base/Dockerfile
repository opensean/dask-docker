FROM continuumio/miniconda3:4.6.14

RUN conda install --yes --freeze-installed \
    -c conda-forge \
    python-blosc \
    cytoolz \
    dask==1.2.0 \
    nomkl \
    numpy==1.16.2 \
    pandas==0.24.2 \
    tini==0.18.0 \
    && conda install --yes --freeze-installed -c bioconda flash \
    && pip install s3fs biopython dask-ml sh --upgrade \
    && conda clean -tipsy \
    && find /opt/conda/ -type f,l -name '*.a' -delete \
    && find /opt/conda/ -type f,l -name '*.pyc' -delete \
    && find /opt/conda/ -type f,l -name '*.js.map' -delete \
    && find /opt/conda/lib/python*/site-packages/bokeh/server/static -type f,l -name '*.js' -not -name '*.min.js' -delete \
    && rm -rf /opt/conda/pkgs \
    ## igblast
    && wget -O /opt/ncbi-igblast-1.14.0-x64-linux.tar.gz ftp://ftp.ncbi.nih.gov/blast/executables/igblast/release/LATEST/ncbi-igblast-1.14.0-x64-linux.tar.gz \
    && tar -C /opt/ -xvzf /opt/ncbi-igblast-1.14.0-x64-linux.tar.gz \
    && mkdir /opt/ncbi-igblast-1.14.0/databases 

ADD ./databases /opt/ncbi-igblast-1.14.0/databases

COPY prepare.sh /usr/bin/prepare.sh

RUN mkdir /opt/app

ENTRYPOINT ["tini", "-g", "--", "/usr/bin/prepare.sh"]
