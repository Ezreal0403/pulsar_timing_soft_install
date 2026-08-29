# pulsar_timing_soft_installw
此文档针对于x86_64架构CPU, Ubuntu系统安装常用的脉冲星计时软件（Dspsr,Psrcat,Psrchive,Tempo,Tempo2）.
首先下载必要的依赖包.
打开终端，运行命令
sudo apt update
sudo apt install vim libtool wget libpng-dev libgd-dev autoconf automake libtool m4 git gsl-bin libgsl-dev flex bison fort77 libglib2.0-dev gnuplot  swig libltdl-dev libltdl7 cmake libblas3 liblapack3 libblas-dev liblapack-dev libxext-dev libx11-dev libopenmpi-dev openmpi-bin libhdf5-openmpi-dev mpich libmpich-dev libhdf5-mpich-dev tcsh pgplot5 imagemagick bc latex2html gfortran
待安装完成后，添加环境变量
vim ~/.bashrc 打开文件，将光标移到最下面一行，按 i 进入输入模式
将下面的环境变量复制进.bashrc文件末尾
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
用命令 ：wq 保存，再运行 source ~/.bashrc 刷新终端的环境变量
