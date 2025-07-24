---
title: 编译 VASP 和 QE
summary: 使用编译器编译 VASP 和 Quantum Espresso（开源）。
tags:
  - Ab Initio
date: 2025-06-10
categories:
  - Work
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---

## 一、安装 VASP

### 1. 安装 Intel OneAPI 环境

1) 需要文件：

```bash
intel-onemkl-2025.1.0.803_offline.sh
l_BaseKit_p_2024.1.0.596_offline.sh
l_HPCKit_p_2024.1.0.560_offline.sh
```

2) 加载环境变量

```bash
source /opt/intel/oneapi/setvars.sh

:: initializing oneAPI environment ...
   -bash: BASH_VERSION = 5.1.8(1)-release
   args: Using "$@" for setvars.sh arguments: 
:: advisor -- latest
:: ccl -- latest
:: compiler -- latest
:: dal -- latest
:: debugger -- latest
:: dev-utilities -- latest
:: dnnl -- latest
:: dpcpp-ct -- latest
:: dpl -- latest
:: ipp -- latest
:: ippcp -- latest
:: mkl -- latest
:: mpi -- latest
:: pti -- latest
:: tbb -- latest
:: umf -- latest
:: vtune -- latest
:: oneAPI environment initialized ::

icx --version
Intel(R) oneAPI DPC++/C++ Compiler 2025.1.1 (2025.1.1.20250418)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /opt/intel/oneapi/compiler/2025.1/bin/compiler
```

3) 手动进入该目录：`cd /opt/intel/oneapi/mkl/latest/share/mkl/interfaces/fftw3xf/`​ 执行 `make libintel64`​

> 注：从 **Intel oneAPI 2021+**  开始，传统的 `icc`​/`icpc`​ 被新的 **LLVM-based** 编译器 `icx`​（C）和 `icpx`​（C++）取代。

### 2. ELPA编译和配置

1) 下载 elpa `wget https://elpa.mpcdf.mpg.de/software/tarball-archive/Releases/2024.05.001/elpa-2024.05.001.tar.gz`​
2) 配置

    ```bash
    cd elpa-2024.05.001
    ./configure --prefix=/opt/elpa-2024.05.001 CC=mpiicx CXX=mpiicpx FC=mpiifx \
    CFLAGS="-O3 -xHost" FCFLAGS="-O3 -xHost" \
    SCALAPACK_FCFLAGS="-I${MKLROOT}/include/intel64/lp64" \
    SCALAPACK_LDFLAGS="-L${MKLROOT}/lib/intel64 -lmkl_scalapack_lp64 \
    -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lmkl_blacs_intelmpi_lp64 \
    -lpthread -lm -Wl,-rpath,${MKLROOT}/lib/intel64" \
    --enable-option-checking=fatal --with-mpi=yes
    ```

3) 编译安装 `make -j & make instal`​
4) 编辑 .bashrc 添加 `export LD\_LIBRARY\_PATH\=/opt/elpa-2024.05.001/lib:\$LD\_LIBRARY\_PATH`​

### 3. 安装 VASP

1) 安装 vasp：将文件传输到虚拟机后，我是将 vasp.6.3.2 安装文件放在 vasp  文件夹下的，在 vasp 目录下打开终端执行解压命令：

```bash
tar -zxvf vasp.6.3.2.tgz
```

2) 在解压后进入 vasp.6.3.2 文件夹，打开终端执行：

```bash
cp arch/makefile.include.intel makefile.include
```

3) 执行 vim 命令对 makefile.include 文件进行编辑：删除 MKLROOT＝后面的值，因为 intel 声明环境变量的时候就已经声明位置了

修改 makefile.include 文件中 FC 和 FCL 数值 mpiifort 为 mpiifx，32行 icc 为 icx，40 行 icpc 为 icpx，并在结尾添加如下代码

```bash
CPP_OPTIONS += -DELPA
ELPA_ROOT   ?= /opt/elpa-2024.05.001
LLIBS       += -L$(ELPA_ROOT)/lib -lelpa
INCS        += -I$(ELPA_ROOT)/include/elpa-2024.05.001/elpa
INCS        += -I$(ELPA_ROOT)/include/elpa-2024.05.001/modules
```

4) 编译安装：`make DEPS\=1 -j18 all`​

