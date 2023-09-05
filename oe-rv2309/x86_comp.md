# same
## FS_File
### oe_test_FSIO_act_file_lack_inode
- 均为软件包未预装，x86为lvm， riscv为软件xfsprogs未安装
### oe_test_FSIO_chmod
- 均为mugen脚本问题
### oe_test_FSIO_filefrag
- 均为软件包未预装，x86为lvm， riscv为软件xfsprogs未安装
## audit
- 软件audit未安装
## cachefilesd
### oe_test_service_cachefilesd
- systemd模块软件均未能正常启动，journalctl有文件缺失提示
## cmake
### oe_test_ccmake
- expect问题导致无法正常测试，在2303上两种架构均出现此问题，可能需要手动执行脚本检测缺陷
### oe_test_ccmake3
- expect问题导致无法正常测试，在2303上两种架构均出现此问题，可能需要手动执行脚本检测缺陷
## fftw
- x86和riscv中均出现相同问题，怀疑为上游缺陷
## hostname
- x86软件未预装，riscv启动失败
## iprutils
- 原因相同，测试脚本没有显式安装iprutils，镜像也没有预装
## kmod
### oe_test_insmod-lsmod
- 原因相同，由于raid0与faulty两个模块缺失，故 find 没有输出
## mcstrans
- 原因相同，mcstransd.service 启动失败
## mysql
- 原因相同，安装mysql软件包后，无法找到mysql.service
## qpdf
### oe_test_qpdf_qpdf_01
- 原因相同，mugen脚本编写问题，grep内容与输出不符
### oe_test_qpdf_qpdf_02
- 原因相同，qpdf无法使用弱密码算法写入文件
### oe_test_qpdf_qpdf_03
- 原因相同，qpdf无法使用弱密码算法写入文件
### oe_test_qpdf_qpdf_08
- 原因相同，qpdf无法使用弱密码算法写入文件
### oe_test_qpdf_qpdf_10
- 原因相同，mugen脚本编写问题，grep内容与输出不符
## rng-tools
- 软件rng-tools未安装
## systemtap
### oe_test_service_systemtap
- 原因相同，软件未安装

# diff
## iSulad
### oe_test_iSulad_container
- 原因不同，x86
```
Error response from daemon: shim-log error: {"level": "error", "msg": "runtime error"}

runtime-log error: 
```
riscv 出现安装依赖错误

### oe_test_iSulad_resource_mapping
- 原因不同，x86未预装软件hostname,riscv出现预装问题

### oe_test_iSulad_resource_restriction_cgroup
- 原因不同，x86
```
Error response from daemon: shim-log error: {"level": "error", "msg": "runtime error"}

runtime-log error: 
```
riscv 出现安装依赖错误

### oe_test_service_isulad
- 原因不同，x86服务启动失败，riscv 出现安装依赖错误

## kmod
### oe_test_depmod
- 原因相同， find Module.symvers与System.map均为空
### oe_test_modinfo
- 原因相同，modinfo -p raid1 无输出

## lvm2
- 原因相同，mugen中suite2cases文档中无add disk信息

## rpmdevtools
### oe_test_rpmdevtools_rpmargs
- 原因不同，x86为-q -C 选项均未通过， riscv为yumdownloader行为异常

## yajl
- 原因相同，源中无对应软件



# 09fail

## gsl
### oe_test_gsl_histogram_01
- 原因相同，2309中gsl对应版本为2.7.1，软件命令行使用格式有所改变，但mugen脚本没有更新

## libvirt
### oe_test_socket_libvirtd
- 手动无法复现
### oe_test_socket_libvirtd-ro
- 手动无法复现
### oe_test_socket_virtlockd
- 手动无法复现
### oe_test_socket_virtlogd
- 手动无法复现

## lvm2
- mugen中suite2cases文档中无add disk信息

## polkit
### oe_test_service_polkit
- 原因相同，软件polkit未安装

## rpmlint
- 原因不同，x86 wget软件未安装，riscv软件安装出现依赖错误

## timedatex
- 原因相同，timedatex对应软件包未安装

