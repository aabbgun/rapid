# RAPID
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.593867.svg)](https://doi.org/10.5281/zenodo.593867)

[![License (3-Clause BSD)](https://img.shields.io/badge/license-BSD%203--Clause-yellow.svg)](https://github.com/c-h-david/rapid/blob/master/LICENSE)

[![Build Status](https://travis-ci.org/c-h-david/rapid.svg?branch=master)](https://travis-ci.org/c-h-david/rapid)

[![Docker Build](https://img.shields.io/docker/cloud/build/chdavid/rapid.svg)](https://hub.docker.com/r/chdavid/rapid/)

The Routing Application for Parallel computatIon of Discharge (RAPID) is a river
network routing model. Given surface and groundwater inflow to rivers, this 
model can compute flow and volume of water everywhere in river networks made out 
of many thousands of reaches. 

For further information on RAPID including peer-reviewed publications, tutorials, 
sample input/output data, sample processing scripts and animations of model 
results, please go to: 
[http://rapid-hub.org/](http://rapid-hub.org/).

## Installation with Docker
Installing RAPID is **by far the easiest with Docker**. This document was
written and tested using
[Docker Community Edition](https://www.docker.com/community-edition#/download)
which is available for free and can be installed on a wide variety of operating
systems. To install it, follow the instructions in the link provided above.

Note that the experienced users may find more up-to-date installation
instructions in
[Dockerfile](https://github.com/c-h-david/rapid/blob/master/Dockerfile).

### Download RAPID
Downloading RAPID with Docker can be done using:

```
$ docker pull chdavid/rapid
```

### Install packages
The beauty of Docker is that there is **no need to install anymore packages**.
RAPID is ready to go! To run it, just use:

```
$ docker run --rm --name rapid -it chdavid/rapid
```

## Testing with Docker
Testing scripts are currently under development.

Note that the experienced users may find more up-to-date testing instructions
in
[docker.test.yml](https://github.com/c-h-david/rapid/blob/master/docker.test.yml).

## Installation on Ubuntu
This document was written and tested on a machine with a **clean** image of 
[Ubuntu 16.04.1 Desktop 64-bit](http://old-releases.ubuntu.com/releases/16.04.1/ubuntu-16.04.1-desktop-amd64.iso)
installed, *i.e.* **no update** was performed, and **no upgrade** either. 

Note that the experienced users may find more up-to-date installation 
instructions in
[.travis.yml](https://github.com/c-h-david/rapid/blob/master/.travis.yml).

### Download RAPID
First, make sure that `git` is installed: 

```
$ sudo apt-get install -y git
```

Then download RAPID:

```
$ git clone https://github.com/c-h-david/rapid
```

Finally, enter the rapid directory:

```
$ cd rapid/
```

### Install APT packages
Software packages for the Advanced Packaging Tool (APT) are summarized in 
[requirements.apt](https://github.com/c-h-david/rapid/blob/master/requirements.apt)
and can be installed with `apt-get`. All packages can be installed at once using:

```
$ sudo apt-get install -y $(grep -v -E '(^#|^$)' requirements.apt)
```

> Alternatively, one may install the APT packages listed in 
> [requirements.apt](https://github.com/c-h-david/rapid/blob/master/requirements.apt)
> one by one, for example:
>
> ```
> $ sudo apt-get install -y gfortran
>```

### Install netCDF
The Network Common Data Form (NetCDF) can be installed using:

```
$ mkdir $HOME/installz
$ cd $HOME/installz
$ mkdir netcdf-install
$ wget "http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-c-4.7.3.tar.gz"
$ wget "http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-fortran-4.5.2.tar.gz"
$ tar -xzf netcdf-c-4.7.3.tar.gz
$ tar -xzf netcdf-fortran-4.5.2.tar.gz
$ cd netcdf-c-4.7.3/
$ ./configure CC=gcc CPPFLAGS=-I/usr/lib/x86_64-linux-gnu/hdf5/serial/include LDFLAGS=-L/usr/lib/x86_64-linux-gnu/hdf5/serial/lib --prefix=$INSTALLZ_DIR/netcdf-install --disable-dap
$ make check > check.log
$ make install > install.log
$ cd ..
$ cd netcdf-fortran-4.5.2/
$ ./configure CC=gcc FC=gfortran CPPFLAGS=-I$HOME/installz/netcdf-install/include LDFLAGS=-L$HOME/installz/netcdf-install/lib --prefix=$HOME/installz/netcdf-install/
$ make check > check.log
$ make install > install.log
$ cd ..
```

Then, the environment should be updated using:

```
$ export TACC_NETCDF_DIR=$HOME/installz/netcdf-install
$ export TACC_NETCDF_LIB=$TACC_NETCDF_DIR/lib
$ export TACC_NETCDF_INC=$TACC_NETCDF_DIR/include
$ export LD_LIBRARY_PATH=$TACC_NETCDF_LIB
$ export PATH=$PATH:$TACC_NETCDF_DIR/bin
```

> Note that these four lines can also be added in `~/.bash_aliases` so that the 
> environment variables persist.

### Install PETSc
The Portable, Extensible Toolkit for Scientific Computation (PETSc)
can be installed using:

```
$ cd $HOME/installz
$ wget "http://ftp.mcs.anl.gov/pub/petsc/release-snapshots/petsc-3.6.2.tar.gz"
$ tar -xzf petsc-3.6.2.tar.gz
$ cd petsc-3.6.2
$ ./configure PETSC_DIR=$PWD PETSC_ARCH=linux-gcc-c --download-fblaslapack=1 --download-mpich=1 --with-cc=gcc --with-cxx=g++ --with-fc=gfortran --with-clanguage=c --with-debugging=0
$ make all > all.log
$ make test > test.log
```

Then, the environment should be updated using:

```
$ export PETSC_DIR=$HOME/installz/petsc-3.6.2$ export PETSC_ARCH=linux-gcc-c
$ export PATH=$PATH:$PETSC_DIR/$PETSC_ARCH/bin
```

> Note that these three lines can also be added in `~/.bash_aliases` so that the 
> environment variables persist.
> 

### Build RAPID

```
$ cd rapid/
$ cd src/
$ make rapid
```

## Testing on Ubuntu
Testing scripts are currently under development.

```
$ cd rapid/
$ cd tst/
$ gfortran -o tst_run_comp tst_run_comp.f90 -I $TACC_NETCDF_INC -L $TACC_NETCDF_LIB -lnetcdff
$ gfortran -o tst_run_cerr tst_run_cerr.f90 -I $TACC_NETCDF_INC -L $TACC_NETCDF_LIB -lnetcdff
$ gfortran -o tst_run_conv_Qinit tst_run_conv_Qinit.f90 -I $TACC_NETCDF_INC -L $TACC_NETCDF_LIB -lnetcdff
```

Note that the experienced users may find more up-to-date testing instructions 
in
[.travis.yml](https://github.com/c-h-david/rapid/blob/master/.travis.yml).
