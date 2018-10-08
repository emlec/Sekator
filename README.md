# Sekator 0.2.1

**Multithreaded quality and adapter trimmer for PAIRED fastq files (Python2.7/Cython/C)**

[see GitHub Page](http://a-slide.github.io/Sekator) 

**Creation : 2015/03/31**

**Last update : 2015/04/13** 

## Motivation

Sekator is a **python2.7, cython 0.21+, C** object oriented semi-compiled program performing fastq quality trimming and adapter trimming

Specific features:

* The program can parse a pair of fastq files files per sample, but many samples can be analysed together.
* Fastq reading and writing are mono-threaded, but the trimming steps are multi-threaded.
* The quality trimming step can be performed from both ends of reads with an adjustable sliding windows.
* The adapter trimming step is performed by searching imperfect matches of as many adapters as desired (or short sequences) thanks to a fast Smith and Waterman Algorithm coded in C (maintained by [Mengyao](https://github.com/mengyao/Complete-Striped-Smith-Waterman-Library)).

## Principle

1. A configuration file containing all program parameters (including sample/adpater association) is parsed and thoroughly verified for validity.
2. Paired fastq paired files are read read by read and sample by sample with a custom Fastq parser (pyFastq Submodule) supporting **Illumina 1.8 Phred +33 quality encoding only**.
3. If required, a quality trimming of reads can be performed with a quality sliding windows, starting from both ends of reads. Reads of insufficient quality or too short after trimming are discarded, together with their paired mate.
4. If required, an adapter trimming of reads can be performed with the adapters provided for each sample. **Imperfect matches can be found anywhere in the reads for as many adapters as required** thanks to an optimized and fast Smith and Waterman Algorithm. If adapters matches are found in a read, the longest part of the read without adapter match is extracted. Reads too short after trimming are discarded, together with their paired mate.
5. The paired reads that passed thought the trimming steps are subsequently writen in new fastq.gz files (R1 and R2) in Illumina 1.8 Phred+33 quality encoding.
6. A progress bar indicates the advancement of sequence processing and a report is generated for each sample. 

## Dependencies

The program was developed under Linux Mint 17 and was not tested with other OS.
In addition to python2.7 and gcc 4.8 + the following dependencies are required for proper program execution:

* cython 0.21.2+
* python package [numpy](http://www.numpy.org/) 1.7.1+

If you have pip already installed, enter the following line to install packages: ```sudo pip install numpy cython```

## Get and install Sekator

* Clone the repository in **recursive mode** to download the main repo and its submodules ```git clone --recursive https://github.com/emlec/Sekator.git```

* Enter the src folder of the program folder

* Compile the C/Cython sources with the Makefile ```make``` or the setup.py ```python setup.py build_ext --inplace```. This will create the dynamic library AdapterTrimmer.so required for the adapter trimming step.

* Unnecessary files can be removed with the makefile ```make clean```

* Make the main script executable ```sudo chmod u+x Sekator.py```

* Finally, add Sekator.py to your PATH

## Usage

In the folder where fastq files will be created

Usage: Sekator.py -c Conf.txt [-i -h]
```
Options:
  --version     show program's version number and exit
  -h, --help    show this help message and exit
  -c CONF_FILE  Path to the configuration file [Mandatory]
  -i            Generate an example configuration file and exit [Facultative]
```
An example configuration file can be generated by running the program with the option -i
The possible options are extensively described in the configuration file.
The program can be tested from the test folder with the dataset provided and the default configuration file.
```
cd ./test/result
Sekator.py -i
Sekator.py -c Sekator_conf_file.txt
```

## Authors and Contact

* Adrien Leger <aleg@ebi.ac.uk> @a-slide
* Emilie Lecomte <emilie.lecomte@univ-nantes.fr> @emlec