```bash
alias vs11="mpirun -np 36 /home/jzq/software/vasp.6.4.2/bin/vasp_std"
alias vg11="mpirun -np 36 /home/jzq/software/vasp.6.4.2/vasp_gam"
alias vn11="mpirun -np 36 /home/jzq/software/vasp.6.4.2/vasp_ncl"
```

‍

> 注意添加动态链接库

```bash
export LD_LIBRARY_PATH=/opt/intel/oneapi/mkl/2025.1/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/opt/intel/oneapi/compiler/2024.1/lib/:$LD_LIBRARY_PATH
export PATH=/opt/intel/oneapi/2024.1/bin:$PATH
export LD_LIBRARY_PATH=/opt/elpa-2024.05.001/lib:$LD_LIBRARY_PATH

$ ldd vasp_std 
        linux-vdso.so.1 (0x0000146b0ef22000)
        libmkl_intel_lp64.so.2 => /opt/intel/oneapi/mkl/2025.1/lib/libmkl_intel_lp64.so.2 (0x0000146b0de00000)
        libmkl_sequential.so.2 => /opt/intel/oneapi/mkl/2025.1/lib/libmkl_sequential.so.2 (0x0000146b0c800000)
        libmkl_core.so.2 => /opt/intel/oneapi/mkl/2025.1/lib/libmkl_core.so.2 (0x0000146b08600000)
        libstdc++.so.6 => /lib64/libstdc++.so.6 (0x0000146b08200000)
        libmkl_scalapack_lp64.so.2 => /opt/intel/oneapi/mkl/2025.1/lib/libmkl_scalapack_lp64.so.2 (0x0000146b07a00000)
        libmkl_blacs_intelmpi_lp64.so.2 => /opt/intel/oneapi/mkl/2025.1/lib/libmkl_blacs_intelmpi_lp64.so.2 (0x0000146b0eecd000)
        libelpa.so.19 => /opt/elpa-2024.05.001/lib/libelpa.so.19 (0x0000146b07400000)
        libmpifort.so.12 => /opt/intel/oneapi/mpi/2021.12/lib/libmpifort.so.12 (0x0000146b07000000)
        libmpi.so.12 => /opt/intel/oneapi/mpi/2021.12/lib/libmpi.so.12 (0x0000146b05800000)
        libimf.so => /opt/intel/oneapi/compiler/2024.1/lib/libimf.so (0x0000146b05200000)
        libm.so.6 => /lib64/libm.so.6 (0x0000146b0edf0000)
        libc.so.6 => /lib64/libc.so.6 (0x0000146b04e00000)
        libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x0000146b0edd6000)
        libdl.so.2 => /lib64/libdl.so.2 (0x0000146b0edd1000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x0000146b0edcc000)
        /lib64/ld-linux-x86-64.so.2 (0x0000146b0ef24000)
        libifport.so.5 => /opt/intel/oneapi/compiler/2024.1/lib/libifport.so.5 (0x0000146b0eda2000)
        libifcoremt.so.5 => /opt/intel/oneapi/compiler/2024.1/lib/libifcoremt.so.5 (0x0000146b0c68e000)
        libintlc.so.5 => /opt/intel/oneapi/compiler/2024.1/lib/libintlc.so.5 (0x0000146b0ed3e000)
        libsvml.so => /opt/intel/oneapi/compiler/2024.1/lib/libsvml.so (0x0000146b03600000)
        librt.so.1 => /lib64/librt.so.1 (0x0000146b0ed39000)
```

参考：

> http://bbs.keinsci.com/thread-52194-1-1.html

## 二、安装 Quantum Espresso

### 1. 检查编译器

```bash
which mpif90
which gfortran
which gcc
```

### 2. 安装 openmpi

```bash
sudo dnf update -y
sudo dnf install openmpi openmpi-devel -y

echo 'export PATH=/usr/lib64/openmpi/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/lib64/openmpi/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

### 3. 检查库

```bash
sudo dnf install fftw-devel
sudo dnf install blas-devel lapack-devel
sudo dnf install scalapack-openmpi-devel
```

### 4. 配置

官网下载 QE 安装包解压，进入目录：

```bash
./configure MPIF90=mpif90 F90=gfortran CC=gcc \
            --enable-parallel \
            --enable-openmp \
            --prefix=/data/qe-7.3.1
make all install
```

