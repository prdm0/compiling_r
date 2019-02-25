# Instructions for compiling R, OpenBLAS and linking R with OpenBLAS (GNU/Linux)

**Note**: Disregard the lines with #.

**Compiling R**

Initially download the **R** and **OpenBLAS** (**Open** Optimized **BLAS** Library) source codes in [**OpenBLAS**](https://www.openblas.net/). In the file directory, perform the following steps.
```
tar -zxvf OpenBLAS*
cd OpenBLAs*
make -j $nproc
sudo make install
```
or

```
git clone https://github.com/xianyi/OpenBLAS.git
cd OpenBLAS*
make -j $nproc
sudo make install

```
**Note**: This will make the compilation run faster using all the features of your CPU. To know the number of cores, do: ```nproc```.

After compiling **OpenBLAS**, download the **R** code. It is not necessary to compile **R** to make use of **OpenBLAS**, but compiling the language may bring some benefits that may be insignificant depending on what is being done in **R**. That way, download the source code of the language [**R**](https://cloud.r-project.org/).

**Note**: In my operating system, Arch Linux, **OpenBLAS** was installed in the ```/opt``` directory. Search for the **OpenBLAS** installation directory in your GNU/Linux distribution.

In the directory where the **R** was downloaded, do the following:

```
tar -zxvf R*
cd R-* && ./configure --enable-R-shlib --enable-threads=posix --with-lapack --with-blas="-L/opt/OpenBLAS/lib -I/opt/OpenBLAS/include -m64 -lpthread -lm"
make -j $nproc
sudo make install
```

We need to link the **R** with the file ```libopenblas_*```, created in the process of compiling the library **OpenBLAS**. In my case, the file is **ibopenblas_haswellp-r0.2.20.so**. Look for this in ```/opt/OpenBLAS/lib``` or in the directory where **OpenBLAS** was installed on your GNU/Linux system. Also look for the **libRblas.so** file directory found in the **R** language installation directory. In Arch, this directory is ```/usr/local/lib64/R/lib```. So, do:

```
cd /usr/local/lib64/R/lib
mv libRblas.so libRblas.so.keep
ln -s /opt/OpenBLAS/lib/libopenblas_haswellp-r0.2.20.so libRblas.so
```

Start a section of language **R** and do ```sessionInfo()```. You should note something like:

```
Matrix products: default
BLAS/LAPACK: /opt/OpenBLAS/lib/libopenblas_haswellp-r0.2.20.so
```
To make use of multithreaded processing, do ```export OPENBLAS_NUM_THREADS=1``` before starting a **R** section.

**Armadillo C++**

Para quem faz uso de códigos **C++** em **R** utilizando a biblioteca [**Rcpp**](http://www.rcpp.org/), configurar o [**Armadillo**](http://arma.sourceforge.net/) com a biblioteca **OpenBLAS** poderá ser algo proveitoso. 

```
tar -xvf armadillo*
cd armadillo*
```
**Note**: Maiores detalhes a respeito da compilação da biblioteca **Armadillo** poderá ser encontrado em https://gitlab.com/conradsnicta/armadillo-code.


**Note**: Disregard the code below. I will use it in future tests.

```
#export MKL_NUM_THREADS=1
#export GOTO_NUM_THREADS=8
#export OMP_NUM_THREADS=8
#USE_THREAD=0
```
**NOTE**: For intel processors,```sudo cpupower frequency-set -g performance```, can boost performance. Read more at https://wiki.archlinux.org/index.php/CPU_frequency_scaling.






