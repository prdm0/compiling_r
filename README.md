# Instructions for compiling R, OpenBLAS and linking R with OpenBLAS (GNU/Linux)

**DEPENDENCES**: make, cmake, gcc, gcc-fortran and tk.

**Important**: I'll be at all times assuming that the project [**Julia**](https://julialang.org/) has been cloned into the directory `~/Downloads`. Also, I will consider the `/opt` directory as the installation directory for the [**OpenBLAS**](https://www.openblas.net/) library and of the [**Julia**](https://julialang.org/) language. You can choose a directory of your choice.

## Compiling OpenBLAS

Initially download the [**R**](https://cloud.r-project.org/) and [**OpenBLAS**](https://www.openblas.net/) (**Open** Optimized **BLAS** Library) source codes in [**OpenBLAS**](https://www.openblas.net/). In the file directory, perform the following steps.
```
tar -zxvf OpenBLAS*
cd OpenBLAs*
make -j $(nproc)
sudo make install
```
or

```
git clone https://github.com/xianyi/OpenBLAS.git
cd OpenBLAS*
git checkout v0.3.5
make -j $(nproc)
sudo make install
```
**Note**: This will make the compilation run faster using all the features of your CPU. To know the number of cores, do: ```nproc```. The default installation directory is `/opt/OpenBLAS`.


## Compiling Armadillo C++  with OpenBLAS

For those who use **C++** codes in [**R**](https://cloud.r-project.org/) using the library [**Rcpp**](http://www.rcpp.org/), configure the [**Armadillo**](http://arma.sourceforge.net/) with the library [**OpenBLAS**](https://www.openblas.net/) be something fruitful.

```
tar -xvf armadillo*
cd armadillo*
export LD_LIBRARY_PATH=/opt/OpenBLAS/lib/
./configure -DCMAKE_PREFIX_PATH=/opt/OpenBLAS/lib/
cmake . -DCMAKE_PREFIX_PATH=/opt/OpenBLAS/lib/
make -j $(nproc)
sudo make install
```
**Note**: Further details regarding the compilation of the library [**Armadillo**](http://arma.sourceforge.net/) can be found at https://gitlab.com/conradsnicta/armadillo-code.

## Compiling R with OpenBLAS

After compiling [**OpenBLAS**](https://www.openblas.net/), download the [**R**](https://cloud.r-project.org/) code. It is not necessary to compile [**R**](https://cloud.r-project.org/) to make use of [**OpenBLAS**](https://www.openblas.net/), but compiling the language may bring some benefits that may be insignificant depending on what is being done in [**R**](https://cloud.r-project.org/). That way, download the source code of the language [**R**](https://cloud.r-project.org/).

**Note**: In my operating system, Arch Linux, [**OpenBLAS**](https://www.openblas.net/) was installed in the ```/opt``` directory. Search for the [**OpenBLAS**](https://www.openblas.net/) installation directory in your GNU/Linux distribution.

In the directory where the [**R**](https://cloud.r-project.org/) was downloaded, do the following:

```
tar -zxvf R*
cd R-* 
export LD_LIBRARY_PATH=/opt/OpenBLAS/lib/
./configure --prefix=/opt/R/3.5.3 --enable-R-shlib --enable-threads=posix --with-blas="-lopenblas -L/opt/OpenBLAS/lib -I/opt/OpenBLAS/include -m64 -lpthread -lm"
make -j $(nproc)
sudo make install
```
**Note**: The language [**R**](https://cloud.r-project.org/) will be configured and installed in the `/opt/R/3.5.3` directory. Replace 3.5.3 by the version of [**R**](https://cloud.r-project.org/) being installed. This allows us to have multiple versions of [**R**](https: //cloud.r-project.org/) in `/opt/R/`.

Most likely the [**OpenBLAS**](https://www.openblas.net/) library will be bound to [**R**](https://cloud.r-project.org/). To check, run  in the [**R**](https://cloud.r-project.org/) the ```sessionInfo()``` code. Something like the output below should appear:

```
Matrix products: default
BLAS/LAPACK: /opt/OpenBLAS/lib/libopenblas_haswellp-r0.3.5.so
```
If linking does not occur, follow the steps outlined in the code below.

We need to link the [**R**](https://cloud.r-project.org/) with the file ```libopenblas_*```, created in the process of compiling the library [**OpenBLAS**](https://www.openblas.net/). In my case, the file is **libopenblas_haswellp-r0.3.5.so**. Look for this in ```/opt/OpenBLAS/lib``` or in the directory where [**OpenBLAS**](https://www.openblas.net/) was installed on your GNU/Linux system. Also look for the **libRblas.so** file directory found in the [**R**](https://cloud.r-project.org/) language installation directory. In Arch, this directory is ```/usr/local/lib64/R/lib```. 

```
cd /usr/local/lib64/R/lib
sudo mv libRblas.so libRblas.so.keep
sudo ln -s /opt/OpenBLAS/lib/libopenblas_haswellp-r0.3.5.so libRblas.so
```

Start a section of language [**R**](https://cloud.r-project.org/) and do ```sessionInfo()```. You should note something like:

```
Matrix products: default
BLAS/LAPACK: /opt/OpenBLAS/lib/libopenblas_haswellp-r0.3.5.so
```
To make use of multithreaded processing, do ```export OPENBLAS_NUM_THREADS=1``` before starting a [**R**](https://cloud.r-project.org/) section.

```
export OPENBLAS_NUM_THREADS=1
export GOTO_NUM_THREADS=1
export OMP_NUM_THREADS=1
```

**NOTE**: For intel processors,```sudo cpupower frequency-set -g performance```, can boost performance. Read more at https://wiki.archlinux.org/index.php/CPU_frequency_scaling.






