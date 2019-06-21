# Dask docker images

Docker images for dask-distributed.

1. Base image to use for dask scheduler and workers
2. Jupyter Notebook image to use as helper entrypoint

This images are built primarily for the [dask-distributed Helm Chart](https://github.com/kubernetes/charts/tree/master/stable/dask-distributed)
but they should work for more use cases.

## How to use / test

A helper docker-compose file is provided to test functionality.

```
docker-compose up
```

Open the notebook using the URL that is printed by the output so it has the token.

On a new notebook run:

```python
from dask.distributed import Client
client = Client('scheduler:8786')
client.ncores()
```

It should output something like this:

```
{'tcp://172.23.0.4:41269': 4}
```

## Building images

Docker compose provides an easy way to building all the images with the right context

```
docker-compose build

# Just build one image e.g. notebook
docker-compose build notebook
```


## Building IgBLAST enabled worker

Contributers: 

- https://bitbucket.org/kenneym/
- https://github.com/opensean/

The ```base/Dockerfile``` had been modified to retrieve and setup NCBI IgBLAST.
It is available in the ```/opt/ncbi-igblast-1.14.0``` directory.
Germiline databases must be retrieved from either NCBI  
(https://ftp.ncbi.nih.gov/blast/executables/igblast/release/database/) or 
IMGT (http://www.imgt.org/vquest/refseqh.html#VQUEST).  The following 
example describes the process for building the V, D, and J gene 
germline databases for rabbit from IMGT.

```
mkdir base/database/rabbit/imgt2019

cd base/database/rabbit/imgt2019

vim IGXV.fa

```

- Now, go to http://www.imgt.org/vquest/refseqh.html#VQUEST

- Scroll down to IMGT/V-QUEST reference directory sets per taxon:

- Click on IGHV under Orycctolagus cuniculus (rabbit)

- Copy all of the genetic information, starting at the first `>` sign

- Now, re-enter your vim session:
  
```"+p``` or, whatever way of pasting from clipboard you best prefer

- Feel free to add other V gene lines from rabbit, for instance, by copying IGLV as well.

- Place this imgt file into the correct format using the edit_imgt_file script:
  
```/opt/ncbi-igblast-1.14.0/bin/edit_imgt_file.pl IGXV.fa > IGXV```

- Create the database now, using:

```/opt/ncbi-igblast-1.14.0/bin/makeblastdb -parse_seqids -dbtype nucl -in IGXV```

- The output to stdout should look something like this:

    Building a new DB, current time: 06/21/2019 13:44:41
    New DB name:   dask-docker/base/databases/rabbit/imgt2019/IGXV
    New DB title:  base/databases/rabbit/imgt2019/IGXV
    Sequence type: Nucleotide
    Keep MBits: T
    Maximum file size: 1000000000B
    Adding sequences from FASTA; added 148 sequences in 0.0136719 seconds.



- In the same directory, reproduce the same exact process for the J and D genes for the rabbit; for example, to build the j-gene database, you should stay where you are in the rabbit directory, go back to the imgt site, click on the IGLJ or IGHJ sequences, copy and paste them into a file, run the edit_imgt_file script, and then run makedb. At the end of creating all V, J, and H databases, you will have filled the rabbit directory with tons of files, some prefaced IGXV, some prefaced with IGHD, and some with IGXJ. You are now ready to run igblast.


References:

Lefranc, M.-P. and Lefranc, G. The Immunoglobulin FactsBook Academic Press, London, UK (458 pages), (2001)

Lefranc, M.-P. and Lefranc, G. The T cell receptor FactsBook Academic Press, London, UK (398 pages), (2001) 

Ye, J., Ma, N., Madden, T. L., & Ostell, J. M. (2013). IgBLAST: An immunoglobulin variable domain sequence analysis tool. Nucleic Acids Research, 41(Web Server issue), W34–W40. https://doi.org/10.1093/nar/gkt382



