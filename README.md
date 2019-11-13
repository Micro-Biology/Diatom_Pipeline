# diatom-pipeline
Pipeline for diatom analysis

## Running Pipeline

Ensure you have Docker installed.  Instructions on how to do that are found at https://www.docker.com/

If you have built this container before please run `docker-compose build` before running `docker-compose up`


### If image has not been built before

1. In the directory containing these scripts, create a folder called **"sequences"** and then save all the fastq files in this folder.
2. Open a terminal in the directory containing all the scripts and run `docker-compose up`

**Make sure that the docker-compose.yml file is in the same directory as the other scripts and save all the fastq files you wish to analyse to the 'sequences' folder on your local machine / VM.**

### Once the docker image is built
1. Once complete, run `docker run -i -p 8888:8888 -t -v {path of local folder where scripts are}:/code/ {name of container}_app /bin/bash` An example is `docker run -i -t  -p 8888:8888 -v /mnt/diatom-pipeline:/code/ diatom-pipeline_app /bin/bash`
2. Now to copy PEAR to the user/bin of the container `cp pear/pear /usr/local/bin`
3. You can now access the notebook by entering `http://localhost:8888` in your browser. 
4. Next, run `sh diatomPipeline.dms sequences lookuptable.txt`
4. Enter `exit`



## Results
After running to completion the pipeline will output two files. The first file, **Abundances.fail.csv**, contains in the top row a list of the IDs of the samples which failed QC,i.e. samples returning <3000 sequences after quality trimming and read merging.

The second file, **Abundances.pass.csv**, contains a list of all samples that have passed QC.Column 1 lists the identity (strain ID) of each of strains identified in each sample and row 1 lists the sample ID (taken from the lookuptable.txtfile. Each cell will show the number of reads mapping to a particular strain for a particular sample.


### Tips and tricks

- You can find the container ID/ name by running `docker ps`.
- Large datasets need lots of RAM to run. The more your machine has, the better. 8 GB is minimum.
- Do not be concerned if the pipeline takes some time to run, our tests have shown that for a year's worth of data over 20 hours is normal.
=======
Existing pipeline created by The Environment Agency.

## Specified dependencies
- Python 2.7 
- BioPython v1.64+
- Pygal 2.0.0+
- Sickle v1.33
- Cutadapt v1.9.1
- QIIME v1.9.1
- PEAR v0.9.10 https://www.h-its.org/downloads/pear-commercial/

PEAR creative commons license prohibits commercial use of the code. For testing and using PEAR on a commercial basis you need to purchase a commercial software license. If you wish to purchase such a license please contact Prof. Alexandros Stamatakisat Alexandros.Stamatakis@h-its.org). 

## Discovered dependencies
Conda does not appear to pick up correct versions of some of the packages. 

```
sudo apt-get install ncbi-blast+
conda install -c bioconda biopython
sudo apt-get install --reinstall zlibc zlib1g zlib1g-dev
apt install moreutils
apt install parallel
apt install python-dev
pip install cutadapt==1.9.1
pip install lxml==3.5.0
pip install pygal==2.0.0
pip install numpy==1.7.1
pip install qiime==1.9.1
pip install pygal --upgrade
```

`sudo cp sickle /usr/local/bin` – make sickle and then copy it to `/usr/local/bin` - picked up newer version from github and built locally following the instructions on the repo as version from conda does not seem to install correctly / work. 

https://github.com/najoshi/sickle

### Base dependencies for Qiime 1.9.1

Install full QIIME 1.9.1 using **QIIME deploy** https://github.com/qiime/qiime-deploy

```
sudo apt-get --force-yes -y install python-dev libncurses5-dev libssl-dev libzmq-dev libgsl0-dev openjdk-6-jdk libxml2 libxslt1.1 libxslt1-dev ant git subversion build-essential zlib1g-dev libpng12-dev libfreetype6-dev mpich2 libreadline-dev gfortran unzip libmysqlclient18 libmysqlclient-dev ghc sqlite3 libsqlite3-dev libc6-i386 libbz2-dev tcl-dev tk-dev r-base r-base-dev libatlas-dev libatlas-base-dev liblapack-dev swig libhdf5-serial-dev
```

### Running the shell script in Ubuntu
Following the installation of the software, additional scripts and files are required to run the pipeline. 
These include:

- AmpliconQC.py
- produceDiatomReports.py
- create_taxonomy_assignments_from_blast.py 
- diatomPipeline.dms
- diatoms.taxonomy.FINAL2017.txt
- diatoms.sequences.FINAL2017.fasta

**There may be additional dependencies in other versions of Linux, so we recommend using Ubuntu.**

The files should be copied to **/usr/local/bin** or any other folder included in the PATH environment variableand perform the following:

1. Create a directory in which to perform your analysis (the **parent** directory)
2. Within the parent directory,create a subdirectory (sequences), and copy all of the *.fastq.gz sequencing files into the subdirectory.
3. Copy all the diatom database files **diatoms.taxonomy.FINAL2017.txt** and **diatoms.sequences.FINAL2017.fasta**, to the parent directory.
4. A tab-separated lookup table **(lookuptable.txt)** is required. This is a user-created file, linking the sequence **FERAID** (left hand side of table)  to **EA barcode id**.  Save this file in the parent directory. An example would look like this; 

| Sample_ID | Customer_ID | 
| --------- | ----------- |
| 146685423 | 11474855879 |

5. From the parent directory, type 
```
sh diatomPipeline.dms sequences lookuptable.txt
```
After running to completion the pipeline will output two files. 
The first file, **Abundances.fail.csv**, contains in the top row a list of the IDs of the samples which failed QC,i.e. samples returning <3000 sequences after quality trimming and read merging. The second file, **Abundances.pass.csv**, contains a list of all samples that have passed QC.Column 1 lists the identity (strain ID)of each of strainsidentifiedin each sample and row 1 lists the sample ID (taken from the
lookuptable.txtfile in step 4.). Each cell will show the number of reads mappingto a particular strain for a particular sample.


