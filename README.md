<h1 align="center">脉冲星计时软件安装指南</h1>

### yangf  
此文档针对于**x86_64**架构CPU, **Ubuntu**系统安装常用的脉冲星计时软件(Dspsr,Psrcat,Psrchive,Tempo,Tempo2),终端执行`uname -m`可以查看CPU架构.  
### 下载必要的依赖包
打开终端，运行命令  
```bash
sudo apt update
```
```bash
sudo apt install vim libtool wget libpng-dev libgd-dev autoconf automake libtool m4 git gsl-bin libgsl-dev flex bison fort77 libglib2.0-dev gnuplot  swig libltdl-dev libltdl7 cmake libblas3 liblapack3 libblas-dev liblapack-dev libxext-dev libx11-dev libopenmpi-dev openmpi-bin libhdf5-openmpi-dev mpich libmpich-dev libhdf5-mpich-dev tcsh pgplot5 imagemagick bc latex2html gfortran
```  
等待安装完成.  

### 添加环境变量  
```bash
vim ~/.bashrc
```
打开文件，将光标移到最下面一行，按`i`进入输入模式  
将下面的环境变量复制进.bashrc文件末尾，**将{user}换成你电脑的用户名**  

```bash
# Path to the pulsar software installation directory e.g:
export ASTROSOFT=/home/{user}/psrsoft

# OSTYPE
export OSTYPE=linux

# Pgplot
PGPLOT_DIR=$ASTROSOFT/pgplot_build

# PSRCAT
export PSRCAT_RUNDIR=$ASTROSOFT/psrcat_tar
export PSRCAT_FILE=$ASTROSOFT/psrcat_tar/psrcat.db

# Tempo
export TEMPO=$ASTROSOFT/tempo

# Tempo2
export TEMPO2=$ASTROSOFT/tempo2/T2runtime

# MULTINEST
export MULTINEST_DIR=$ASTROSOFT/TempoNest/MultiNest

# LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/lib:/usr/lib/x86_64-linux-gnu:$PGPLOT_DIR:$ASTROSOFT/lib:$PRESTO/lib:$PRESTO/lib64:$MULTINEST_DIR

# PATH
# Some Presto executables match sigproc executables so keep separate -
# all other executables are found in $ASTROSOFT/bin
export PATH=$PATH:$ASTROSOFT/bin:$PGPLOT_DIR

# Pkgconfig
export PKG_CONFIG_PATH=$ASTROSOFT/lib/pkgconfig:$PKG_CONFIG_PATH:$ASTROSOFT/psrchive/Management:$ASTROSOFT/lib/pkgconfig
```

输入`：wq`加回车保存.终端执行`source ~/.bashrc`刷新终端的环境变量.若终端执行`echo $ASTROSOFT`输出了`/home/{user}/psrsoft`,则说明成功.  
创建两个文件夹,存放脉冲星计时软件的可执行文件和库文件
```bash
mkdir $ASTROSOFT/bin $ASTROSOFT/lib
```
### 安装FFT包
```bash
cd $ASTROSOFT
wget http://www.fftw.org/fftw-3.3.11.tar.gz
tar zvxf fftw-3.3.11.tar.gz
cd fftw-3.3.11
./configure --prefix=$ASTROSOFT --enable-float --enable-shared #第二个参数--enable-float是安装单精度版本，第三个参数--enable-shared是安装双精度版本
make
make check
make install
make clean
```
若在$ASTROSOFT/lib下看到**libfftw3.so**和**libfftw3f.so**两个文件说明安装成功  

### 按装CFITSIO包
```bash
cd $ASTROSOFT
wget https://heasarc.gsfc.nasa.gov/FTP/software/fitsio/c/cfitsio-4.7.0.tar.gz
tar zvxf cfitsio-4.7.0.tar.gz
cd cfitsio-4.7.0
./configure --prefix=$ASTROSOFT
make
make install
make clean
```
### 安装Psrcat
```bash
cd $ASTROSOFT
wget https://www.atnf.csiro.au/research/pulsar/psrcat/downloads/psrcat_pkg.v2.8.1.tar.gz
tar zvxf psrcat_pkg.v2.8.1.tar.gz
cd psrcat_tar
source makeit
cp psrcat $ASTROSOFT/bin
```
### 安装Pgplot
```bash
mkdir $ASTROSOFT/pgplot_build
cd $ASTROSOFT
wget ftp://ftp.astro.caltech.edu/pub/pgplot/pgplot5.2.tar.gz
tar zvxf pgplot5.2.tar.gz
cd pgplot_build
../pgplot/makemake ../pgplot linux g77_gcc
```
##### 打开drivers.list,将下面内容所在行前的！去掉  
PNDRIV 1 /PNG  
PNDRIV 2 /TPNG  
PSDRIV 1 /PS  
PSDRIV 2 /VPS  
PSDRIV 3 /CPS  
PSDRIV 4 /VCPS  
XWDRIV 1 /XWINDOW  
XWDRIV 2 /XSERVE  
```bash
../pgplot/makemake ../pgplot linux g77_gcc #再执行一次
cd pgplot
```
##### 修改 Makefile
第25行改为 FCOMPL=gfortran  
第880行改为 pndriv.o : /usr/include/png.h /usr/include/pngconf.h /usr/include/zlib.h /usr/include/zconf.h
```bash
make
make clean
make cpg
ld -shared -o libcpgplot.so --whole-archive libcpgplot.a
```
可以运行`./cpgdemo`测试是否安装成功，点击终端，Enter切换下一张图

### 安装Tempo
```bash
cd $ASTROSOFT
git clone git://git.code.sf.net/p/tempo/tempo
cd tempo
./prepare
./configure F77=gfortran --prefix=$ASTROSOFT --with-cfitsio-dir=$ASTROSOFT --with-fftw3-dir=$ASTROSOFT CFLAGS=-fPIC FFLAGS=-fPIC LIBS=-lgslcblas
make
make install
make clean
```
### 安装Tempo2
```bash
cd $ASTROSOFT
git clone https://bitbucket.org/psrsoft/tempo2.git
./bootstrap
./configure F77=gfortran --prefix=$ASTROSOFT --with-cfitsio-dir=$ASTROSOFT --with-fftw3-dir=$ASTROSOFT LDFLAGS=-L/lib/x86_64-linux-gnu LIBS=-lgslcblas
make && make install
make plugins && make plugins-install
```
### 安装Psrchive
```bash
cd $ASTROSOFT
git clone git://git.code.sf.net/p/psrchive/code psrchive
cd psrchive
./bootstrap
./configure F77=gfortran --prefix=$ASTROSOFT LDFLAGS=-L/lib/x86_64-linux-gnu --enable-shared
make
make install
make clean
```
### 安装Dspsr
```bash
cd $ASTROSOFT
git clone git://git.code.sf.net/p/dspsr/code dspsr
cd dspsr
```
```bash
vim backends.list
```
将下面内容复制进去
```
apsr asp bcpm bpsr caspsr cpsr2 cpsr dummy fits gmrt guppi kat lbadr64 lbadr lump lwa mark4 mark5 maxim mwa pdev pmdaq s2 sigproc spda1k spigot vdif
```
`:wq`保存内容  
```bash
./bootstrap
./configure --prefix=$ASTROSOFT --with-cfitsio-dir=$ASTROSOFT F77=gfortran CFLAGS=-fPIC FFLAGS=-fPIC LDFLAGS=-L$PGPLOT_DIR
make
make install
make clean
```
