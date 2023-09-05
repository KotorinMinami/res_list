# openEuler 23.09 RISC-V Mugen测试结果

本次测试基于 [mugen](https://gitee.com/openeuler/mugen) [2023年8月1日](https://gitee.com/openeuler/mugen/commit/d1fb5af5572de344090fb979bdc45694564b0620)测试套仓库中包含的所有base OS测试套及测试用例。

测试方法详见 [Mugen 测试方法](./Mugen.md)

共测试了 **266** 个测试套， **2075** 个测试用例，其中

- **1246** 个测试用例在23.09上成功
- **494** 个测试用例在23.03和23.09上均失败，失败原因相同
- **75** 个测试用例在23.03和23.09上均失败，失败原因不同
- **260** 个测试用例在23.09上失败，在23.03上成功
- **142** 个测试例在23.09上成功，在23.03上失败

本次测试中，可通过result_parser.py排查确认的测试未通过原因或者用例未通过原因有以下类型：
- 测试用例不能（完全）执行: broken testcase
- 软件包缺失: pkg not found
- 预装缺失: preinstall absent
- 内核模块缺失: kernel module absent
- 文件缺失（软件包已安装）: file missing
- systemd 单元错误
  - 重启错误: systemd unit restart failure
  - 运行时错误: systemd unit runtime error
  - 使能错误: systemd unit enable failure
- 超时: timeout
- 无效参数: invalid argument
- 其他（未被归类）

- 一个用例可能会有多个类型的测试未通过原因或者未被归类（其他）

## 测试未通过原因类型说明

1. 较大概率为软件缺陷造成

- 文件缺失（软件包已安装）: file missing
  - 判断标准：用例有执行软件包安装操作，遇到 command not found/.service not found/No such file or directory的情况
  - 说明:可能为安装的软件包中缺少对应文件，软件打包存在问题
- systemd 单元错误
  - 重启错误: systemd unit restart failure
    - 用例中测试 systemd 单元重启操作失败
  - 运行时错误: systemd unit runtime error
    - 测试中 systemd 单元的日志中有报错信息
  - 使能错误: systemd unit enable failure
    - 用例中测试 systemd 单元使能操作失败
- 其他（未被归类）
  - 其他（未被归类）的测试未通过原因中，大部分为被测软件运行出错，但无法被归为某一特定类型，较大概率为软件 bug

2. 较小概率或并非为软件缺陷造成

- 超时: timeout
  - 可能为软件缺陷造成
  - 判断标准：日志中出现 timeout 等关键词
- 测试用例不能（完全）执行: broken testcase
  - 可能为软件缺陷造成
  - 判断标准：测试用例没有执行主体测试代码（ run_test 函数）
  - 说明:一般为用例运行时在环境准备阶段出错退出
- 无效参数: invalid argument
  - 小概率为软件缺陷造成
  - 判断标准：日志中出现 invalid option/invalid argument 等关键词
  - 说明:测试中执行了被测程序的无效参数，可能为用例编写有问题或程序更新后参数变化
- 软件包缺失: pkg not found
  - 并非软件缺陷造成
  - 判断标准：用例运行时软件包安装操作（ DNF_INSTALL 函数）遇到软件包缺失
  - 说明:由于测试的测试套都有软件源中对应的软件包，运行测试时遇到的软件包缺失为与测试套对应软件包相关的软件包缺失（如相关的库等）
- 预装缺失: preinstall absent
  - 并非为软件缺陷造成
  - 判断标准：用例没有执行软件包安装操作，遇到 command not found/.service not found/No such file or directory 的情况
  - 说明:一般为用例将某些软件包视为预装但被测镜像中并未预装
- 内核模块缺失: kernel module absent
  - 并非为软件缺陷造成
  - 判断标准： modprobe 时报错 xxx module not found
  - 说明:缺失对应的内核模块

## BaseOS 只在 23.09 版本下失败用例

此表内的测试套及测试用例均为在23.03上成功，在 23.09 上失败的 BaseOS 测试用例。共涉及 260 个测试用例。

| 测试套/软件包名 | 测试用例名 | 状态 | 日志文件 | 原因 | 
| :---: | :---: | :---: | :---: | :---: | 
| FS_File | oe_test_FSIO_check_alias | fail | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_alias/2023-08-09-02_17_51.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_alias/2023-08-09-02_17_51.log) | grep: /root/.bashrc: No such file or directory，测试镜像中bash环境配置文件缺失 |
| ModemManager | oe_test_service_ModemManager | fail | [./oe-rv2309/mugen-riscv/logs/ModemManager/oe_test_service_ModemManager/2023-08-08-16_16_45.log](./oe-rv2309/mugen-riscv/logs/ModemManager/oe_test_service_ModemManager/2023-08-08-16_16_45.log) | Failed to restart ModemManager.service: Unit polkit.service not found，镜像没有预装 polkit 软件包 |
| amanda | oe_test_amanda_ambackup | fail | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_ambackup/2023-08-12-20_44_00.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_ambackup/2023-08-12-20_44_00.log) |  |
|  | oe_test_amanda_amssl | fail | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_amssl/2023-08-12-20_46_05.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_amssl/2023-08-12-20_46_05.log) |  |
| asciidoc | oe_test_asciidoc_a2x_03 | fail | [./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_03/2023-08-08-10_35_01.log](./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_03/2023-08-08-10_35_01.log) |  |
| bind | oe_test_service_named | fail | [./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named/2023-08-09-04_13_07.log](./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named/2023-08-09-04_13_07.log) | 源中无bind软件包，可能尚未构建完成 |
|  | oe_test_service_named-chroot | fail | [./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-chroot/2023-08-09-04_10_55.log](./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-chroot/2023-08-09-04_10_55.log) | 源中无bind软件包，可能尚未构建完成 |
|  | oe_test_service_named-chroot-setup | fail | [./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-chroot-setup/2023-08-09-04_09_35.log](./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-chroot-setup/2023-08-09-04_09_35.log) | 源中无bind软件包，可能尚未构建完成 |
| clevis | oe_test_tang_nbde | fail | [./oe-rv2309/mugen-riscv/logs/clevis/oe_test_tang_nbde/2023-08-08-13_49_19.log](./oe-rv2309/mugen-riscv/logs/clevis/oe_test_tang_nbde/2023-08-08-13_49_19.log) | 软件包 clevis 安装依赖出错 nothing provides libcrypto.so.1.1()(64bit) libcrypto.so.1.1(OPENSSL_1_1_0)(64bit) |
| cockpit | oe_test_service_cockpit-wsinstance-http | fail | [./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit-wsinstance-http/2023-08-08-10_39_01.log](./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit-wsinstance-http/2023-08-08-10_39_01.log) |  |
| dbus | oe_test_socket_dbus | fail | [./oe-rv2309/mugen-riscv/logs/dbus/oe_test_socket_dbus/2023-08-08-11_14_29.log](./oe-rv2309/mugen-riscv/logs/dbus/oe_test_socket_dbus/2023-08-08-11_14_29.log) | 重新测试通过 |
| dnf | oe_test_dnf_check_diffenert-packages | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_check_diffenert-packages/2023-08-08-11_15_02.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_check_diffenert-packages/2023-08-08-11_15_02.log) | dnf check-update 异常推出，返回值 100 ，没有打印报错信息 |
| fio | oe_test_fio_002 | fail | [./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_002/2023-08-08-14_28_48.log](./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_002/2023-08-08-14_28_48.log) | 测试多个 fio-dedupe 命令测试失败，原因未知 |
|  | oe_test_fio_003 | fail | [./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_003/2023-08-08-15_05_36.log](./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_003/2023-08-08-15_05_36.log) | 测试超时，手动重测通过 |
| gnome-shell | oe_test_gnome-shell | fail | [./oe-rv2309/mugen-riscv/logs/gnome-shell/oe_test_gnome-shell/2023-08-09-05_00_14.log](./oe-rv2309/mugen-riscv/logs/gnome-shell/oe_test_gnome-shell/2023-08-09-05_00_14.log) | 软件包安装时发生依赖错误 |
| gsl | oe_test_gsl_histogram_01 | fail | [./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_histogram_01/2023-08-09-04_04_19.log](./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_histogram_01/2023-08-09-04_04_19.log) | 2309中gsl对应版本为2.7.1，软件命令行使用格式有所改变，但mugen脚本没有更新 |
| iperf3 | oe_test_iperf3_command_clientAndShared | fail | [./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_clientAndShared/2023-08-12-22_28_55.log](./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_clientAndShared/2023-08-12-22_28_55.log) |  |
|  | oe_test_iperf3_command_serverAndBase | fail | [./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_serverAndBase/2023-08-12-22_29_59.log](./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_serverAndBase/2023-08-12-22_29_59.log) |  |
| ipvsadm | oe_test_service_ipvsadm | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_service_ipvsadm/2023-08-12-16_57_22.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_service_ipvsadm/2023-08-12-16_57_22.log) |  |
| kernel | oe_test_bnx2fc | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_bnx2fc/2023-08-08-11_25_04.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_bnx2fc/2023-08-08-11_25_04.log) | 2303 未测试该测试用例，内核模块 scsi/hpsa.ko 不存在 |
| kpatch | oe_test_service_kpatch | fail | [./oe-rv2309/mugen-riscv/logs/kpatch/oe_test_service_kpatch/2023-08-09-05_01_03.log](./oe-rv2309/mugen-riscv/logs/kpatch/oe_test_service_kpatch/2023-08-09-05_01_03.log) | 源中无对应软件包 |
| libosinfo | oe_test_osinfo-install-script | fail | [./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-install-script/2023-08-08-10_50_25.log](./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-install-script/2023-08-08-10_50_25.log) | 重新测试通过 |
| libstoragemgmt | oe_test_service_libstoragemgmt | fail | [./oe-rv2309/mugen-riscv/logs/libstoragemgmt/oe_test_service_libstoragemgmt/2023-08-08-11_43_59.log](./oe-rv2309/mugen-riscv/logs/libstoragemgmt/oe_test_service_libstoragemgmt/2023-08-08-11_43_59.log) | 重新测试通过 |
| libvirt | oe_test_socket_libvirtd | fail | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd/2023-08-09-02_43_13.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd/2023-08-09-02_43_13.log) | 手动无法复现 |
|  | oe_test_socket_libvirtd-admin | fail | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-admin/2023-08-09-02_41_23.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-admin/2023-08-09-02_41_23.log) | 手动无法复现 |
|  | oe_test_socket_libvirtd-ro | fail | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-ro/2023-08-09-02_42_18.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-ro/2023-08-09-02_42_18.log) | 手动无法复现 |
|  | oe_test_socket_virtlockd | fail | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlockd/2023-08-09-02_51_20.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlockd/2023-08-09-02_51_20.log) | 手动无法复现 |
|  | oe_test_socket_virtlogd | fail | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlogd/2023-08-09-02_53_06.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlogd/2023-08-09-02_53_06.log) | 手动无法复现 |
| libvma | oe_test_service_vma | fail | [./oe-rv2309/mugen-riscv/logs/libvma/oe_test_service_vma/2023-08-13-03_31_57.log](./oe-rv2309/mugen-riscv/logs/libvma/oe_test_service_vma/2023-08-13-03_31_57.log) |  |
| lorax | oe_test_service_lorax-composer | fail | [./oe-rv2309/mugen-riscv/logs/lorax/oe_test_service_lorax-composer/2023-08-13-01_05_10.log](./oe-rv2309/mugen-riscv/logs/lorax/oe_test_service_lorax-composer/2023-08-13-01_05_10.log) |  |
| lvm2 | oe_test_lvm2_pvchange_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvchange_001/2023-08-09-03_30_44.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvchange_001/2023-08-09-03_30_44.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_pvdisplay_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvdisplay_001/2023-08-09-03_28_56.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvdisplay_001/2023-08-09-03_28_56.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_pvdisplay_002 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvdisplay_002/2023-08-09-03_26_48.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvdisplay_002/2023-08-09-03_26_48.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_pvremove_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvremove_001/2023-08-09-03_22_22.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvremove_001/2023-08-09-03_22_22.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgchange_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgchange_001/2023-08-09-03_31_22.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgchange_001/2023-08-09-03_31_22.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgchange_002 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgchange_002/2023-08-09-03_35_32.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgchange_002/2023-08-09-03_35_32.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgcreate_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgcreate_001/2023-08-09-03_25_49.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgcreate_001/2023-08-09-03_25_49.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgdisplay_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgdisplay_001/2023-08-09-03_27_29.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgdisplay_001/2023-08-09-03_27_29.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgexport_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgexport_001/2023-08-09-03_34_37.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgexport_001/2023-08-09-03_34_37.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgextend_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgextend_001/2023-08-09-03_33_20.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgextend_001/2023-08-09-03_33_20.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgextend_002 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgextend_002/2023-08-09-03_32_23.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgextend_002/2023-08-09-03_32_23.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
|  | oe_test_lvm2_vgrename_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgrename_001/2023-08-09-03_28_14.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_vgrename_001/2023-08-09-03_28_14.log) | mugen中suite2cases文档中无add disk信息，2303riscv为重测通过 |
| lxc | oe_test_lxc_unshare_update | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_unshare_update/2023-08-08-03_30_15.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_unshare_update/2023-08-08-03_30_15.log) |  |
| lxcfs | oe_test_service_lxcfs | fail | [./oe-rv2309/mugen-riscv/logs/lxcfs/oe_test_service_lxcfs/2023-08-08-16_50_22.log](./oe-rv2309/mugen-riscv/logs/lxcfs/oe_test_service_lxcfs/2023-08-08-16_50_22.log) | 软件包 lxcfs-tools 安装依赖出错，nothing provides libabsl_synchronization.so.2206.0.0()(64bit) needed by iSulad-2.1.2-4.oe2309.riscv64 |
| net-tools | oe_test_net-tools_ipmaddr | fail | [./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_ipmaddr/2023-08-12-22_21_35.log](./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_ipmaddr/2023-08-12-22_21_35.log) |  |
| netcf | oe_test_service_netcf-transaction | fail | [./oe-rv2309/mugen-riscv/logs/netcf/oe_test_service_netcf-transaction/2023-08-08-11_47_03.log](./oe-rv2309/mugen-riscv/logs/netcf/oe_test_service_netcf-transaction/2023-08-08-11_47_03.log) | 重新测试通过 |
| openscap | oe_test_ensure_security_compliance | fail | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_ensure_security_compliance/2023-08-08-21_04_08.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_ensure_security_compliance/2023-08-08-21_04_08.log) | 2309中找不到openscap软件包、没有scap-security-guide的依赖 |
| openssh | oe_test_openssh_ssh-copy-id | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh-copy-id/2023-08-11-00_35_09.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh-copy-id/2023-08-11-00_35_09.log) | ssh-keygen 没有生成 pubkey 导致 /usr/bin/ssh-copy-id: ERROR: failed to open ID file '/root/.ssh/id_rsa.pub': No such file or directory |
| os-basic | oe_test_IOaccess_1Gfile | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_1Gfile/2023-08-08-21_11_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_1Gfile/2023-08-08-21_11_39.log) | 测试超时，手动重测通过 |
|  | oe_test_IOaccess_500Mfile | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_500Mfile/2023-08-08-20_23_20.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_500Mfile/2023-08-08-20_23_20.log) | 测试超时，手动重测通过 |
|  | oe_test_ProcMgmt_at | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_at/2023-08-08-19_48_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_at/2023-08-08-19_48_34.log) | systemd-253-1 问题 |
|  | oe_test_ProcMgmt_sar | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_sar/2023-08-08-19_38_11.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_sar/2023-08-08-19_38_11.log) | systemd-253-1 问题 |
|  | oe_test_chrony_Manuall | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_Manuall/2023-08-08-20_19_15.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_Manuall/2023-08-08-20_19_15.log) | systemd-253-1 问题 |
|  | oe_test_chrony_chronyc_cmd | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_cmd/2023-08-08-20_09_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_cmd/2023-08-08-20_09_34.log) | systemd-253-1 问题 |
|  | oe_test_chrony_chronyc_hardwaretime | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_hardwaretime/2023-08-08-20_11_53.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_hardwaretime/2023-08-08-20_11_53.log) | systemd-253-1 问题 |
|  | oe_test_chrony_chronyc_ntpstat | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_ntpstat/2023-08-08-20_14_15.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyc_ntpstat/2023-08-08-20_14_15.log) | systemd-253-1 问题 |
|  | oe_test_chrony_chronyd | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyd/2023-08-08-20_16_46.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chrony_chronyd/2023-08-08-20_16_46.log) | systemd-253-1 问题 |
|  | oe_test_date | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_date/2023-08-08-19_59_22.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_date/2023-08-08-19_59_22.log) | systemd-253-1 问题 |
|  | oe_test_disk_tuned_disable | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_disable/2023-08-08-20_02_43.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_disable/2023-08-08-20_02_43.log) | systemd-253-1 问题 |
|  | oe_test_disk_tuned_install | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_install/2023-08-08-19_57_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_install/2023-08-08-19_57_34.log) | systemd-253-1 问题 |
|  | oe_test_disk_tuned_modify | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_modify/2023-08-08-20_00_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_modify/2023-08-08-20_00_48.log) | systemd-253-1 问题 |
|  | oe_test_disk_tuned_new | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_new/2023-08-08-19_55_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_new/2023-08-08-19_55_39.log) | systemd-253-1 问题 |
|  | oe_test_disk_tuned_set | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_set/2023-08-08-19_51_46.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_tuned_set/2023-08-08-19_51_46.log) | systemd-253-1 问题 |
|  | oe_test_expect | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expect/2023-08-08-22_59_17.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expect/2023-08-08-22_59_17.log) | 软件源问题 |
|  | oe_test_gcc_01 | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gcc_01/2023-08-09-00_01_09.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gcc_01/2023-08-09-00_01_09.log) | gcc 警告信息变化导致 grep 失败 |
|  | oe_test_glibc | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_glibc/2023-08-08-23_50_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_glibc/2023-08-08-23_50_28.log) | glibc 升级导致的问题 |
|  | oe_test_hostnamectl | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hostnamectl/2023-08-08-20_55_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hostnamectl/2023-08-08-20_55_30.log) | systemd 问题 |
|  | oe_test_kernel_sysctl | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_sysctl/2023-08-08-20_54_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_sysctl/2023-08-08-20_54_37.log) | systemd-253-1 问题 |
|  | oe_test_localectl | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_localectl/2023-08-08-21_55_56.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_localectl/2023-08-08-21_55_56.log) | systemd-253-1 问题 |
|  | oe_test_net_config | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_config/2023-08-08-22_04_50.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_config/2023-08-08-22_04_50.log) | systemd-253-1 问题 |
|  | oe_test_net_mtr | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_mtr/2023-08-08-22_35_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_mtr/2023-08-08-22_35_29.log) | systemd-253-1 问题 |
|  | oe_test_net_ncat | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_ncat/2023-08-08-22_10_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_ncat/2023-08-08-22_10_07.log) | systemd-253-1 问题 |
|  | oe_test_nmcli_cancel_auto | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_cancel_auto/2023-08-08-19_23_00.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_cancel_auto/2023-08-08-19_23_00.log) | systemd-253-1 问题 |
|  | oe_test_nmcli_systemd_resolved | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_systemd_resolved/2023-08-08-19_08_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_systemd_resolved/2023-08-08-19_08_07.log) | systemd-253-1 问题 |
|  | oe_test_password_change | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_password_change/2023-08-08-19_05_05.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_password_change/2023-08-08-19_05_05.log) | systemd-253-1 问题 |
|  | oe_test_power_measurement_internal | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_measurement_internal/2023-08-08-18_56_15.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_measurement_internal/2023-08-08-18_56_15.log) | powertop 失败 |
|  | oe_test_power_powertop2tuned_optimize | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop2tuned_optimize/2023-08-08-18_51_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop2tuned_optimize/2023-08-08-18_51_37.log) | 无法安装 dmidecode 软件包 |
|  | oe_test_power_powertop_powerup | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_powerup/2023-08-08-18_46_53.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_powerup/2023-08-08-18_46_53.log) | powertop 失败 |
|  | oe_test_server_httpd_checkfirewall | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_checkfirewall/2023-08-08-16_05_33.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_checkfirewall/2023-08-08-16_05_33.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_backup | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backup/2023-08-08-15_29_31.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backup/2023-08-08-15_29_31.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_backupDB | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backupDB/2023-08-08-15_25_58.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backupDB/2023-08-08-15_25_58.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_backuptable | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backuptable/2023-08-08-15_21_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_backuptable/2023-08-08-15_21_29.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_copy | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_copy/2023-08-08-15_16_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_copy/2023-08-08-15_16_55.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_dump | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_dump/2023-08-08-15_12_22.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_dump/2023-08-08-15_12_22.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_dumpMulDB | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_dumpMulDB/2023-08-08-15_08_10.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_dumpMulDB/2023-08-08-15_08_10.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_loadfile | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_loadfile/2023-08-08-14_58_53.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_loadfile/2023-08-08-14_58_53.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_onlinebackup | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_onlinebackup/2023-08-08-14_53_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_onlinebackup/2023-08-08-14_53_48.log) | systemd-253-1 问题 |
|  | oe_test_server_mariadb_stop | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_stop/2023-08-08-14_48_45.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_stop/2023-08-08-14_48_45.log) | systemd-253-1 问题 |
|  | oe_test_server_mysql | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mysql/2023-08-08-14_17_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mysql/2023-08-08-14_17_48.log) | 测试超时，手动重测通过 |
|  | oe_test_server_openssh_secure | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_secure/2023-08-08-13_52_50.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_secure/2023-08-08-13_52_50.log) | 测试失败 |
|  | oe_test_server_squid_ip | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_ip/2023-08-08-13_09_40.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_ip/2023-08-08-13_09_40.log) | systemd-253-1 问题 |
|  | oe_test_server_squid_proxy | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_proxy/2023-08-08-12_54_52.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_proxy/2023-08-08-12_54_52.log) | systemd-253-1 问题 |
|  | oe_test_timedatectl | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_timedatectl/2023-08-08-11_45_47.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_timedatectl/2023-08-08-11_45_47.log) | systemd-253-1 问题 |
|  | oe_test_vim | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_vim/2023-08-08-22_46_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_vim/2023-08-08-22_46_29.log) | 没有预装 vim 软件包 |
|  | oe_test_virt-what | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_virt-what/2023-08-08-23_59_00.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_virt-what/2023-08-08-23_59_00.log) | virt-what 安装失败 nothing provides dmidecode needed by virt-what-1.25-1.oe2309.riscv64 |
|  | oe_test_who | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_who/2023-08-08-10_55_58.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_who/2023-08-08-10_55_58.log) | 原因未知 |
| os-storage | oe_test_storage_DevMgmt_fstrim | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_fstrim/2023-08-13-13_29_38.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_fstrim/2023-08-13-13_29_38.log) |  |
|  | oe_test_storage_diskpartiton_parted_create | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_create/2023-08-13-15_14_52.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_create/2023-08-13-15_14_52.log) |  |
|  | oe_test_storage_diskpartiton_parted_delete | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_delete/2023-08-13-12_23_42.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_delete/2023-08-13-12_23_42.log) |  |
|  | oe_test_storage_diskpartiton_parted_view | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_view/2023-08-13-13_22_52.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_view/2023-08-13-13_22_52.log) |  |
|  | oe_test_storage_ext3_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_mount/2023-08-13-12_04_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_mount/2023-08-13-12_04_03.log) |  |
|  | oe_test_storage_ext4_create | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_create/2023-08-13-12_41_01.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_create/2023-08-13-12_41_01.log) |  |
|  | oe_test_storage_ext4_resize | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_resize/2023-08-13-15_02_33.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_resize/2023-08-13-15_02_33.log) |  |
|  | oe_test_storage_lvm_VG_transfer | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_VG_transfer/2023-08-13-14_28_00.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_VG_transfer/2023-08-13-14_28_00.log) |  |
|  | oe_test_storage_lvm_activation | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_activation/2023-08-13-15_15_47.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_activation/2023-08-13-15_15_47.log) |  |
|  | oe_test_storage_lvm_print | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print/2023-08-13-13_20_47.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print/2023-08-13-13_20_47.log) |  |
|  | oe_test_storage_lvm_print_history | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print_history/2023-08-13-14_38_30.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print_history/2023-08-13-14_38_30.log) |  |
|  | oe_test_storage_lvm_pvs | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_pvs/2023-08-13-12_30_02.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_pvs/2023-08-13-12_30_02.log) |  |
|  | oe_test_storage_lvm_rename | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_rename/2023-08-13-14_08_27.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_rename/2023-08-13-14_08_27.log) |  |
|  | oe_test_storage_lvm_rename_VG | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_rename_VG/2023-08-13-12_35_27.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_rename_VG/2023-08-13-12_35_27.log) |  |
|  | oe_test_storage_lvm_resize_PV | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_resize_PV/2023-08-13-13_12_42.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_resize_PV/2023-08-13-13_12_42.log) |  |
|  | oe_test_storage_lvm_set_lvconvert_volume | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_volume/2023-08-13-12_59_36.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_volume/2023-08-13-12_59_36.log) |  |
|  | oe_test_storage_mount_private | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_private/2023-08-13-15_21_16.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_private/2023-08-13-15_21_16.log) |  |
|  | oe_test_storage_nfs_mount_RW | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_RW/2023-08-13-13_53_46.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_RW/2023-08-13-13_53_46.log) |  |
|  | oe_test_storage_nfs_repeat_restart | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_restart/2023-08-13-12_31_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_restart/2023-08-13-12_31_03.log) |  |
| pcp | oe_test_pcp_pcp-import-iostat2pcp | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-iostat2pcp/2023-08-12-14_30_36.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-iostat2pcp/2023-08-12-14_30_36.log) |  |
|  | oe_test_pcp_pcp-import-mrtg2pcp | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-mrtg2pcp/2023-08-12-14_35_02.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-mrtg2pcp/2023-08-12-14_35_02.log) |  |
|  | oe_test_pcp_pcp-import-sar2pcp | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-sar2pcp/2023-08-12-14_39_43.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-sar2pcp/2023-08-12-14_39_43.log) |  |
|  | oe_test_service_pmlogger | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmlogger/2023-08-12-14_47_09.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmlogger/2023-08-12-14_47_09.log) |  |
| perl-libwww-perl | oe_test_perl-libwww-perl_GET_01 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_GET_01/2023-08-08-12_05_36.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_GET_01/2023-08-08-12_05_36.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_GET_02 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_GET_02/2023-08-08-12_10_42.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_GET_02/2023-08-08-12_10_42.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_HEAD_01 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_HEAD_01/2023-08-08-12_15_17.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_HEAD_01/2023-08-08-12_15_17.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_HEAD_02 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_HEAD_02/2023-08-08-12_20_09.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_HEAD_02/2023-08-08-12_20_09.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_POST_01 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_POST_01/2023-08-08-12_40_23.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_POST_01/2023-08-08-12_40_23.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_POST_02 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_POST_02/2023-08-08-12_45_35.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_POST_02/2023-08-08-12_45_35.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_lwp-dump | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp-dump/2023-08-08-12_25_13.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp-dump/2023-08-08-12_25_13.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_lwp_request_01 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp_request_01/2023-08-08-12_30_32.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp_request_01/2023-08-08-12_30_32.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
|  | oe_test_perl-libwww-perl_lwp_request_02 | fail | [./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp_request_02/2023-08-08-12_35_45.log](./oe-rv2309/mugen-riscv/logs/perl-libwww-perl/oe_test_perl-libwww-perl_lwp_request_02/2023-08-08-12_35_45.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
| polkit | oe_test_service_polkit | fail | [./oe-rv2309/mugen-riscv/logs/polkit/oe_test_service_polkit/2023-08-09-05_09_32.log](./oe-rv2309/mugen-riscv/logs/polkit/oe_test_service_polkit/2023-08-09-05_09_32.log) | 软件polkit未安装 |
| python-blivet | oe_test_service_blivet | fail | [./oe-rv2309/mugen-riscv/logs/python-blivet/oe_test_service_blivet/2023-08-13-04_05_37.log](./oe-rv2309/mugen-riscv/logs/python-blivet/oe_test_service_blivet/2023-08-13-04_05_37.log) |  |
| qemu | oe_test_service_qemu-pr-helper | fail | [./oe-rv2309/mugen-riscv/logs/qemu/oe_test_service_qemu-pr-helper/2023-08-12-23_56_29.log](./oe-rv2309/mugen-riscv/logs/qemu/oe_test_service_qemu-pr-helper/2023-08-12-23_56_29.log) |  |
|  | oe_test_socket_qemu-pr-helper | fail | [./oe-rv2309/mugen-riscv/logs/qemu/oe_test_socket_qemu-pr-helper/2023-08-12-23_58_56.log](./oe-rv2309/mugen-riscv/logs/qemu/oe_test_socket_qemu-pr-helper/2023-08-12-23_58_56.log) |  |
| quota | oe_test_service_rpc-rquotad | fail | [./oe-rv2309/mugen-riscv/logs/quota/oe_test_service_rpc-rquotad/2023-08-08-11_21_35.log](./oe-rv2309/mugen-riscv/logs/quota/oe_test_service_rpc-rquotad/2023-08-08-11_21_35.log) | 重新测试通过 |
| rpmdevtools | oe_test_rpmdevtools_rpmdev-rmdevelrpms | fail | [./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-rmdevelrpms/2023-08-09-04_09_56.log](./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-rmdevelrpms/2023-08-09-04_09_56.log) | 软件yumdownloader行为异常，导致下载失败 |
| rpmlint | oe_test_rpmdiff | fail | [./oe-rv2309/mugen-riscv/logs/rpmlint/oe_test_rpmdiff/2023-08-09-04_46_08.log](./oe-rv2309/mugen-riscv/logs/rpmlint/oe_test_rpmdiff/2023-08-09-04_46_08.log) | rpmlint 软件包安装时发生依赖错误 nothing provides python3.11dist(zstandard) needed by rpmlint-2.4.0-1.oe2309.noarch |
|  | oe_test_rpmlint | fail | [./oe-rv2309/mugen-riscv/logs/rpmlint/oe_test_rpmlint/2023-08-09-04_47_09.log](./oe-rv2309/mugen-riscv/logs/rpmlint/oe_test_rpmlint/2023-08-09-04_47_09.log) | rpmlint 软件包安装时发生依赖错误 nothing provides python3.11dist(zstandard) needed by rpmlint-2.4.0-1.oe2309.noarch |
| rsyslog | oe_test_rsyslog_abnormal_config | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_abnormal_config/2023-08-08-00_45_57.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_abnormal_config/2023-08-08-00_45_57.log) |  |
|  | oe_test_rsyslog_function_discard | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_discard/2023-08-08-00_47_34.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_discard/2023-08-08-00_47_34.log) |  |
|  | oe_test_rsyslog_function_facility | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_facility/2023-08-08-00_47_50.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_facility/2023-08-08-00_47_50.log) |  |
|  | oe_test_rsyslog_function_imfile | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_imfile/2023-08-08-00_50_06.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_imfile/2023-08-08-00_50_06.log) |  |
|  | oe_test_rsyslog_function_priority | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_priority/2023-08-08-00_51_49.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_priority/2023-08-08-00_51_49.log) |  |
|  | oe_test_rsyslog_function_rainerscript | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_rainerscript/2023-08-08-00_52_45.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_rainerscript/2023-08-08-00_52_45.log) |  |
|  | oe_test_rsyslog_function_shell | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_shell/2023-08-08-00_54_10.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_shell/2023-08-08-00_54_10.log) |  |
|  | oe_test_rsyslog_function_template | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_template/2023-08-08-01_25_45.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_template/2023-08-08-01_25_45.log) |  |
|  | oe_test_rsyslog_function_wildcard | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_wildcard/2023-08-08-01_26_41.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_wildcard/2023-08-08-01_26_41.log) |  |
|  | oe_test_rsyslog_parameter01 | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter01/2023-08-08-01_27_07.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter01/2023-08-08-01_27_07.log) |  |
|  | oe_test_rsyslog_parameter02 | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter02/2023-08-08-01_27_22.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter02/2023-08-08-01_27_22.log) |  |
|  | oe_test_rsyslog_parameter03 | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter03/2023-08-08-01_27_35.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter03/2023-08-08-01_27_35.log) |  |
|  | oe_test_rsyslog_parameter04 | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter04/2023-08-08-01_57_42.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_parameter04/2023-08-08-01_57_42.log) |  |
|  | oe_test_rsyslog_reliability_restart | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_restart/2023-08-08-02_28_55.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_restart/2023-08-08-02_28_55.log) |  |
|  | oe_test_service_rsyslog | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_service_rsyslog/2023-08-08-02_32_23.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_service_rsyslog/2023-08-08-02_32_23.log) |  |
| ruby | oe_test_rake_02 | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_02/2023-08-08-11_25_08.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_02/2023-08-08-11_25_08.log) | perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed |
| samba | oe_test_service_ctdb | fail | [./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_ctdb/2023-08-08-13_45_48.log](./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_ctdb/2023-08-08-13_45_48.log) | 未知原因 ctdb.service: Failed to parse PID from file /run/ctdb/ctdbd.pid: Invalid argument |
| smoke-basic-os | oe_test_aide_init_database | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_init_database/2023-08-08-13_29_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_init_database/2023-08-08-13_29_13.log) | 新版aide命令输出发生变化 |
|  | oe_test_cmp | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cmp/2023-08-08-10_59_36.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cmp/2023-08-08-10_59_36.log) | 重新测试通过 |
|  | oe_test_ip6tables_02 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip6tables_02/2023-08-08-11_53_37.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip6tables_02/2023-08-08-11_53_37.log) | 没有预装iptables |
|  | oe_test_iptables | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iptables/2023-08-08-11_55_20.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iptables/2023-08-08-11_55_20.log) | 没有预装iptables |
|  | oe_test_ipv6_dnsmasq | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_dnsmasq/2023-08-08-11_56_27.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_dnsmasq/2023-08-08-11_56_27.log) | 2309中找不到bind-utils软件包 |
|  | oe_test_llvm | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_llvm/2023-08-08-11_00_18.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_llvm/2023-08-08-11_00_18.log) | clang调用ld报错 |
|  | oe_test_log_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_log_001/2023-08-08-10_35_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_log_001/2023-08-08-10_35_48.log) | 没有/var/log/messages目录 |
|  | oe_test_os-prober_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_os-prober_01/2023-08-08-12_17_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_os-prober_01/2023-08-08-12_17_56.log) | 没有预装os-prober |
|  | oe_test_os-prober_02 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_os-prober_02/2023-08-08-12_18_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_os-prober_02/2023-08-08-12_18_04.log) | 没有预装os-prober |
|  | oe_test_rpm_cmd | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpm_cmd/2023-08-08-12_26_28.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpm_cmd/2023-08-08-12_26_28.log) | 命令yum download报错 |
|  | oe_test_rsyslog_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_01/2023-08-08-12_27_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_01/2023-08-08-12_27_34.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_02 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_02/2023-08-08-12_27_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_02/2023-08-08-12_27_56.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_04 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_04/2023-08-08-12_28_17.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_04/2023-08-08-12_28_17.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_05 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_05/2023-08-08-12_28_46.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_05/2023-08-08-12_28_46.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_06 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_06/2023-08-08-12_32_38.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_06/2023-08-08-12_32_38.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_07 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_07/2023-08-08-12_32_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_07/2023-08-08-12_32_56.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_08 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_08/2023-08-08-12_33_07.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_08/2023-08-08-12_33_07.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_09 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_09/2023-08-08-12_33_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_09/2023-08-08-12_33_47.log) | 没有预装rsyslog |
|  | oe_test_rsyslog_10 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_10/2023-08-08-12_34_36.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_10/2023-08-08-12_34_36.log) | date命令输出发生变化 |
|  | oe_test_sort | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sort/2023-08-08-10_47_30.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sort/2023-08-08-10_47_30.log) | 新版sort默认不忽略空行 |
|  | oe_test_user_debug_iotop_SCEN_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_debug_iotop_SCEN_01/2023-08-08-13_57_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_debug_iotop_SCEN_01/2023-08-08-13_57_16.log) | iotop -b -n 1 -d 10命令未能正确显示PRIO（显示为?sys） |
| systemd | oe_test_service_dbus-org.freedesktop.locale1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.locale1/2023-08-08-00_06_16.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.locale1/2023-08-08-00_06_16.log) |  |
|  | oe_test_service_dbus-org.freedesktop.login1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.login1/2023-08-08-00_06_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.login1/2023-08-08-00_06_50.log) |  |
|  | oe_test_service_dbus-org.freedesktop.machine1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.machine1/2023-08-08-00_07_23.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.machine1/2023-08-08-00_07_23.log) |  |
|  | oe_test_service_dbus-org.freedesktop.timedate1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.timedate1/2023-08-08-00_08_39.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.timedate1/2023-08-08-00_08_39.log) |  |
|  | oe_test_service_debug-shell | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_debug-shell/2023-08-08-00_09_13.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_debug-shell/2023-08-08-00_09_13.log) |  |
|  | oe_test_service_hwclock-save | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_hwclock-save/2023-08-08-00_09_47.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_hwclock-save/2023-08-08-00_09_47.log) |  |
|  | oe_test_service_kmod-static-nodes | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_kmod-static-nodes/2023-08-08-00_10_58.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_kmod-static-nodes/2023-08-08-00_10_58.log) |  |
|  | oe_test_service_ldconfig | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_ldconfig/2023-08-08-00_11_31.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_ldconfig/2023-08-08-00_11_31.log) |  |
|  | oe_test_service_rc-local | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_rc-local/2023-08-08-00_13_07.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_rc-local/2023-08-08-00_13_07.log) |  |
|  | oe_test_service_rescue | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_rescue/2023-08-08-00_13_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_rescue/2023-08-08-00_13_42.log) |  |
|  | oe_test_service_system-update-cleanup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_system-update-cleanup/2023-08-08-00_39_51.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_system-update-cleanup/2023-08-08-00_39_51.log) |  |
|  | oe_test_service_systemd-ask-password-console | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-ask-password-console/2023-08-08-00_14_16.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-ask-password-console/2023-08-08-00_14_16.log) |  |
|  | oe_test_service_systemd-hostnamed | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hostnamed/2023-08-08-00_17_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hostnamed/2023-08-08-00_17_42.log) |  |
|  | oe_test_service_systemd-hwdb-update | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hwdb-update/2023-08-08-00_18_15.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hwdb-update/2023-08-08-00_18_15.log) |  |
|  | oe_test_service_systemd-journal-catalog-update | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-catalog-update/2023-08-08-00_20_15.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-catalog-update/2023-08-08-00_20_15.log) |  |
|  | oe_test_service_systemd-journal-flush | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-flush/2023-08-08-00_21_26.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-flush/2023-08-08-00_21_26.log) |  |
|  | oe_test_service_systemd-journald | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journald/2023-08-08-00_20_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journald/2023-08-08-00_20_50.log) |  |
|  | oe_test_service_systemd-localed | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-localed/2023-08-08-00_24_53.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-localed/2023-08-08-00_24_53.log) |  |
|  | oe_test_service_systemd-logind | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-logind/2023-08-08-00_25_27.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-logind/2023-08-08-00_25_27.log) |  |
|  | oe_test_service_systemd-machined | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-machined/2023-08-08-00_26_01.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-machined/2023-08-08-00_26_01.log) |  |
|  | oe_test_service_systemd-random-seed | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-random-seed/2023-08-08-00_29_43.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-random-seed/2023-08-08-00_29_43.log) |  |
|  | oe_test_service_systemd-remount-fs | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-remount-fs/2023-08-08-00_30_26.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-remount-fs/2023-08-08-00_30_26.log) |  |
|  | oe_test_service_systemd-sysctl | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysctl/2023-08-08-00_32_09.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysctl/2023-08-08-00_32_09.log) |  |
|  | oe_test_service_systemd-sysusers | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysusers/2023-08-08-00_32_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysusers/2023-08-08-00_32_42.log) |  |
|  | oe_test_service_systemd-time-wait-sync | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-time-wait-sync/2023-08-08-00_34_25.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-time-wait-sync/2023-08-08-00_34_25.log) |  |
|  | oe_test_service_systemd-timedated | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-timedated/2023-08-08-00_33_18.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-timedated/2023-08-08-00_33_18.log) |  |
|  | oe_test_service_systemd-timesyncd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-timesyncd/2023-08-08-00_33_52.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-timesyncd/2023-08-08-00_33_52.log) |  |
|  | oe_test_service_systemd-tmpfiles-clean | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-clean/2023-08-08-00_34_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-clean/2023-08-08-00_34_59.log) |  |
|  | oe_test_service_systemd-tmpfiles-setup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-setup/2023-08-08-00_35_44.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-setup/2023-08-08-00_35_44.log) |  |
|  | oe_test_service_systemd-tmpfiles-setup-dev | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-setup-dev/2023-08-08-00_35_10.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-tmpfiles-setup-dev/2023-08-08-00_35_10.log) |  |
|  | oe_test_service_systemd-udev-settle | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udev-settle/2023-08-08-00_36_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udev-settle/2023-08-08-00_36_34.log) |  |
|  | oe_test_service_systemd-udev-trigger | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udev-trigger/2023-08-08-00_37_08.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udev-trigger/2023-08-08-00_37_08.log) |  |
|  | oe_test_service_systemd-update-done | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-done/2023-08-08-00_37_22.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-done/2023-08-08-00_37_22.log) |  |
|  | oe_test_service_systemd-update-utmp | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-utmp/2023-08-08-00_38_01.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-utmp/2023-08-08-00_38_01.log) |  |
|  | oe_test_service_systemd-user-sessions | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-user-sessions/2023-08-08-00_38_35.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-user-sessions/2023-08-08-00_38_35.log) |  |
|  | oe_test_service_systemd-vconsole-setup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-vconsole-setup/2023-08-08-00_39_08.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-vconsole-setup/2023-08-08-00_39_08.log) |  |
|  | oe_test_socket_systemd-coredump | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-coredump/2023-08-08-00_41_07.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-coredump/2023-08-08-00_41_07.log) |  |
|  | oe_test_socket_systemd-initctl | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-initctl/2023-08-08-00_41_40.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-initctl/2023-08-08-00_41_40.log) |  |
|  | oe_test_socket_systemd-journald | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald/2023-08-08-00_43_24.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald/2023-08-08-00_43_24.log) |  |
|  | oe_test_socket_systemd-journald-audit | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald-audit/2023-08-08-00_42_14.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald-audit/2023-08-08-00_42_14.log) |  |
|  | oe_test_socket_systemd-udevd-control | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-udevd-control/2023-08-08-00_47_01.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-udevd-control/2023-08-08-00_47_01.log) |  |
|  | oe_test_socket_systemd-udevd-kernel | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-udevd-kernel/2023-08-08-00_47_37.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-udevd-kernel/2023-08-08-00_47_37.log) |  |
|  | oe_test_target_basic | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_basic/2023-08-08-00_48_12.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_basic/2023-08-08-00_48_12.log) |  |
|  | oe_test_target_boot-complete | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_boot-complete/2023-08-08-00_48_46.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_boot-complete/2023-08-08-00_48_46.log) |  |
|  | oe_test_target_default | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_default/2023-08-08-00_50_17.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_default/2023-08-08-00_50_17.log) |  |
|  | oe_test_target_first-boot-complete | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_first-boot-complete/2023-08-08-01_12_43.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_first-boot-complete/2023-08-08-01_12_43.log) |  |
|  | oe_test_target_getty | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_getty/2023-08-08-00_51_46.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_getty/2023-08-08-00_51_46.log) |  |
|  | oe_test_target_getty-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_getty-pre/2023-08-08-00_51_11.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_getty-pre/2023-08-08-00_51_11.log) |  |
|  | oe_test_target_graphical | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_graphical/2023-08-08-00_52_21.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_graphical/2023-08-08-00_52_21.log) |  |
|  | oe_test_target_initrd-fs | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-fs/2023-08-08-00_53_25.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-fs/2023-08-08-00_53_25.log) |  |
|  | oe_test_target_initrd-root-device | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-root-device/2023-08-08-00_54_01.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-root-device/2023-08-08-00_54_01.log) |  |
|  | oe_test_target_initrd-root-fs | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-root-fs/2023-08-08-00_54_36.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-root-fs/2023-08-08-00_54_36.log) |  |
|  | oe_test_target_local-fs | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_local-fs/2023-08-08-00_55_53.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_local-fs/2023-08-08-00_55_53.log) |  |
|  | oe_test_target_local-fs-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_local-fs-pre/2023-08-08-00_55_41.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_local-fs-pre/2023-08-08-00_55_41.log) |  |
|  | oe_test_target_machines | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_machines/2023-08-08-00_56_27.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_machines/2023-08-08-00_56_27.log) |  |
|  | oe_test_target_multi-user | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_multi-user/2023-08-08-00_57_02.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_multi-user/2023-08-08-00_57_02.log) |  |
|  | oe_test_target_network | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network/2023-08-08-00_58_22.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network/2023-08-08-00_58_22.log) |  |
|  | oe_test_target_network-online | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network-online/2023-08-08-00_57_36.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network-online/2023-08-08-00_57_36.log) |  |
|  | oe_test_target_network-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network-pre/2023-08-08-00_58_10.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_network-pre/2023-08-08-00_58_10.log) |  |
|  | oe_test_target_nss-lookup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_nss-lookup/2023-08-08-00_58_35.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_nss-lookup/2023-08-08-00_58_35.log) |  |
|  | oe_test_target_nss-user-lookup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_nss-user-lookup/2023-08-08-00_58_47.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_nss-user-lookup/2023-08-08-00_58_47.log) |  |
|  | oe_test_target_paths | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_paths/2023-08-08-00_58_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_paths/2023-08-08-00_58_59.log) |  |
|  | oe_test_target_remote-fs | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-fs/2023-08-08-01_00_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-fs/2023-08-08-01_00_42.log) |  |
|  | oe_test_target_remote-fs-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-fs-pre/2023-08-08-01_00_29.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-fs-pre/2023-08-08-01_00_29.log) |  |
|  | oe_test_target_rescue | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_rescue/2023-08-08-01_01_16.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_rescue/2023-08-08-01_01_16.log) |  |
|  | oe_test_target_rpcbind | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_rpcbind/2023-08-08-01_01_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_rpcbind/2023-08-08-01_01_50.log) |  |
|  | oe_test_target_runlevel1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel1/2023-08-08-01_02_13.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel1/2023-08-08-01_02_13.log) |  |
|  | oe_test_target_runlevel2 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel2/2023-08-08-01_02_48.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel2/2023-08-08-01_02_48.log) |  |
|  | oe_test_target_runlevel3 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel3/2023-08-08-01_03_22.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel3/2023-08-08-01_03_22.log) |  |
|  | oe_test_target_runlevel4 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel4/2023-08-08-01_03_55.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel4/2023-08-08-01_03_55.log) |  |
|  | oe_test_target_runlevel5 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel5/2023-08-08-01_04_29.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel5/2023-08-08-01_04_29.log) |  |
|  | oe_test_target_sigpwr | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sigpwr/2023-08-08-01_05_23.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sigpwr/2023-08-08-01_05_23.log) |  |
|  | oe_test_target_slices | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_slices/2023-08-08-01_06_07.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_slices/2023-08-08-01_06_07.log) |  |
|  | oe_test_target_sockets | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sockets/2023-08-08-01_06_41.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sockets/2023-08-08-01_06_41.log) |  |
|  | oe_test_target_swap | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_swap/2023-08-08-01_07_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_swap/2023-08-08-01_07_34.log) |  |
|  | oe_test_target_sysinit | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sysinit/2023-08-08-01_08_08.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sysinit/2023-08-08-01_08_08.log) |  |
|  | oe_test_target_system-update | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_system-update/2023-08-08-01_08_54.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_system-update/2023-08-08-01_08_54.log) |  |
|  | oe_test_target_system-update-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_system-update-pre/2023-08-08-01_08_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_system-update-pre/2023-08-08-01_08_42.log) |  |
|  | oe_test_target_time-set | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_time-set/2023-08-08-01_10_03.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_time-set/2023-08-08-01_10_03.log) |  |
|  | oe_test_target_time-sync | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_time-sync/2023-08-08-01_10_15.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_time-sync/2023-08-08-01_10_15.log) |  |
|  | oe_test_target_timers | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_timers/2023-08-08-01_09_29.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_timers/2023-08-08-01_09_29.log) |  |
|  | oe_test_target_umount | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_umount/2023-08-08-01_10_27.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_umount/2023-08-08-01_10_27.log) |  |
| timedatex | oe_test_service_timedatex | fail | [./oe-rv2309/mugen-riscv/logs/timedatex/oe_test_service_timedatex/2023-08-09-05_14_00.log](./oe-rv2309/mugen-riscv/logs/timedatex/oe_test_service_timedatex/2023-08-09-05_14_00.log) | timedatex对应软件包未安装 |
| tog-pegasus | oe_test_service_tog-pegasus | fail | [./oe-rv2309/mugen-riscv/logs/tog-pegasus/oe_test_service_tog-pegasus/2023-08-08-12_02_40.log](./oe-rv2309/mugen-riscv/logs/tog-pegasus/oe_test_service_tog-pegasus/2023-08-08-12_02_40.log) | 2309中找不到tog-pegasus软件包 |
| valgrind | oe_test_valgrind | fail | [./oe-rv2309/mugen-riscv/logs/valgrind/oe_test_valgrind/2023-08-08-12_05_24.log](./oe-rv2309/mugen-riscv/logs/valgrind/oe_test_valgrind/2023-08-08-12_05_24.log) | 2309中找不到valgrind软件包和glibc-debuginfo软件包 |




## BaseOS 在 23.09 和 23.03 版本下均失败用例

下表内的测试套和测试用例均为在 23.03 上和 23.09 上均失败的 BaseOS 测试用例，且原因相同，此部分详细分析可到[23.03-test] (https://gitee.com/yunxiangluo/oerv-2303-test/tree/master/BasicTest/function/mugen)查找

| 测试套/软件包名 | 测试用例名 | 状态 | 日志文件 | 原因 | 
| :---: | :---: | :---: | :---: | :---: | 
| FS_File | oe_test_FSIO_act_file_lack_inode | fail | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_file_lack_inode/2023-08-09-02_14_17.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_file_lack_inode/2023-08-09-02_14_17.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_chmod | fail | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chmod/2023-08-09-02_18_47.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chmod/2023-08-09-02_18_47.log) | mugen脚本问题，检测的字符多了个点 |
|  | oe_test_FSIO_filefrag | fail | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_filefrag/2023-08-09-02_33_23.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_filefrag/2023-08-09-02_33_23.log) | preinstall absent，软件xfsprogs未安装 |
| FS_FileSystem | oe_test_FSIO_check_fs_type | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_check_fs_type/2023-08-11-00_22_04.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_check_fs_type/2023-08-11-00_22_04.log) | The system doesn't support ext4. |
| acl | oe_test_access_providepam | fail | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_access_providepam/2023-08-08-09_52_11.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_access_providepam/2023-08-08-09_52_11.log) | pam_systemd.so不存在 |
| amanda | oe_test_amanda_amcheck | fail | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_amcheck/2023-08-12-20_37_26.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_amcheck/2023-08-12-20_37_26.log) |  |
|  | oe_test_amanda_dump | fail | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_dump/2023-08-12-20_54_56.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_dump/2023-08-12-20_54_56.log) |  |
| anaconda | oe_test_service_zram | fail | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_zram/2023-08-08-03_51_46.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_zram/2023-08-08-03_51_46.log) |  |
| audit | oe_test_audit_ausearch | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_ausearch/2025-08-09-03_30_50.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_ausearch/2025-08-09-03_30_50.log) | 软件未安装 |
|  | oe_test_audit_available_disk_space | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_available_disk_space/2023-08-09-03_21_32.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_available_disk_space/2023-08-09-03_21_32.log) | 软件未安装 |
|  | oe_test_audit_count_number_of_event | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_count_number_of_event/2023-08-09-03_02_05.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_count_number_of_event/2023-08-09-03_02_05.log) | 软件未安装 |
|  | oe_test_audit_count_time | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_count_time/2023-08-09-03_01_50.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_count_time/2023-08-09-03_01_50.log) | 软件未安装 |
|  | oe_test_audit_event_log | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_event_log/2025-08-09-03_29_41.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_event_log/2025-08-09-03_29_41.log) | 软件未安装 |
|  | oe_test_audit_fetch_file_in_order | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_fetch_file_in_order/2023-08-09-03_00_44.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_fetch_file_in_order/2023-08-09-03_00_44.log) | 软件未安装 |
|  | oe_test_audit_max_log_file_ignore | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_ignore/2023-08-09-03_05_20.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_ignore/2023-08-09-03_05_20.log) | 软件未安装 |
|  | oe_test_audit_max_log_file_keep_logs | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_keep_logs/2023-08-09-03_20_11.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_keep_logs/2023-08-09-03_20_11.log) | 软件未安装 |
|  | oe_test_audit_max_log_file_rotate | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_rotate/2023-08-09-03_04_01.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_rotate/2023-08-09-03_04_01.log) | 软件未安装 |
|  | oe_test_audit_max_log_file_suspend | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_suspend/2023-08-09-03_18_21.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_suspend/2023-08-09-03_18_21.log) | 软件未安装 |
|  | oe_test_audit_max_log_file_syslog | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_syslog/2023-08-09-03_16_30.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_max_log_file_syslog/2023-08-09-03_16_30.log) | 软件未安装 |
|  | oe_test_audit_monitor_dictionary_access | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_dictionary_access/2023-08-09-02_56_10.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_dictionary_access/2023-08-09-02_56_10.log) | 软件未安装 |
|  | oe_test_audit_monitor_do_command | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_do_command/2023-08-09-02_57_00.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_do_command/2023-08-09-02_57_00.log) | 软件未安装 |
|  | oe_test_audit_monitor_file_access | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_file_access/2023-08-09-02_25_59.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_file_access/2023-08-09-02_25_59.log) | 软件未安装 |
|  | oe_test_audit_monitor_network_visit | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_network_visit/2023-08-09-02_57_59.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_network_visit/2023-08-09-02_57_59.log) | 软件未安装 |
|  | oe_test_audit_monitor_system_use | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_system_use/2023-08-09-02_56_33.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_system_use/2023-08-09-02_56_33.log) | 软件未安装 |
|  | oe_test_audit_other | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_other/2025-08-09-03_30_01.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_other/2025-08-09-03_30_01.log) | 软件未安装 |
|  | oe_test_audit_rule_conflict_strategy | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_conflict_strategy/2023-08-09-02_59_56.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_conflict_strategy/2023-08-09-02_59_56.log) | 软件未安装 |
|  | oe_test_audit_rule_contact_strategy | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_contact_strategy/2023-08-09-02_59_12.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_contact_strategy/2023-08-09-02_59_12.log) | 软件未安装 |
|  | oe_test_audit_rule_fetch_from_rule | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_fetch_from_rule/2023-08-09-03_00_25.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_rule_fetch_from_rule/2023-08-09-03_00_25.log) | 软件未安装 |
|  | oe_test_audit_show_event_list | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_show_event_list/2023-08-09-03_02_20.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_show_event_list/2023-08-09-03_02_20.log) | 软件未安装 |
|  | oe_test_audit_track_designated_access | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_track_designated_access/2023-08-09-02_58_19.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_track_designated_access/2023-08-09-02_58_19.log) | 软件未安装 |
|  | oe_test_audit_use_d_audit | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_use_d_audit/2023-08-09-02_58_35.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_use_d_audit/2023-08-09-02_58_35.log) | 软件未安装 |
|  | oe_test_audit_use_w_audit | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_use_w_audit/2023-08-09-02_58_54.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_use_w_audit/2023-08-09-02_58_54.log) | 软件未安装 |
|  | oe_test_audit_user_build_connection | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_user_build_connection/2023-08-09-03_02_38.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_user_build_connection/2023-08-09-03_02_38.log) | 软件未安装 |
|  | oe_test_aulastlog | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_aulastlog/2025-08-09-03_31_22.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_aulastlog/2025-08-09-03_31_22.log) | 软件未安装 |
|  | oe_test_inject_time_fault | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_inject_time_fault/2023-08-09-03_27_02.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_inject_time_fault/2023-08-09-03_27_02.log) | 软件未安装 |
|  | oe_test_service_auditd | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_service_auditd/2025-08-09-03_28_52.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_service_auditd/2025-08-09-03_28_52.log) | 软件未安装 |
|  | oe_test_start_audit | fail | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_start_audit/2025-08-09-03_30_30.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_start_audit/2025-08-09-03_30_30.log) | 软件未安装 |
| cachefilesd | oe_test_service_cachefilesd | fail | [./oe-rv2309/mugen-riscv/logs/cachefilesd/oe_test_service_cachefilesd/2023-08-09-04_52_37.log](./oe-rv2309/mugen-riscv/logs/cachefilesd/oe_test_service_cachefilesd/2023-08-09-04_52_37.log) | /dev/cachefiles 文件不存在导致 cachefilesd.service 启动失败 |
| chrony | oe_test_service_chrony-wait | fail | [./oe-rv2309/mugen-riscv/logs/chrony/oe_test_service_chrony-wait/2023-08-13-00_46_21.log](./oe-rv2309/mugen-riscv/logs/chrony/oe_test_service_chrony-wait/2023-08-13-00_46_21.log) |  |
|  | oe_test_service_chronyd | fail | [./oe-rv2309/mugen-riscv/logs/chrony/oe_test_service_chronyd/2023-08-13-00_45_04.log](./oe-rv2309/mugen-riscv/logs/chrony/oe_test_service_chronyd/2023-08-13-00_45_04.log) |  |
| chrpath | oe_test_chrpath | fail | [./oe-rv2309/mugen-riscv/logs/chrpath/oe_test_chrpath/2023-08-08-16_24_40.log](./oe-rv2309/mugen-riscv/logs/chrpath/oe_test_chrpath/2023-08-08-16_24_40.log) | 错误一致 |
| clevis | oe_test_high_nbde | fail | [./oe-rv2309/mugen-riscv/logs/clevis/oe_test_high_nbde/2023-08-08-13_18_37.log](./oe-rv2309/mugen-riscv/logs/clevis/oe_test_high_nbde/2023-08-08-13_18_37.log) | 待测软件包 cryptsetup-reencrypt 不存在 |
| cmake | oe_test_ccmake | fail | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ccmake/2023-08-09-03_44_59.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ccmake/2023-08-09-03_44_59.log) | expect问题导致无法正常测试，在2303上两种架构均出现此问题，可能需要手动执行脚本检测缺陷 |
|  | oe_test_ccmake3 | fail | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ccmake3/2023-08-09-03_46_47.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ccmake3/2023-08-09-03_46_47.log) | expect问题导致无法正常测试，在2303上两种架构均出现此问题，可能需要手动执行脚本检测缺陷 |
| crontabs | oe_test_crontabs | fail | [./oe-rv2309/mugen-riscv/logs/crontabs/oe_test_crontabs/2023-08-08-11_32_27.log](./oe-rv2309/mugen-riscv/logs/crontabs/oe_test_crontabs/2023-08-08-11_32_27.log) | Cannot obtain SELinux process context |
| cryptsetup | oe_test_encrypt_data | fail | [./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_encrypt_data/2023-08-13-20_59_10.log](./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_encrypt_data/2023-08-13-20_59_10.log) |  |
|  | oe_test_luks_encrypted | fail | [./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_luks_encrypted/2023-08-13-20_59_50.log](./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_luks_encrypted/2023-08-13-20_59_50.log) |  |
|  | oe_test_use_luks | fail | [./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_use_luks/2023-08-13-20_58_23.log](./oe-rv2309/mugen-riscv/logs/cryptsetup/oe_test_use_luks/2023-08-13-20_58_23.log) |  |
| dbxtool | oe_test_service_dbxtool | fail | [./oe-rv2309/mugen-riscv/logs/dbxtool/oe_test_service_dbxtool/2023-08-13-02_57_48.log](./oe-rv2309/mugen-riscv/logs/dbxtool/oe_test_service_dbxtool/2023-08-13-02_57_48.log) |  |
| dhcp | oe_test_service_dhcpd | fail | [./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcpd/2023-08-08-10_21_02.log](./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcpd/2023-08-08-10_21_02.log) |  |
|  | oe_test_service_dhcpd6 | fail | [./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcpd6/2023-08-08-10_20_22.log](./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcpd6/2023-08-08-10_20_22.log) |  |
|  | oe_test_service_dhcrelay | fail | [./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcrelay/2023-08-08-10_21_40.log](./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_service_dhcrelay/2023-08-08-10_21_40.log) |  |
| dmidecode | oe_test_dmidecode | fail | [./oe-rv2309/mugen-riscv/logs/dmidecode/oe_test_dmidecode/2023-08-08-11_34_20.log](./oe-rv2309/mugen-riscv/logs/dmidecode/oe_test_dmidecode/2023-08-08-11_34_20.log) | 测试依赖 dmidecode 命令，该软件包没有预装也没有被显式安装 |
| dnf | oe_test_dnf_all-repos | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_all-repos/2023-08-08-10_56_02.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_all-repos/2023-08-08-10_56_02.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_enabled_enablerepo | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_enabled_enablerepo/2023-08-08-11_33_50.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_enabled_enablerepo/2023-08-08-11_33_50.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_enhancement_exclude | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_enhancement_exclude/2023-08-08-11_50_28.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_enhancement_exclude/2023-08-08-11_50_28.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_list_mark | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_list_mark/2023-08-08-11_59_07.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_list_mark/2023-08-08-11_59_07.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_makecache_clean | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_makecache_clean/2023-08-08-11_18_43.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_makecache_clean/2023-08-08-11_18_43.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_nobest_nodocs_nogpgcheck | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_nobest_nodocs_nogpgcheck/2023-08-08-12_06_25.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_nobest_nodocs_nogpgcheck/2023-08-08-12_06_25.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_priority | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_priority/2023-08-08-12_11_04.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_priority/2023-08-08-12_11_04.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_provides_randomwait | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_provides_randomwait/2023-08-08-12_13_40.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_provides_randomwait/2023-08-08-12_13_40.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_reinstall_repoinfo | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_reinstall_repoinfo/2023-08-08-12_18_58.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_reinstall_repoinfo/2023-08-08-12_18_58.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_repeat-install | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_repeat-install/2023-08-08-12_27_12.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_repeat-install/2023-08-08-12_27_12.log) | oerv 和 x86 的软件源结构不同 |
|  | oe_test_dnf_repeat-upgrade-downgrade | fail | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_repeat-upgrade-downgrade/2023-08-08-12_58_00.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_repeat-upgrade-downgrade/2023-08-08-12_58_00.log) | oerv 和 x86 的软件源结构不同 |
| ebtables | oe_test_service_ebtables | fail | [./oe-rv2309/mugen-riscv/logs/ebtables/oe_test_service_ebtables/2023-08-13-03_04_20.log](./oe-rv2309/mugen-riscv/logs/ebtables/oe_test_service_ebtables/2023-08-13-03_04_20.log) |  |
| fftw | oe_test_fftw_fftw-wisdom-to-conf | fail | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom-to-conf/2023-08-09-03_38_21.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom-to-conf/2023-08-09-03_38_21.log) | 2303x86和riscv中均出现相同问题，怀疑为上游缺陷 |
|  | oe_test_fftw_fftw-wisdom_02 | fail | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_02/2023-08-09-03_40_33.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_02/2023-08-09-03_40_33.log) | 2303x86和riscv中均出现相同问题，怀疑为上游缺陷 |
|  | oe_test_fftw_fftwf-wisdom_02 | fail | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_02/2023-08-09-03_43_44.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_02/2023-08-09-03_43_44.log) | 2303x86和riscv中均出现相同问题，怀疑为上游缺陷 |
|  | oe_test_fftw_fftwl-wisdom_02 | fail | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_02/2023-08-09-03_46_58.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_02/2023-08-09-03_46_58.log) | 2303x86和riscv中均出现相同问题，怀疑为上游缺陷 |
| fio | oe_test_fio_001 | fail | [./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_001/2023-08-08-14_00_05.log](./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_001/2023-08-08-14_00_05.log) | 手动未复现 |
|  | oe_test_fio_004 | fail | [./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_004/2023-08-08-15_36_51.log](./oe-rv2309/mugen-riscv/logs/fio/oe_test_fio_004/2023-08-08-15_36_51.log) | 手动未复现 |
| firewalld | oe_test_firewalld_add_newservice | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_newservice/2023-08-11-10_22_07.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_newservice/2023-08-11-10_22_07.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_add_port | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_port/2023-08-11-10_25_12.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_port/2023-08-11-10_25_12.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_add_service | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_service/2023-08-11-10_29_43.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_service/2023-08-11-10_29_43.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_add_sourceip | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_sourceip/2023-08-11-10_31_32.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_sourceip/2023-08-11-10_31_32.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_add_sourceport | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_sourceport/2023-08-11-10_31_52.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_add_sourceport/2023-08-11-10_31_52.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_addport_udp | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_addport_udp/2023-08-11-10_27_02.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_addport_udp/2023-08-11-10_27_02.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_allow_zones | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_allow_zones/2023-08-11-10_32_12.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_allow_zones/2023-08-11-10_32_12.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_block_icmp | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_block_icmp/2023-08-11-10_34_32.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_block_icmp/2023-08-11-10_34_32.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_change_interface | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_change_interface/2023-08-11-10_35_02.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_change_interface/2023-08-11-10_35_02.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_change_xml | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_change_xml/2023-08-11-10_36_46.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_change_xml/2023-08-11-10_36_46.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_directrule_acceptdport | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_directrule_acceptdport/2023-08-11-10_38_32.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_directrule_acceptdport/2023-08-11-10_38_32.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_dnat | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_dnat/2023-08-11-10_40_15.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_dnat/2023-08-11-10_40_15.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_dropzone_service | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_dropzone_service/2023-08-11-10_43_11.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_dropzone_service/2023-08-11-10_43_11.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_forward_nat | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_forward_nat/2023-08-11-10_45_02.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_forward_nat/2023-08-11-10_45_02.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_getzone | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_getzone/2023-08-09-02_14_45.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_getzone/2023-08-09-02_14_45.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_ip_camouflage | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_ip_camouflage/2023-08-09-02_15_05.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_ip_camouflage/2023-08-09-02_15_05.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_list | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_list/2023-08-09-02_15_25.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_list/2023-08-09-02_15_25.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_lockdown | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_lockdown/2023-08-09-02_15_44.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_lockdown/2023-08-09-02_15_44.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_log_denied | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_log_denied/2023-08-09-02_16_03.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_log_denied/2023-08-09-02_16_03.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_new_zone | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_new_zone/2023-08-09-02_17_57.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_new_zone/2023-08-09-02_17_57.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_panic_on | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_panic_on/2023-08-11-10_52_29.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_panic_on/2023-08-11-10_52_29.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_port_map | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_port_map/2023-08-11-10_53_33.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_port_map/2023-08-11-10_53_33.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_protocol_tcp | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_protocol_tcp/2023-08-11-10_56_13.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_protocol_tcp/2023-08-11-10_56_13.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_acceptsource | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_acceptsource/2023-08-11-10_58_54.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_acceptsource/2023-08-11-10_58_54.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_allowippak | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_allowippak/2023-08-11-11_00_38.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_allowippak/2023-08-11-11_00_38.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_blacklist | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_blacklist/2023-08-11-11_02_26.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_blacklist/2023-08-11-11_02_26.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_dropicmppack | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_dropicmppack/2023-08-11-11_03_16.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_dropicmppack/2023-08-11-11_03_16.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_dropipv4pak | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_dropipv4pak/2023-08-11-11_03_48.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_dropipv4pak/2023-08-11-11_03_48.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_richrule_forwardport | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_forwardport/2023-08-11-11_05_39.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_richrule_forwardport/2023-08-11-11_05_39.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_runtime_permanent | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_runtime_permanent/2023-08-09-02_18_17.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_runtime_permanent/2023-08-09-02_18_17.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_runtime_rules_in_effect | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_runtime_rules_in_effect/2023-08-11-11_17_44.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_runtime_rules_in_effect/2023-08-11-11_17_44.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_set_defaultzone | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_defaultzone/2023-08-11-11_08_48.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_defaultzone/2023-08-11-11_08_48.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_set_ipset | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_ipset/2023-08-11-11_10_36.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_ipset/2023-08-11-11_10_36.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_set_target | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_target/2023-08-09-02_18_54.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_target/2023-08-09-02_18_54.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_start_check | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_check/2023-08-09-02_19_14.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_check/2023-08-09-02_19_14.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_start_status | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_status/2023-08-09-02_19_31.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_status/2023-08-09-02_19_31.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_start_stop | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_stop/2023-08-09-02_19_49.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_start_stop/2023-08-09-02_19_49.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_whitelist_command | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_command/2023-08-09-02_20_20.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_command/2023-08-09-02_20_20.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_whitelist_contexts | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_contexts/2023-08-09-02_20_44.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_contexts/2023-08-09-02_20_44.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_whitelist_uids | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_uids/2023-08-09-02_21_02.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_uids/2023-08-09-02_21_02.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_whitelist_user | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_user/2023-08-09-02_21_22.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_whitelist_user/2023-08-09-02_21_22.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_zone_add_service | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_zone_add_service/2023-08-09-02_21_44.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_zone_add_service/2023-08-09-02_21_44.log) | 软件firewalld未安装 |
|  | oe_test_firewalld_zone_migration | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_zone_migration/2023-08-11-12_18_03.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_zone_migration/2023-08-11-12_18_03.log) | 软件firewalld未安装 |
|  | oe_test_service_firewalld | fail | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_service_firewalld/2023-08-09-02_22_01.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_service_firewalld/2023-08-09-02_22_01.log) | 软件firewalld未安装 |
| fontconfig | oe_test_fontconfig_fc-list | fail | [./oe-rv2309/mugen-riscv/logs/fontconfig/oe_test_fontconfig_fc-list/2023-08-08-12_14_11.log](./oe-rv2309/mugen-riscv/logs/fontconfig/oe_test_fontconfig_fc-list/2023-08-08-12_14_11.log) | Broken testcase，测试不存在 |
| freeradius | oe_test_freeradius_freeradius-utils_rad_counter | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_rad_counter/2023-08-12-18_34_49.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_rad_counter/2023-08-12-18_34_49.log) |  |
|  | oe_test_freeradius_freeradius-utils_radclient2 | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radclient2/2023-08-12-18_32_13.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radclient2/2023-08-12-18_32_13.log) |  |
|  | oe_test_freeradius_freeradius-utils_radlast | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radlast/2023-08-12-18_40_12.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radlast/2023-08-12-18_40_12.log) |  |
|  | oe_test_freeradius_freeradius-utils_radlastAndRadsniff | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radlastAndRadsniff/2023-08-12-18_41_20.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radlastAndRadsniff/2023-08-12-18_41_20.log) |  |
|  | oe_test_freeradius_freeradius-utils_radsniff | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsniff/2023-08-12-18_42_17.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsniff/2023-08-12-18_42_17.log) |  |
|  | oe_test_freeradius_freeradius-utils_radsniff2 | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsniff2/2023-08-12-19_12_35.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsniff2/2023-08-12-19_12_35.log) |  |
|  | oe_test_freeradius_freeradius-utils_radsqlrelay | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay/2023-08-12-19_15_16.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay/2023-08-12-19_15_16.log) |  |
|  | oe_test_freeradius_freeradius-utils_radtestAndRadwho | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radtestAndRadwho/2023-08-12-19_19_51.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radtestAndRadwho/2023-08-12-19_19_51.log) |  |
|  | oe_test_freeradius_freeradius-utils_radwho | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radwho/2023-08-12-19_22_38.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radwho/2023-08-12-19_22_38.log) |  |
|  | oe_test_freeradius_freeradius-utils_radzap | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radzap/2023-08-12-19_23_45.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radzap/2023-08-12-19_23_45.log) |  |
|  | oe_test_freeradius_freeradius-utils_rlm_ippool_toolAndSmbencrypt | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_rlm_ippool_toolAndSmbencrypt/2023-08-12-19_25_36.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_rlm_ippool_toolAndSmbencrypt/2023-08-12-19_25_36.log) |  |
|  | oe_test_freeradius_freeradius_raddebugAndCheckrad | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_raddebugAndCheckrad/2023-08-12-17_29_18.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_raddebugAndCheckrad/2023-08-12-17_29_18.log) |  |
|  | oe_test_service_radiusd | fail | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_service_radiusd/2023-08-12-19_26_39.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_service_radiusd/2023-08-12-19_26_39.log) |  |
| glib2 | oe_test_glib2 | fail | [./oe-rv2309/mugen-riscv/logs/glib2/oe_test_glib2/2023-08-13-03_13_50.log](./oe-rv2309/mugen-riscv/logs/glib2/oe_test_glib2/2023-08-13-03_13_50.log) |  |
| hdparm | oe_test_hdparm | fail | [./oe-rv2309/mugen-riscv/logs/hdparm/oe_test_hdparm/2023-08-13-03_19_56.log](./oe-rv2309/mugen-riscv/logs/hdparm/oe_test_hdparm/2023-08-13-03_19_56.log) |  |
|  | oe_test_hdparm | fail | [./oe-rv2309/mugen-riscv/logs/hdparm/oe_test_hdparm/2023-08-13-03_19_56.log](./oe-rv2309/mugen-riscv/logs/hdparm/oe_test_hdparm/2023-08-13-03_19_56.log) |  |
| hostname | oe_test_service_nis-domainname | fail | [./oe-rv2309/mugen-riscv/logs/hostname/oe_test_service_nis-domainname/2023-08-09-05_00_31.log](./oe-rv2309/mugen-riscv/logs/hostname/oe_test_service_nis-domainname/2023-08-09-05_00_31.log) | nis-domainname.service 启动失败 |
| initscripts | oe_test_service_netconsole | fail | [./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_netconsole/2023-08-09-10_04_29.log](./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_netconsole/2023-08-09-10_04_29.log) | Job for netconsole.service failed because the control process exited with error code. |
| iperf3 | oe_test_iperf3_command_client | fail | [./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_client/2023-08-12-22_25_01.log](./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_command_client/2023-08-12-22_25_01.log) |  |
| ipmitool | oe_test_service_bmc-snmp-proxy | fail | [./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_bmc-snmp-proxy/2023-08-08-10_55_56.log](./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_bmc-snmp-proxy/2023-08-08-10_55_56.log) | bmc-snmp-proxy.service: Unit bmc-snmp-proxy.service not found. |
|  | oe_test_service_exchange-bmc-os-info | fail | [./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_exchange-bmc-os-info/2023-08-08-10_57_16.log](./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_exchange-bmc-os-info/2023-08-08-10_57_16.log) | Unit exchange-bmc-os-info.service not found. |
| iprutils | oe_test_service_iprdump | fail | [./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprdump/2023-08-09-04_11_11.log](./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprdump/2023-08-09-04_11_11.log) | 测试脚本没有显式安装iprutils，镜像也没有预装 |
|  | oe_test_service_iprinit | fail | [./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprinit/2023-08-09-04_11_55.log](./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprinit/2023-08-09-04_11_55.log) | 测试脚本没有显式安装iprutils，镜像也没有预装 |
|  | oe_test_service_iprupdate | fail | [./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprupdate/2023-08-09-04_12_39.log](./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_service_iprupdate/2023-08-09-04_12_39.log) | 测试脚本没有显式安装iprutils，镜像也没有预装 |
|  | oe_test_target_iprutils | fail | [./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_target_iprutils/2023-08-09-04_13_24.log](./oe-rv2309/mugen-riscv/logs/iprutils/oe_test_target_iprutils/2023-08-09-04_13_24.log) | 测试脚本没有显式安装iprutils，镜像也没有预装 |
| iptables | oe_test_ip6tables-restore_01 | fail | [./oe-rv2309/mugen-riscv/logs/iptables/oe_test_ip6tables-restore_01/2023-08-08-10_24_37.log](./oe-rv2309/mugen-riscv/logs/iptables/oe_test_ip6tables-restore_01/2023-08-08-10_24_37.log) |  |
|  | oe_test_ip6tables-save | fail | [./oe-rv2309/mugen-riscv/logs/iptables/oe_test_ip6tables-save/2023-08-08-10_23_52.log](./oe-rv2309/mugen-riscv/logs/iptables/oe_test_ip6tables-save/2023-08-08-10_23_52.log) |  |
|  | oe_test_iptables-restore | fail | [./oe-rv2309/mugen-riscv/logs/iptables/oe_test_iptables-restore/2023-08-08-10_23_06.log](./oe-rv2309/mugen-riscv/logs/iptables/oe_test_iptables-restore/2023-08-08-10_23_06.log) |  |
|  | oe_test_service_ip6tables | fail | [./oe-rv2309/mugen-riscv/logs/iptables/oe_test_service_ip6tables/2023-08-08-10_21_44.log](./oe-rv2309/mugen-riscv/logs/iptables/oe_test_service_ip6tables/2023-08-08-10_21_44.log) |  |
|  | oe_test_service_iptables | fail | [./oe-rv2309/mugen-riscv/logs/iptables/oe_test_service_iptables/2023-08-08-10_22_24.log](./oe-rv2309/mugen-riscv/logs/iptables/oe_test_service_iptables/2023-08-08-10_22_24.log) |  |
| iputils | oe_test_service_ninfod | fail | [./oe-rv2309/mugen-riscv/logs/iputils/oe_test_service_ninfod/2023-08-08-20_25_13.log](./oe-rv2309/mugen-riscv/logs/iputils/oe_test_service_ninfod/2023-08-08-20_25_13.log) | 安装脚本报错：No match for argument: iputils-ninfod |
|  | oe_test_service_rdisc | fail | [./oe-rv2309/mugen-riscv/logs/iputils/oe_test_service_rdisc/2023-08-08-20_26_21.log](./oe-rv2309/mugen-riscv/logs/iputils/oe_test_service_rdisc/2023-08-08-20_26_21.log) | Unit rdisc.service not found. |
| ipvsadm | oe_test_ipvs_ADD_01 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_ADD_01/2023-08-12-15_43_38.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_ADD_01/2023-08-12-15_43_38.log) |  |
|  | oe_test_ipvs_DEL_01 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_DEL_01/2023-08-12-15_47_08.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_DEL_01/2023-08-12-15_47_08.log) |  |
|  | oe_test_ipvs_MOD_01 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_MOD_01/2023-08-12-15_49_17.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_MOD_01/2023-08-12-15_49_17.log) |  |
|  | oe_test_ipvs_SAVE_01 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SAVE_01/2023-08-12-15_51_36.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SAVE_01/2023-08-12-15_51_36.log) |  |
|  | oe_test_ipvs_SCEN_DR_05 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_05/2023-08-12-16_14_39.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_05/2023-08-12-16_14_39.log) |  |
|  | oe_test_ipvs_SCEN_DR_06 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_06/2023-08-12-16_17_56.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_06/2023-08-12-16_17_56.log) |  |
|  | oe_test_ipvs_SCEN_TUN_05 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_05/2023-08-12-16_41_27.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_05/2023-08-12-16_41_27.log) |  |
|  | oe_test_ipvs_SCEN_TUN_06 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_06/2023-08-12-16_44_50.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_06/2023-08-12-16_44_50.log) |  |
|  | oe_test_ipvs_WRONG_COMMAND_01 | fail | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_WRONG_COMMAND_01/2023-08-12-16_55_00.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_WRONG_COMMAND_01/2023-08-12-16_55_00.log) |  |
| irqbalance | oe_test_service_irqbalance | fail | [./oe-rv2309/mugen-riscv/logs/irqbalance/oe_test_service_irqbalance/2023-08-09-09_59_33.log](./oe-rv2309/mugen-riscv/logs/irqbalance/oe_test_service_irqbalance/2023-08-09-09_59_33.log) | 依赖 irqbalance 软件包，但是 23.09 riscv 源中不存在该包 |
| keepalived | oe_test_service_keepalived | fail | [./oe-rv2309/mugen-riscv/logs/keepalived/oe_test_service_keepalived/2023-08-08-11_47_02.log](./oe-rv2309/mugen-riscv/logs/keepalived/oe_test_service_keepalived/2023-08-08-11_47_02.log) |  |
| kernel | oe_test_check_huge_task | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_check_huge_task/2023-08-08-11_17_26.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_check_huge_task/2023-08-08-11_17_26.log) | 内核配置与预期相悖 |
|  | oe_test_cifs | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cifs/2023-08-08-11_19_05.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cifs/2023-08-08-11_19_05.log) | mugen脚本问题 |
|  | oe_test_cpu_rand | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cpu_rand/2023-08-08-11_13_19.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cpu_rand/2023-08-08-11_13_19.log) | 内核配置问题 |
|  | oe_test_fnic | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_fnic/2023-08-08-11_23_16.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_fnic/2023-08-08-11_23_16.log) | modinfo: ERROR: Module fnic not found. |
|  | oe_test_hifc | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hifc/2023-08-08-11_22_23.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hifc/2023-08-08-11_22_23.log) | modinfo: ERROR: Module hifc not found. |
|  | oe_test_hinic | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hinic/2023-08-08-11_10_10.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hinic/2023-08-08-11_10_10.log) | modinfo: ERROR: Module hinic not found. |
|  | oe_test_hpsa | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hpsa/2023-08-08-11_24_36.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hpsa/2023-08-08-11_24_36.log) | test的模块文件名与实际不同 |
|  | oe_test_io_sched | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_io_sched/2023-08-08-11_06_23.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_io_sched/2023-08-08-11_06_23.log) | 内核配置问题 |
|  | oe_test_kernel_cmd_01 | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_01/2023-08-08-10_53_35.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_01/2023-08-08-10_53_35.log) | Trying to set a specific frequency, but userspace governor is not available,   for example because of hardware which cannot be set to a specific frequency   
 or because the userspace governor isn't loaded? |
|  | oe_test_libfc | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_libfc/2023-08-08-11_24_03.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_libfc/2023-08-08-11_24_03.log) | test的模块文件名与实际不同 |
|  | oe_test_lpfc | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_lpfc/2023-08-08-11_16_39.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_lpfc/2023-08-08-11_16_39.log) | modinfo: ERROR: Module lpfc not found. |
|  | oe_test_service_cpupower | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_service_cpupower/2023-08-08-11_02_42.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_service_cpupower/2023-08-08-11_02_42.log) | 软件kernel-tools未安装 |
|  | oe_test_snd_aloop | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_snd_aloop/2023-08-08-11_08_52.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_snd_aloop/2023-08-08-11_08_52.log) | modinfo: ERROR: Module snd-aloop not found. |
|  | oe_test_swap_compress | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_swap_compress/2023-08-08-11_15_57.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_swap_compress/2023-08-08-11_15_57.log) | 测试使用的 swap 设备 ``/dev/dm-1`` 没有提前建立直接使用 |
|  | oe_test_vport-geneve | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_vport-geneve/2023-08-08-11_15_15.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_vport-geneve/2023-08-08-11_15_15.log) | modprobe: ERROR: could not insert 'vport_geneve': Unknown symbol in module, or unknown parameter (see dmesg) |
|  | oe_test_wangxun | fail | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_wangxun/2023-08-08-11_20_16.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_wangxun/2023-08-08-11_20_16.log) | insmod: ERROR: could not insert module ngbe.ko: Unknown symbol in module |
| kexec-tools | oe_test_service_kdump | fail | [./oe-rv2309/mugen-riscv/logs/kexec-tools/oe_test_service_kdump/2023-08-13-03_23_04.log](./oe-rv2309/mugen-riscv/logs/kexec-tools/oe_test_service_kdump/2023-08-13-03_23_04.log) |  |
| keyutils | oe_test_keyutils-api | fail | [./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-api/2023-08-08-10_51_47.log](./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-api/2023-08-08-10_51_47.log) | 找不到头文件keyutils.h |
| kmod | oe_test_insmod-lsmod | fail | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_insmod-lsmod/2023-08-09-03_50_37.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_insmod-lsmod/2023-08-09-03_50_37.log) | 由于raid0与faulty两个模块缺失，故 find 没有输出 |
| libappstream-glib | oe_test_libappstream-glib_appstream-util_03 | fail | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_03/2023-08-12-21_10_16.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_03/2023-08-12-21_10_16.log) |  |
| libosinfo | oe_test_osinfo-detect | fail | [./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-detect/2023-08-08-10_47_55.log](./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-detect/2023-08-08-10_47_55.log) | osinfo-detect –format=env 命令 env 不合法 |
| libreswan | oe_test_host_to_host_vpn | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_host_to_host_vpn/2023-08-08-02_54_13.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_host_to_host_vpn/2023-08-08-02_54_13.log) |  |
|  | oe_test_libreswan_ipsec_addconn | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_addconn/2023-08-08-02_38_40.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_addconn/2023-08-08-02_38_40.log) |  |
|  | oe_test_libreswan_ipsec_auto_1 | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_auto_1/2023-08-08-02_40_52.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_auto_1/2023-08-08-02_40_52.log) |  |
|  | oe_test_libreswan_ipsec_auto_2 | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_auto_2/2023-08-08-02_42_04.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_auto_2/2023-08-08-02_42_04.log) |  |
|  | oe_test_libreswan_ipsec_setup | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_setup/2023-08-08-02_45_29.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_setup/2023-08-08-02_45_29.log) |  |
|  | oe_test_libreswan_ipsec_showhostkey_nss | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_showhostkey_nss/2023-08-08-02_46_34.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_showhostkey_nss/2023-08-08-02_46_34.log) |  |
|  | oe_test_libreswan_ipsec_systemctl | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_systemctl/2023-08-08-02_47_47.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_systemctl/2023-08-08-02_47_47.log) |  |
|  | oe_test_service_ipsec | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_service_ipsec/2023-08-08-02_52_44.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_service_ipsec/2023-08-08-02_52_44.log) |  |
|  | oe_test_site_to_site_vpn | fail | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_site_to_site_vpn/2023-08-08-02_56_01.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_site_to_site_vpn/2023-08-08-02_56_01.log) |  |
| libtiff | oe_test_libtiff | fail | [./oe-rv2309/mugen-riscv/logs/libtiff/oe_test_libtiff/2023-08-13-05_13_33.log](./oe-rv2309/mugen-riscv/logs/libtiff/oe_test_libtiff/2023-08-13-05_13_33.log) |  |
| lm_sensors | oe_test_service_fancontrol | fail | [./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_fancontrol/2023-08-12-23_20_08.log](./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_fancontrol/2023-08-12-23_20_08.log) |  |
|  | oe_test_service_lm_sensors | fail | [./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_lm_sensors/2023-08-12-23_23_18.log](./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_lm_sensors/2023-08-12-23_23_18.log) |  |
| lvm2 | oe_test_service_lvmlockd | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvmlockd/2023-08-09-03_18_27.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvmlockd/2023-08-09-03_18_27.log) | oerv 缺失软件包 lvm2-locked |
|  | oe_test_service_lvmlocks | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvmlocks/2023-08-09-03_19_46.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvmlocks/2023-08-09-03_19_46.log) | oerv 缺失软件包 lvm2-locked |
| mc | oe_test_mc_base_01 | fail | [./oe-rv2309/mugen-riscv/logs/mc/oe_test_mc_base_01/2023-08-08-10_33_01.log](./oe-rv2309/mugen-riscv/logs/mc/oe_test_mc_base_01/2023-08-08-10_33_01.log) |  |
|  | oe_test_mcedit_base_01 | fail | [./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcedit_base_01/2023-08-08-10_34_23.log](./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcedit_base_01/2023-08-08-10_34_23.log) |  |
|  | oe_test_mcview_base_01 | fail | [./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcview_base_01/2023-08-08-10_34_42.log](./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcview_base_01/2023-08-08-10_34_42.log) |  |
| mcstrans | oe_test_service_mcstrans | fail | [./oe-rv2309/mugen-riscv/logs/mcstrans/oe_test_service_mcstrans/2023-08-09-04_42_41.log](./oe-rv2309/mugen-riscv/logs/mcstrans/oe_test_service_mcstrans/2023-08-09-04_42_41.log) | mcstransd.service 启动失败 |
|  | oe_test_service_mcstransd | fail | [./oe-rv2309/mugen-riscv/logs/mcstrans/oe_test_service_mcstransd/2023-08-09-04_41_12.log](./oe-rv2309/mugen-riscv/logs/mcstrans/oe_test_service_mcstransd/2023-08-09-04_41_12.log) | mcstransd.service 启动失败 |
| mtx | oe_test_mtx_loaderinfo | fail | [./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_loaderinfo/2023-08-08-10_33_35.log](./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_loaderinfo/2023-08-08-10_33_35.log) | 缺失lsscsi、build mhvtl-utils-1.7-3.riscv64时缺少kernel-devel |
|  | oe_test_mtx_mtx | fail | [./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_mtx/2023-08-08-10_36_31.log](./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_mtx/2023-08-08-10_36_31.log) | 缺失lsscsi、build mhvtl-utils-1.7-3.riscv64时缺少kernel-devel |
|  | oe_test_mtx_scsieject | fail | [./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_scsieject/2023-08-08-10_38_14.log](./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_scsieject/2023-08-08-10_38_14.log) | 缺失lsscsi、build mhvtl-utils-1.7-3.riscv64时缺少kernel-devel |
|  | oe_test_mtx_scsitape | fail | [./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_scsitape/2023-08-08-10_39_38.log](./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_scsitape/2023-08-08-10_39_38.log) | 缺失lsscsi、build mhvtl-utils-1.7-3.riscv64时缺少kernel-devel |
|  | oe_test_mtx_tapeinfo | fail | [./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_tapeinfo/2023-08-08-10_35_08.log](./oe-rv2309/mugen-riscv/logs/mtx/oe_test_mtx_tapeinfo/2023-08-08-10_35_08.log) | 缺失lsscsi、build mhvtl-utils-1.7-3.riscv64时缺少kernel-devel |
| mysql | oe_test_service_mysql | fail | [./oe-rv2309/mugen-riscv/logs/mysql/oe_test_service_mysql/2023-08-09-05_04_21.log](./oe-rv2309/mugen-riscv/logs/mysql/oe_test_service_mysql/2023-08-09-05_04_21.log) | 安装mysql软件包后，无法找到mysql.service |
| ndisc6 | oe_test_ndisc6_name2addr | fail | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_name2addr/2023-08-11-00_24_09.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_name2addr/2023-08-11-00_24_09.log) | 配置环境时未适配ipv6 |
|  | oe_test_ndisc6_rdisc6 | fail | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rdisc6/2023-08-11-00_29_11.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rdisc6/2023-08-11-00_29_11.log) | 返回值与预期不同 |
| net-snmp | oe_test_net-snmp_01 | fail | [./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_net-snmp_01/2023-08-12-22_41_00.log](./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_net-snmp_01/2023-08-12-22_41_00.log) |  |
|  | oe_test_net-snmp_02 | fail | [./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_net-snmp_02/2023-08-12-22_41_52.log](./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_net-snmp_02/2023-08-12-22_41_52.log) |  |
| net-tools | oe_test_net-tools_route | fail | [./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_route/2023-08-12-22_20_49.log](./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_route/2023-08-12-22_20_49.log) |  |
| nftables | oe_test_nftables_anonymous_map | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_anonymous_map/2023-08-08-11_41_51.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_anonymous_map/2023-08-08-11_41_51.log) | 软件nftables未安装 |
|  | oe_test_nftables_backup_rules | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_backup_rules/2023-08-08-11_55_00.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_backup_rules/2023-08-08-11_55_00.log) | 软件nftables未安装 |
|  | oe_test_nftables_chains | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_chains/2023-08-08-11_48_33.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_chains/2023-08-08-11_48_33.log) | 软件nftables未安装 |
|  | oe_test_nftables_create_counters | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_create_counters/2023-08-08-11_35_50.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_create_counters/2023-08-08-11_35_50.log) | 软件nftables未安装 |
|  | oe_test_nftables_listen_package | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_listen_package/2023-08-08-11_43_40.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_listen_package/2023-08-08-11_43_40.log) | 软件nftables未安装 |
|  | oe_test_nftables_name_sets | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_name_sets/2023-08-08-11_45_40.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_name_sets/2023-08-08-11_45_40.log) | 软件nftables未安装 |
|  | oe_test_nftables_replace_counters | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_replace_counters/2023-08-08-11_56_39.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_replace_counters/2023-08-08-11_56_39.log) | 软件nftables未安装 |
|  | oe_test_nftables_variable_map | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_variable_map/2023-08-08-11_47_06.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_nftables_variable_map/2023-08-08-11_47_06.log) | 软件nftables未安装 |
|  | oe_test_service_nftables | fail | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_service_nftables/2023-08-08-11_58_05.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_service_nftables/2023-08-08-11_58_05.log) | 软件nftables未安装 |
| open-iscsi | oe_test_open-iscsi_iscsiadm | fail | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_open-iscsi_iscsiadm/2023-08-08-18_47_32.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_open-iscsi_iscsiadm/2023-08-08-18_47_32.log) |  |
|  | oe_test_open-iscsi_iscsistart_iscsiuio | fail | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_open-iscsi_iscsistart_iscsiuio/2023-08-08-18_52_08.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_open-iscsi_iscsistart_iscsiuio/2023-08-08-18_52_08.log) |  |
|  | oe_test_service_iscsiuio | fail | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsiuio/2023-08-08-18_56_05.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsiuio/2023-08-08-18_56_05.log) |  |
|  | oe_test_socket_iscsiuio | fail | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_socket_iscsiuio/2023-08-08-18_58_39.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_socket_iscsiuio/2023-08-08-18_58_39.log) |  |
| openscap | oe_test_assess_safety_compliance | fail | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_assess_safety_compliance/2023-08-08-21_07_53.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_assess_safety_compliance/2023-08-08-21_07_53.log) | 测试 node2 不存在 |
|  | oe_test_scanning_remote_system | fail | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_scanning_remote_system/2023-08-08-21_00_31.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_scanning_remote_system/2023-08-08-21_00_31.log) | 测试 node2 不存在 |
| openssh | oe_test_openssh_cipher | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_cipher/2023-08-11-00_20_45.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_cipher/2023-08-11-00_20_45.log) | 预期ssh -oCiphers=aes256-ctr localhost登陆失败但登陆成功 |
|  | oe_test_openssh_locked | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_locked/2023-08-11-00_23_15.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_locked/2023-08-11-00_23_15.log) | /usr/bin/ssh-copy-id: ERROR: failed to open ID file '/root/.ssh/id_rsa.pub': No such file or directory |
|  | oe_test_openssh_no_password | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_no_password/2023-08-11-00_24_33.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_no_password/2023-08-11-00_24_33.log) | scp: stat local "/root/.ssh/id_rsa.pub": No such file or directory |
|  | oe_test_openssh_port | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_port/2023-08-11-00_26_36.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_port/2023-08-11-00_26_36.log) | firewalld未安装 |
|  | oe_test_openssh_scp | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp/2023-08-11-00_30_49.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp/2023-08-11-00_30_49.log) | scp镜像中/etc/openEuler-latest不存在 |
|  | oe_test_openssh_scp_P | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_P/2023-08-11-00_27_20.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_P/2023-08-11-00_27_20.log) | firewalld未安装 |
|  | oe_test_openssh_scp_q | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_q/2023-08-11-00_28_42.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_q/2023-08-11-00_28_42.log) | scp镜像中/etc/openEuler-latest不存在 |
|  | oe_test_sec_ssh | fail | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_sec_ssh/2023-08-11-00_43_14.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_sec_ssh/2023-08-11-00_43_14.log) | ValueError: SELinux policy is not managed or store cannot be accessed. ssh配置问题 |
| openssl | oe_test_openssl_DSA_algorithm | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_DSA_algorithm/2023-08-12-15_05_50.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_DSA_algorithm/2023-08-12-15_05_50.log) |  |
|  | oe_test_openssl_Diffie_Hellman | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_Diffie_Hellman/2023-08-12-15_05_06.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_Diffie_Hellman/2023-08-12-15_05_06.log) |  |
|  | oe_test_openssl_Implements_https | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_Implements_https/2023-08-12-15_13_19.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_Implements_https/2023-08-12-15_13_19.log) |  |
|  | oe_test_openssl_RSA_algorithm | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_RSA_algorithm/2023-08-12-15_35_30.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_RSA_algorithm/2023-08-12-15_35_30.log) |  |
|  | oe_test_openssl_create_CA_applying_certificate | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_create_CA_applying_certificate/2023-08-12-14_57_37.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_create_CA_applying_certificate/2023-08-12-14_57_37.log) |  |
|  | oe_test_openssl_delete_configuration_file | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_delete_configuration_file/2023-08-12-15_02_28.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_delete_configuration_file/2023-08-12-15_02_28.log) |  |
|  | oe_test_openssl_empty_private_key | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_empty_private_key/2023-08-12-15_06_22.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_empty_private_key/2023-08-12-15_06_22.log) |  |
|  | oe_test_openssl_empty_public_key | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_empty_public_key/2023-08-12-15_06_49.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_empty_public_key/2023-08-12-15_06_49.log) |  |
|  | oe_test_openssl_encrypt_decrypt_file_asymmetrically | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypt_decrypt_file_asymmetrically/2023-08-12-15_07_19.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypt_decrypt_file_asymmetrically/2023-08-12-15_07_19.log) |  |
|  | oe_test_openssl_encrypte_decrypte_emails | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypte_decrypte_emails/2023-08-12-15_07_56.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypte_decrypte_emails/2023-08-12-15_07_56.log) |  |
|  | oe_test_openssl_expired_certificate | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_expired_certificate/2023-08-12-15_08_45.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_expired_certificate/2023-08-12-15_08_45.log) |  |
|  | oe_test_openssl_file_signature_verification | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_file_signature_verification/2023-08-12-15_11_16.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_file_signature_verification/2023-08-12-15_11_16.log) |  |
|  | oe_test_openssl_generate_key_pair | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_key_pair/2023-08-12-15_11_53.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_key_pair/2023-08-12-15_11_53.log) |  |
|  | oe_test_openssl_generate_password | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_password/2023-08-12-15_12_23.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_password/2023-08-12-15_12_23.log) |  |
|  | oe_test_openssl_incorrect_public_private_key | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_incorrect_public_private_key/2023-08-12-15_16_01.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_incorrect_public_private_key/2023-08-12-15_16_01.log) |  |
|  | oe_test_openssl_rc4_encryption_file | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_rc4_encryption_file/2023-08-12-15_37_53.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_rc4_encryption_file/2023-08-12-15_37_53.log) |  |
|  | oe_test_openssl_reliability | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_reliability/2023-08-12-15_19_01.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_reliability/2023-08-12-15_19_01.log) |  |
|  | oe_test_openssl_remove_mod_ssl | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_remove_mod_ssl/2023-08-12-15_21_38.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_remove_mod_ssl/2023-08-12-15_21_38.log) |  |
|  | oe_test_openssl_repeat_restart | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_repeat_restart/2023-08-12-15_26_27.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_repeat_restart/2023-08-12-15_26_27.log) |  |
|  | oe_test_openssl_speed | fail | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_speed/2023-08-12-15_39_54.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_speed/2023-08-12-15_39_54.log) |  |
| openvpn | oe_test_openvpn | fail | [./oe-rv2309/mugen-riscv/logs/openvpn/oe_test_openvpn/2023-08-15-08_07_50.log](./oe-rv2309/mugen-riscv/logs/openvpn/oe_test_openvpn/2023-08-15-08_07_50.log) | 命令测试失败 |
| openvswitch | oe_test_service_openvswitch | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_openvswitch/2023-08-12-21_14_43.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_openvswitch/2023-08-12-21_14_43.log) |  |
|  | oe_test_service_openvswitch-ipsec | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_openvswitch-ipsec/2023-08-12-21_30_34.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_openvswitch-ipsec/2023-08-12-21_30_34.log) |  |
|  | oe_test_service_ovn-controller | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-controller/2023-08-12-21_25_05.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-controller/2023-08-12-21_25_05.log) |  |
|  | oe_test_service_ovn-controller-vtep | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-controller-vtep/2023-08-12-21_28_19.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-controller-vtep/2023-08-12-21_28_19.log) |  |
|  | oe_test_service_ovn-northd | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-northd/2023-08-12-21_26_38.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovn-northd/2023-08-12-21_26_38.log) |  |
|  | oe_test_service_ovs-vswitchd | fail | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovs-vswitchd/2023-08-12-21_22_08.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovs-vswitchd/2023-08-12-21_22_08.log) |  |
| os-basic | oe_test_auditctl | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditctl/2023-08-08-11_30_21.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditctl/2023-08-08-11_30_21.log) | 软件包audit未安装 |
|  | oe_test_auditctl_02 | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditctl_02/2023-08-09-00_12_24.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditctl_02/2023-08-09-00_12_24.log) | 软件包audit未安装 |
|  | oe_test_awk | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_awk/2023-08-08-11_23_43.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_awk/2023-08-08-11_23_43.log) | 测试套问题，检查 cpuid 在 qemu riscv 失败 |
|  | oe_test_bunzip2 | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bunzip2/2023-08-09-00_13_04.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bunzip2/2023-08-09-00_13_04.log) | mugen脚本问题，脚本创建文件与使用文件路径不一致 |
|  | oe_test_catman | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_catman/2023-08-08-11_06_54.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_catman/2023-08-08-11_06_54.log) | catman 1|grep "ab" 命令没有 grep 到 ab 字串 |
|  | oe_test_chsh | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chsh/2023-08-08-23_40_08.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chsh/2023-08-08-23_40_08.log) | 测试套问题，脚本需要测试环境默认语言为中文，且依赖 zsh |
|  | oe_test_count_gperftools_function | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_count_gperftools_function/2023-08-08-23_46_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_count_gperftools_function/2023-08-08-23_46_34.log) | 测试套问题，没有引入依赖 gperftools-devel |
|  | oe_test_csplit | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_csplit/2023-08-08-10_57_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_csplit/2023-08-08-10_57_34.log) | mugen脚本问题，csplit产生的文件的位置应为当前目录，而非文本目录 |
|  | oe_test_disk_graphics_card | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_graphics_card/2023-08-08-22_17_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_graphics_card/2023-08-08-22_17_55.log) | 软件dmidecode未安装 |
|  | oe_test_disk_schedule_udev | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_schedule_udev/2023-08-16-13_49_31.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_schedule_udev/2023-08-16-13_49_31.log) | 出错一致 cp: cannot stat '/etc/udev/rules.d/99-scheduler.rules': No such file or directory |
|  | oe_test_dmraid | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_dmraid/2023-08-08-11_13_04.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_dmraid/2023-08-08-11_13_04.log) | 在 riscv 上 dmraid -h 命令出现段错误 Segmentation fault (core dumped)  |
|  | oe_test_envsubst | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_envsubst/2023-08-08-10_59_33.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_envsubst/2023-08-08-10_59_33.log) | 测试依赖 envsubst ，该软件包没有预装也没有被显式安装 |
|  | oe_test_ethtool | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ethtool/2023-08-08-20_04_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ethtool/2023-08-08-20_04_34.log) | systemd 问题 |
|  | oe_test_lastb | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lastb/2023-08-08-23_09_02.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lastb/2023-08-08-23_09_02.log) | 测试需要中文环境 |
|  | oe_test_lsscsi_1 | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsscsi_1/2023-08-09-00_20_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsscsi_1/2023-08-09-00_20_39.log) | lsscsi 失败 |
|  | oe_test_lsscsi_2 | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsscsi_2/2023-08-09-00_22_05.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsscsi_2/2023-08-09-00_22_05.log) | 2303 未测试该测试用例， lsscsi 失败， lsscsi 相关测试一直都失败的 |
|  | oe_test_net_cmd_scp | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_scp/2023-08-08-21_59_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_scp/2023-08-08-21_59_37.log) | 软件源问题 |
|  | oe_test_nmcli_config_connect | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_config_connect/2023-08-08-19_18_49.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_config_connect/2023-08-08-19_18_49.log) | nmcli up 失败 |
|  | oe_test_python3-kmod | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_python3-kmod/2023-08-09-00_17_54.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_python3-kmod/2023-08-09-00_17_54.log) | snd_seq 内核模块不存在 |
|  | oe_test_server_openssh_verifykey | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_verifykey/2023-08-08-13_50_12.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_verifykey/2023-08-08-13_50_12.log) | 测试 node2 不存在 |
|  | oe_test_server_vsftpd_NKdelay | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_NKdelay/2023-08-08-12_49_31.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_NKdelay/2023-08-08-12_49_31.log) | 测试 node2 不存在 |
|  | oe_test_server_vsftpd_transfer | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_transfer/2023-08-08-12_29_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_transfer/2023-08-08-12_29_29.log) | 测试 node2 不存在 |
|  | oe_test_sos | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sos/2023-08-16-14_59_32.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sos/2023-08-16-14_59_32.log) | 测试失败 |
|  | oe_test_system_log_dmesg | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_dmesg/2023-08-08-12_26_03.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_dmesg/2023-08-08-12_26_03.log) | 测试 grep 信息和 riscv 不兼容 |
|  | oe_test_system_log_view | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_view/2023-08-08-12_20_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_view/2023-08-08-12_20_55.log) | /var/log/messages 目录不存在 |
|  | oe_test_system_monitor_login | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_login/2023-08-08-12_18_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_login/2023-08-08-12_18_29.log) | 测试 node2 不存在 |
|  | oe_test_system_monitor_reboot | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_reboot/2023-08-08-11_46_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_reboot/2023-08-08-11_46_37.log) | 测试 node2 不存在 |
|  | oe_test_system_monitor_share_total | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_share_total/2023-08-08-22_17_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_share_total/2023-08-08-22_17_13.log) | ps -eo pcpu,pid,comm,args 失败 |
|  | oe_test_uid_old_uid | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uid_old_uid/2023-08-09-00_16_59.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uid_old_uid/2023-08-09-00_16_59.log) | 测试需要中文环境 |
|  | oe_test_uname | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uname/2023-08-08-11_44_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uname/2023-08-08-11_44_44.log) | whereis帮助信息输出格式不符合 grep 参数 |
|  | oe_test_uname | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uname/2023-08-08-11_44_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_uname/2023-08-08-11_44_44.log) | whereis帮助信息输出格式不符合 grep 参数 |
|  | oe_test_whereis | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whereis/2023-08-08-11_04_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whereis/2023-08-08-11_04_34.log) | whereis帮助信息输出格式不符合 grep 参数 |
|  | oe_test_xzcmp | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_xzcmp/2023-08-09-00_07_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_xzcmp/2023-08-09-00_07_37.log) | 测试需要中文环境 |
| os-storage | oe_test_storage_DevMgmt_block_drop | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_block_drop/2023-08-13-15_07_23.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_block_drop/2023-08-13-15_07_23.log) |  |
|  | oe_test_storage_DevMgmt_disk_operation | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_disk_operation/2023-08-13-12_50_49.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_disk_operation/2023-08-13-12_50_49.log) |  |
|  | oe_test_storage_DevMgmt_lsblk | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_lsblk/2023-08-13-13_56_49.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_lsblk/2023-08-13-13_56_49.log) |  |
|  | oe_test_storage_DevMgmt_swap | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_swap/2023-08-13-13_27_16.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_DevMgmt_swap/2023-08-13-13_27_16.log) |  |
|  | oe_test_storage_Mutipath_dmsetup | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_dmsetup/2023-08-13-13_02_47.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_dmsetup/2023-08-13-13_02_47.log) |  |
|  | oe_test_storage_diskpartiton_create_raid0 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid0/2023-08-13-15_03_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid0/2023-08-13-15_03_08.log) |  |
|  | oe_test_storage_diskpartiton_create_raid1 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid1/2023-08-13-15_11_49.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid1/2023-08-13-15_11_49.log) |  |
|  | oe_test_storage_diskpartiton_create_raid5 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid5/2023-08-13-14_01_12.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_create_raid5/2023-08-13-14_01_12.log) |  |
|  | oe_test_storage_diskpartiton_fdisk | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk/2023-08-13-12_25_36.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk/2023-08-13-12_25_36.log) |  |
|  | oe_test_storage_diskpartiton_fdisk_delete | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk_delete/2023-08-13-13_34_23.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk_delete/2023-08-13-13_34_23.log) |  |
|  | oe_test_storage_diskpartiton_fdisk_settype | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk_settype/2023-08-13-14_56_28.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_fdisk_settype/2023-08-13-14_56_28.log) |  |
|  | oe_test_storage_diskpartiton_parted_resize | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_resize/2023-08-13-13_00_14.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_parted_resize/2023-08-13-13_00_14.log) |  |
|  | oe_test_storage_diskpartiton_view_fdisk | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_view_fdisk/2023-08-13-14_43_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_view_fdisk/2023-08-13-14_43_03.log) |  |
|  | oe_test_storage_ext3_create | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_create/2023-08-13-13_26_28.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_create/2023-08-13-13_26_28.log) |  |
|  | oe_test_storage_ext3_mount_write | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_mount_write/2023-08-13-14_48_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_mount_write/2023-08-13-14_48_08.log) |  |
|  | oe_test_storage_ext3_resize | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_resize/2023-08-13-12_36_56.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext3_resize/2023-08-13-12_36_56.log) |  |
|  | oe_test_storage_ext4_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_mount/2023-08-13-12_19_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_mount/2023-08-13-12_19_03.log) |  |
|  | oe_test_storage_ext4_mount_write | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_mount_write/2023-08-13-12_36_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_ext4_mount_write/2023-08-13-12_36_03.log) |  |
|  | oe_test_storage_fileCMD_cat | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cat/2023-08-13-12_04_46.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cat/2023-08-13-12_04_46.log) |  |
|  | oe_test_storage_fileCMD_dd | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_dd/2023-08-13-12_56_07.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_dd/2023-08-13-12_56_07.log) |  |
|  | oe_test_storage_fileCMD_mkfs | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mkfs/2023-08-13-13_58_45.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mkfs/2023-08-13-13_58_45.log) |  |
|  | oe_test_storage_fileCMD_pwd | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_pwd/2023-08-13-14_45_53.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_pwd/2023-08-13-14_45_53.log) |  |
|  | oe_test_storage_longname_list | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_longname_list/2023-08-13-13_25_37.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_longname_list/2023-08-13-13_25_37.log) |  |
|  | oe_test_storage_longname_modify | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_longname_modify/2023-08-13-15_21_58.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_longname_modify/2023-08-13-15_21_58.log) |  |
|  | oe_test_storage_lvm_activation_missing | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_activation_missing/2023-08-13-13_03_24.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_activation_missing/2023-08-13-13_03_24.log) |  |
|  | oe_test_storage_lvm_add_VG | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_add_VG/2023-08-13-14_04_56.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_add_VG/2023-08-13-14_04_56.log) |  |
|  | oe_test_storage_lvm_change_number | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_change_number/2023-08-13-14_46_15.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_change_number/2023-08-13-14_46_15.log) |  |
|  | oe_test_storage_lvm_cling | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_cling/2023-08-13-14_55_46.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_cling/2023-08-13-14_55_46.log) |  |
|  | oe_test_storage_lvm_create_cache | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_cache/2023-08-13-15_19_29.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_cache/2023-08-13-15_19_29.log) |  |
|  | oe_test_storage_lvm_create_raid | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_raid/2023-08-13-15_05_57.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_raid/2023-08-13-15_05_57.log) |  |
|  | oe_test_storage_lvm_create_snapshot | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_snapshot/2023-08-13-14_00_30.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_snapshot/2023-08-13-14_00_30.log) |  |
|  | oe_test_storage_lvm_create_thinSnapshot | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_thinSnapshot/2023-08-13-14_39_43.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_create_thinSnapshot/2023-08-13-14_39_43.log) |  |
|  | oe_test_storage_lvm_expand_stripeLV | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_expand_stripeLV/2023-08-13-13_02_04.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_expand_stripeLV/2023-08-13-13_02_04.log) |  |
|  | oe_test_storage_lvm_lvdisplay_lvscan | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_lvdisplay_lvscan/2023-08-13-15_06_41.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_lvdisplay_lvscan/2023-08-13-15_06_41.log) |  |
|  | oe_test_storage_lvm_merge_VG | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_merge_VG/2023-08-13-14_23_33.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_merge_VG/2023-08-13-14_23_33.log) |  |
|  | oe_test_storage_lvm_query_lvmsnap | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_query_lvmsnap/2023-08-13-13_55_52.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_query_lvmsnap/2023-08-13-13_55_52.log) |  |
|  | oe_test_storage_lvm_replace_badraid | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_replace_badraid/2023-08-13-12_19_43.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_replace_badraid/2023-08-13-12_19_43.log) |  |
|  | oe_test_storage_lvm_replace_raid | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_replace_raid/2023-08-13-15_01_35.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_replace_raid/2023-08-13-15_01_35.log) |  |
|  | oe_test_storage_lvm_separate_raid | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_separate_raid/2023-08-13-13_32_18.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_separate_raid/2023-08-13-13_32_18.log) |  |
|  | oe_test_storage_lvm_set_label | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_label/2023-08-13-12_13_54.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_label/2023-08-13-12_13_54.log) |  |
|  | oe_test_storage_lvm_set_limit | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_limit/2023-08-13-13_10_01.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_limit/2023-08-13-13_10_01.log) |  |
|  | oe_test_storage_lvm_set_lvconvert_raid | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_raid/2023-08-13-14_10_19.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_raid/2023-08-13-14_10_19.log) |  |
|  | oe_test_storage_lvm_set_lvconvert_raid1 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_raid1/2023-08-13-14_20_57.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_lvconvert_raid1/2023-08-13-14_20_57.log) |  |
|  | oe_test_storage_lvm_snapshot_merge | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_snapshot_merge/2023-08-13-14_16_16.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_snapshot_merge/2023-08-13-14_16_16.log) |  |
|  | oe_test_storage_lvm_snapshot_tag | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_snapshot_tag/2023-08-13-13_33_30.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_snapshot_tag/2023-08-13-13_33_30.log) |  |
|  | oe_test_storage_lvm_specify_size | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_specify_size/2023-08-13-13_22_11.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_specify_size/2023-08-13-13_22_11.log) |  |
|  | oe_test_storage_lvm_split_VG | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_split_VG/2023-08-13-13_57_45.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_split_VG/2023-08-13-13_57_45.log) |  |
|  | oe_test_storage_mount_UUID | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_UUID/2023-08-13-14_49_51.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_UUID/2023-08-13-14_49_51.log) |  |
|  | oe_test_storage_mount_block_device | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_block_device/2023-08-13-12_37_29.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_block_device/2023-08-13-12_37_29.log) |  |
|  | oe_test_storage_mount_copy | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_copy/2023-08-13-14_41_04.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_copy/2023-08-13-14_41_04.log) |  |
|  | oe_test_storage_mount_findmnt | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_findmnt/2023-08-13-12_50_12.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_findmnt/2023-08-13-12_50_12.log) |  |
|  | oe_test_storage_mount_mobile_point | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_mobile_point/2023-08-13-14_03_58.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_mobile_point/2023-08-13-14_03_58.log) |  |
|  | oe_test_storage_mount_repeat | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_repeat/2023-08-13-13_08_40.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_repeat/2023-08-13-13_08_40.log) |  |
|  | oe_test_storage_mount_umount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_umount/2023-08-13-15_20_41.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_umount/2023-08-13-15_20_41.log) |  |
|  | oe_test_storage_nfs_configure_nfsv4 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_configure_nfsv4/2023-08-13-13_15_30.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_configure_nfsv4/2023-08-13-13_15_30.log) |  |
|  | oe_test_storage_nfs_mount_readonly | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_readonly/2023-08-13-12_38_09.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_readonly/2023-08-13-12_38_09.log) |  |
|  | oe_test_storage_nfs_mount_root | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_root/2023-08-13-14_37_17.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_mount_root/2023-08-13-14_37_17.log) |  |
|  | oe_test_storage_nfs_network_latency | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_network_latency/2023-08-13-12_56_41.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_network_latency/2023-08-13-12_56_41.log) |  |
|  | oe_test_storage_nfs_repeat_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_mount/2023-08-13-13_37_35.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_mount/2023-08-13-13_37_35.log) |  |
|  | oe_test_storage_nfs_repeat_restartServer | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_restartServer/2023-08-13-14_05_45.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_repeat_restartServer/2023-08-13-14_05_45.log) |  |
|  | oe_test_storage_nfs_restrict_hostname | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_restrict_hostname/2023-08-13-14_09_12.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_restrict_hostname/2023-08-13-14_09_12.log) |  |
|  | oe_test_storage_nfs_restrict_ip | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_restrict_ip/2023-08-13-13_35_09.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_restrict_ip/2023-08-13-13_35_09.log) |  |
|  | oe_test_storage_nfs_share_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_share_mount/2023-08-13-12_26_17.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_share_mount/2023-08-13-12_26_17.log) |  |
|  | oe_test_storage_nfs_v3_v4 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_v3_v4/2023-08-13-12_41_55.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_v3_v4/2023-08-13-12_41_55.log) |  |
|  | oe_test_storage_nfs_write_full | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_write_full/2023-08-13-13_04_57.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_write_full/2023-08-13-13_04_57.log) |  |
|  | oe_test_storage_repair_e2fsck | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_repair_e2fsck/2023-08-13-14_36_22.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_repair_e2fsck/2023-08-13-14_36_22.log) |  |
|  | oe_test_storage_repair_xfs | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_repair_xfs/2023-08-13-14_34_38.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_repair_xfs/2023-08-13-14_34_38.log) |  |
|  | oe_test_storage_smb_3.0 | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_3.0/2023-08-13-13_59_32.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_3.0/2023-08-13-13_59_32.log) |  |
|  | oe_test_storage_smb_POSIX_ACL | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_POSIX_ACL/2023-08-13-13_00_54.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_POSIX_ACL/2023-08-13-13_00_54.log) |  |
|  | oe_test_storage_smb_abnormal_poweroff | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_abnormal_poweroff/2023-08-13-12_46_55.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_abnormal_poweroff/2023-08-13-12_46_55.log) |  |
|  | oe_test_storage_smb_automatically_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_automatically_mount/2023-08-13-13_54_53.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_automatically_mount/2023-08-13-13_54_53.log) |  |
|  | oe_test_storage_smb_cmd_smbclient | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbclient/2023-08-13-12_24_31.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbclient/2023-08-13-12_24_31.log) |  |
|  | oe_test_storage_smb_cmd_smbpasswd | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbpasswd/2023-08-13-12_20_40.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbpasswd/2023-08-13-12_20_40.log) |  |
|  | oe_test_storage_smb_credential_files | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_credential_files/2023-08-13-13_11_48.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_credential_files/2023-08-13-13_11_48.log) |  |
|  | oe_test_storage_smb_host_share | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_host_share/2023-08-13-13_10_48.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_host_share/2023-08-13-13_10_48.log) |  |
|  | oe_test_storage_smb_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_mount/2023-08-13-14_44_50.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_mount/2023-08-13-14_44_50.log) |  |
|  | oe_test_storage_smb_mount_umount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_mount_umount/2023-08-13-12_05_04.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_mount_umount/2023-08-13-12_05_04.log) |  |
|  | oe_test_storage_smb_multi-user_mount | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_multi-user_mount/2023-08-13-14_53_23.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_multi-user_mount/2023-08-13-14_53_23.log) |  |
|  | oe_test_storage_smb_network_latency | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_network_latency/2023-08-13-14_41_51.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_network_latency/2023-08-13-14_41_51.log) |  |
|  | oe_test_storage_smb_repeat_restart | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_repeat_restart/2023-08-13-13_18_36.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_repeat_restart/2023-08-13-13_18_36.log) |  |
|  | oe_test_storage_smb_share_dir | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_share_dir/2023-08-13-14_54_22.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_share_dir/2023-08-13-14_54_22.log) |  |
|  | oe_test_storage_smb_write_full | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_write_full/2023-08-13-15_00_09.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_write_full/2023-08-13-15_00_09.log) |  |
|  | oe_test_storage_vfat_mount_write | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_vfat_mount_write/2023-08-13-14_58_03.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_vfat_mount_write/2023-08-13-14_58_03.log) |  |
|  | oe_test_storage_xfs_Increase_size | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_Increase_size/2023-08-13-14_24_14.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_Increase_size/2023-08-13-14_24_14.log) |  |
|  | oe_test_storage_xfs_backup | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_backup/2023-08-13-15_08_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_backup/2023-08-13-15_08_08.log) |  |
|  | oe_test_storage_xfs_create | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_create/2023-08-13-15_10_50.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_create/2023-08-13-15_10_50.log) |  |
|  | oe_test_storage_xfs_restore | fail | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_restore/2023-08-13-14_29_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_xfs_restore/2023-08-13-14_29_08.log) |  |
| patchutils | oe_test_patchutils_combinediff_01 | fail | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_combinediff_01/2023-08-08-09_49_51.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_combinediff_01/2023-08-08-09_49_51.log) | combinediff 失败 |
|  | oe_test_patchutils_flipdiff_01 | fail | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_flipdiff_01/2023-08-08-09_54_04.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_flipdiff_01/2023-08-08-09_54_04.log) | 出错一致 |
|  | oe_test_patchutils_interdiff_01 | fail | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_interdiff_01/2023-08-08-09_58_35.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_interdiff_01/2023-08-08-09_58_35.log) | 出错一致 |
| pbzip2 | oe_test_pbzip2_03 | fail | [./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_03/2023-08-08-10_47_03.log](./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_03/2023-08-08-10_47_03.log) |  |
| pcp | oe_test_pcp_atop_01 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_atop_01/2023-08-12-13_55_38.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_atop_01/2023-08-12-13_55_38.log) |  |
|  | oe_test_pcp_atop_02 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_atop_02/2023-08-12-14_07_08.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_atop_02/2023-08-12-14_07_08.log) |  |
|  | oe_test_pcp_pcp-import-ganglia2pcp | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-ganglia2pcp/2023-08-12-14_22_11.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-ganglia2pcp/2023-08-12-14_22_11.log) |  |
|  | oe_test_pmevent_02 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmevent_02/2023-08-12-13_04_59.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmevent_02/2023-08-12-13_04_59.log) |  |
|  | oe_test_pmhostname_pmlock_pmlogger_check | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmhostname_pmlock_pmlogger_check/2023-08-12-13_37_03.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmhostname_pmlock_pmlogger_check/2023-08-12-13_37_03.log) |  |
|  | oe_test_pmlogcheck_pmlogmv | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogcheck_pmlogmv/2023-08-12-13_11_59.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogcheck_pmlogmv/2023-08-12-13_11_59.log) |  |
|  | oe_test_pmlogger_daily_01 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_01/2023-08-12-13_39_53.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_01/2023-08-12-13_39_53.log) |  |
|  | oe_test_pmlogger_daily_report | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_report/2023-08-12-13_51_07.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_report/2023-08-12-13_51_07.log) |  |
|  | oe_test_pmprobe_01 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmprobe_01/2023-08-12-13_20_16.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmprobe_01/2023-08-12-13_20_16.log) |  |
|  | oe_test_pmval_02 | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmval_02/2023-08-12-13_30_22.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmval_02/2023-08-12-13_30_22.log) |  |
|  | oe_test_service_pmmgr | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmmgr/2023-08-12-14_49_50.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmmgr/2023-08-12-14_49_50.log) |  |
|  | oe_test_service_pmwebd | fail | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmwebd/2023-08-12-14_53_13.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmwebd/2023-08-12-14_53_13.log) |  |
| pesign | oe_test_pesign_base_02 | fail | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_base_02/2023-08-08-10_14_18.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_base_02/2023-08-08-10_14_18.log) |  |
|  | oe_test_pesign_efikeygen | fail | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_efikeygen/2023-08-08-10_11_30.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_efikeygen/2023-08-08-10_11_30.log) |  |
|  | oe_test_pesign_efisiglist | fail | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_efisiglist/2023-08-08-10_12_27.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_efisiglist/2023-08-08-10_12_27.log) |  |
|  | oe_test_pesign_pesigcheck | fail | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesigcheck/2023-08-08-10_18_00.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesigcheck/2023-08-08-10_18_00.log) |  |
| plymouth | oe_test_service_plymouth-switch-root-initramfs | fail | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-switch-root-initramfs/2023-08-08-10_07_32.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-switch-root-initramfs/2023-08-08-10_07_32.log) | systemd单元启动失败，Condition check resulted in Tell Plymouth To Jump To initramfs being skipped. |
| policycoreutils | oe_test_service_restorecond | fail | [./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_restorecond/2023-08-08-20_06_27.log](./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_restorecond/2023-08-08-20_06_27.log) | Unit restorecond.service not found. |
| qemu | oe_test_service_qemu-guest-agent | fail | [./oe-rv2309/mugen-riscv/logs/qemu/oe_test_service_qemu-guest-agent/2023-08-12-23_31_54.log](./oe-rv2309/mugen-riscv/logs/qemu/oe_test_service_qemu-guest-agent/2023-08-12-23_31_54.log) |  |
| qpdf | oe_test_qpdf_qpdf_01 | fail | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_01/2023-08-09-03_34_35.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_01/2023-08-09-03_34_35.log) | mugen脚本编写问题，grep内容与输出不符 |
|  | oe_test_qpdf_qpdf_02 | fail | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_02/2023-08-09-03_35_40.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_02/2023-08-09-03_35_40.log) | qpdf无法使用弱密码算法写入文件 |
|  | oe_test_qpdf_qpdf_03 | fail | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_03/2023-08-09-03_36_48.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_03/2023-08-09-03_36_48.log) | qpdf无法使用弱密码算法写入文件 |
|  | oe_test_qpdf_qpdf_08 | fail | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_08/2023-08-09-03_42_08.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_08/2023-08-09-03_42_08.log) | qpdf无法使用弱密码算法写入文件 |
|  | oe_test_qpdf_qpdf_10 | fail | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_10/2023-08-09-03_44_10.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_10/2023-08-09-03_44_10.log) | mugen脚本编写问题，grep内容与输出不符 |
| radvd | oe_test_service_radvd | fail | [./oe-rv2309/mugen-riscv/logs/radvd/oe_test_service_radvd/2023-08-09-13_28_08.log](./oe-rv2309/mugen-riscv/logs/radvd/oe_test_service_radvd/2023-08-09-13_28_08.log) | /etc/radvd.conf:20 error: syntax error |
| rasdaemon | oe_test_service_ras-mc-ctl | fail | [./oe-rv2309/mugen-riscv/logs/rasdaemon/oe_test_service_ras-mc-ctl/2023-08-08-16_10_56.log](./oe-rv2309/mugen-riscv/logs/rasdaemon/oe_test_service_ras-mc-ctl/2023-08-08-16_10_56.log) | Missing command in piped open at /usr/sbin/ras-mc-ctl line 413. |
| rng-tools | oe_test_service_rngd | fail | [./oe-rv2309/mugen-riscv/logs/rng-tools/oe_test_service_rngd/2023-08-09-05_11_18.log](./oe-rv2309/mugen-riscv/logs/rng-tools/oe_test_service_rngd/2023-08-09-05_11_18.log) | 软件rng-tools未安装 |
| rpmdevtools | oe_test_rpmdevtools_rpmdev-wipetree | fail | [./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-wipetree/2023-08-09-04_13_07.log](./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-wipetree/2023-08-09-04_13_07.log) | 受 qemu 运行速度影响，手动重测通过 |
| rsyslog | oe_test_rsyslog_abnormal_template | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_abnormal_template/2023-08-08-00_46_07.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_abnormal_template/2023-08-08-00_46_07.log) |  |
|  | oe_test_rsyslog_function_attribute | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_attribute/2023-08-08-00_46_52.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_attribute/2023-08-08-00_46_52.log) |  |
|  | oe_test_rsyslog_function_httpd | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_httpd/2023-08-08-00_48_49.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_httpd/2023-08-08-00_48_49.log) |  |
|  | oe_test_rsyslog_function_tcp | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_tcp/2023-08-08-01_24_17.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_tcp/2023-08-08-01_24_17.log) |  |
|  | oe_test_rsyslog_function_tcp_firewall | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_tcp_firewall/2023-08-08-01_25_00.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_tcp_firewall/2023-08-08-01_25_00.log) |  |
|  | oe_test_rsyslog_function_udp | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_udp/2023-08-08-01_26_07.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_udp/2023-08-08-01_26_07.log) |  |
|  | oe_test_rsyslog_reliability_lenght | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_lenght/2023-08-08-01_57_59.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_lenght/2023-08-08-01_57_59.log) |  |
|  | oe_test_rsyslog_reliability_network | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_network/2023-08-08-01_58_42.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_network/2023-08-08-01_58_42.log) |  |
|  | oe_test_rsyslog_reliability_tcp | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_tcp/2023-08-08-02_30_58.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_tcp/2023-08-08-02_30_58.log) |  |
| ruby | oe_test_erb | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_erb/2023-08-08-10_53_36.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_erb/2023-08-08-10_53_36.log) | 出错一致 |
|  | oe_test_irb_01 | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_01/2023-08-08-11_01_55.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_01/2023-08-08-11_01_55.log) | 出错一致 |
|  | oe_test_irb_03 | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_03/2023-08-08-11_16_35.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_03/2023-08-08-11_16_35.log) | 出错一致 |
|  | oe_test_rdoc_01 | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rdoc_01/2023-08-08-11_39_34.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rdoc_01/2023-08-08-11_39_34.log) | 出错一致 |
|  | oe_test_ruby | fail | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_ruby/2023-08-08-12_07_22.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_ruby/2023-08-08-12_07_22.log) | 出错一致 ruby: invalid option -T |
| samba | oe_test_service_samba | fail | [./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_samba/2023-08-08-14_03_22.log](./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_samba/2023-08-08-14_03_22.log) | samba.service 停止失败，‘/etc/systemd/system/multi-user.target.wants/samba.service’: No such file or directory |
| security-tool | oe_test_security_tool | fail | [./oe-rv2309/mugen-riscv/logs/security-tool/oe_test_security_tool/2023-08-08-16_08_08.log](./oe-rv2309/mugen-riscv/logs/security-tool/oe_test_security_tool/2023-08-08-16_08_08.log) | security-tools 软件包没有预装也没有显式安装 |
|  | oe_test_service_openEuler-security | fail | [./oe-rv2309/mugen-riscv/logs/security-tool/oe_test_service_openEuler-security/2023-08-08-16_12_53.log](./oe-rv2309/mugen-riscv/logs/security-tool/oe_test_service_openEuler-security/2023-08-08-16_12_53.log) | Unit openEuler-security.service could not be found. |
| sendmail | oe_test_sendmail_func_001 | fail | [./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_sendmail_func_001/2023-08-08-15_57_05.log](./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_sendmail_func_001/2023-08-08-15_57_05.log) | 测试依赖 lsof ，该命令既没有预装也没有显式安装 |
| smoke-basic-os | oe_test_CPUinfo_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_CPUinfo_001/2023-08-08-09_51_27.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_CPUinfo_001/2023-08-08-09_51_27.log) | 测试超时 |
|  | oe_test_MEMinfo_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_MEMinfo_001/2023-08-08-10_38_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_MEMinfo_001/2023-08-08-10_38_47.log) | 测试套问题，预期的 lshw 输出不适合 rv 架构 |
|  | oe_test_bbr_02 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bbr_02/2023-08-08-13_43_52.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bbr_02/2023-08-08-13_43_52.log) | /proc/sys/net/ipv4/tcp_congestion_control 无法写入 |
|  | oe_test_cat_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cat_001/2023-08-08-09_50_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cat_001/2023-08-08-09_50_19.log) | 出错一致 |
|  | oe_test_criu | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_criu/2023-08-08-10_21_33.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_criu/2023-08-08-10_21_33.log) | 待测软件包 criu 不存在 |
|  | oe_test_dumpe2fs | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dumpe2fs/2023-08-08-14_03_29.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dumpe2fs/2023-08-08-14_03_29.log) | 将硬盘格式化时出现问题，无法在上面创建文件inode |
|  | oe_test_ebtables | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ebtables/2023-08-08-11_50_00.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ebtables/2023-08-08-11_50_00.log) | 测试测试 ebtables 命令，该软件包没有预装也没有显式安装 |
|  | oe_test_ethtool_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ethtool_01/2023-08-08-11_50_11.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ethtool_01/2023-08-08-11_50_11.log) | 测试测试 ethtool 命令，该软件包没有预装也没有显式安装 |
|  | oe_test_firewalld_server | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_firewalld_server/2023-08-08-11_50_43.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_firewalld_server/2023-08-08-11_50_43.log) | /var/lib/ebtables/lock 建立失败 |
|  | oe_test_gettext | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gettext/2023-08-08-12_21_58.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gettext/2023-08-08-12_21_58.log) | 测试需要中文环境 |
|  | oe_test_grub2_mkconfig | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_grub2_mkconfig/2023-08-08-11_52_08.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_grub2_mkconfig/2023-08-08-11_52_08.log) | grub2-mkconfig 命令不存在 |
|  | oe_test_logrotate_004 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_004/2023-08-08-14_00_57.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_004/2023-08-08-14_00_57.log) | rsyslogd: no process found |
|  | oe_test_mkdosfs | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mkdosfs/2023-08-08-14_02_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mkdosfs/2023-08-08-14_02_47.log) | 将硬盘格式化时出现问题，无法在上面创建文件inode |
|  | oe_test_mke2fs | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mke2fs/2023-08-08-14_04_17.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mke2fs/2023-08-08-14_04_17.log) | 将硬盘格式化时出现问题，无法在上面创建文件inode |
|  | oe_test_ncurses | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ncurses/2023-08-08-12_09_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ncurses/2023-08-08-12_09_34.log) | Failed to execute infotocap |
|  | oe_test_network_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_network_001/2023-08-08-10_41_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_network_001/2023-08-08-10_41_13.log) | 测试依赖 ethtool 命令，该软件包没有预装也没有显式安装 |
|  | oe_test_perf | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perf/2023-08-08-10_45_07.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perf/2023-08-08-10_45_07.log) | 测试出错 cycles: PMU Hardware doesn't support sampling/overflow-interrupts. Try 'perf stat' |
|  | oe_test_perf_top_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perf_top_01/2023-08-08-13_50_23.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perf_top_01/2023-08-08-13_50_23.log) | 测试出错 cycles: PMU Hardware doesn't support sampling/overflow-interrupts. Try 'perf stat' |
|  | oe_test_pwd_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pwd_001/2023-08-08-10_46_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pwd_001/2023-08-08-10_46_04.log) | 测试使用的 /etc/kernel 目录不存在 |
|  | oe_test_rsyslog_03 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_03/2023-08-08-12_28_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_03/2023-08-08-12_28_05.log) | Unit rsyslog.service not found. |
|  | oe_test_setools | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_setools/2023-08-08-12_50_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_setools/2023-08-08-12_50_05.log) | seinfo 输出不符合预期 |
|  | oe_test_sevice_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sevice_001/2023-08-08-10_47_20.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sevice_001/2023-08-08-10_47_20.log) | systemctl -t service 输出不符合预期 |
|  | oe_test_syslog_logrotate_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_logrotate_001/2023-08-08-10_51_20.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_logrotate_001/2023-08-08-10_51_20.log) | /var/log/messages 目录不存在 |
|  | oe_test_yumgroup_001 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_yumgroup_001/2023-08-08-10_54_21.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_yumgroup_001/2023-08-08-10_54_21.log) | oe-rv目前软件仓内并没有划分软件包组 |
| strongswan | oe_test_service_strongswan | fail | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan/2023-08-12-20_13_53.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan/2023-08-12-20_13_53.log) |  |
|  | oe_test_service_strongswan-starter | fail | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan-starter/2023-08-12-20_34_12.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan-starter/2023-08-12-20_34_12.log) |  |
|  | oe_test_service_strongswan-swanctl | fail | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan-swanctl/2023-08-12-20_19_02.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan-swanctl/2023-08-12-20_19_02.log) |  |
|  | oe_test_service_strongswan_01 | fail | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_01/2023-08-12-20_20_32.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_01/2023-08-12-20_20_32.log) |  |
|  | oe_test_service_strongswan_03 | fail | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_03/2023-08-12-20_24_19.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_03/2023-08-12-20_24_19.log) |  |
| systemd | oe_test_service_console-getty | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_console-getty/2023-08-08-00_03_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_console-getty/2023-08-08-00_03_59.log) |  |
|  | oe_test_service_dbus-org.freedesktop.import1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.import1/2023-08-08-00_05_04.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.import1/2023-08-08-00_05_04.log) |  |
|  | oe_test_service_dbus-org.freedesktop.portable1 | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.portable1/2023-08-08-00_08_05.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.portable1/2023-08-08-00_08_05.log) |  |
|  | oe_test_service_quotaon | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_quotaon/2023-08-08-00_12_05.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_quotaon/2023-08-08-00_12_05.log) |  |
|  | oe_test_service_systemd-ask-password-wall | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-ask-password-wall/2023-08-08-00_14_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-ask-password-wall/2023-08-08-00_14_50.log) |  |
|  | oe_test_service_systemd-boot-check-no-failures | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-boot-check-no-failures/2023-08-08-00_15_43.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-boot-check-no-failures/2023-08-08-00_15_43.log) |  |
|  | oe_test_service_systemd-importd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-importd/2023-08-08-00_19_00.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-importd/2023-08-08-00_19_00.log) |  |
|  | oe_test_service_systemd-initctl | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-initctl/2023-08-08-00_19_41.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-initctl/2023-08-08-00_19_41.log) |  |
|  | oe_test_service_systemd-journal-gatewayd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-gatewayd/2023-08-08-00_21_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-gatewayd/2023-08-08-00_21_59.log) |  |
|  | oe_test_service_systemd-journal-remote | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-remote/2023-08-08-00_22_54.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-remote/2023-08-08-00_22_54.log) |  |
|  | oe_test_service_systemd-journal-upload | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-upload/2023-08-08-00_23_48.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-journal-upload/2023-08-08-00_23_48.log) |  |
|  | oe_test_service_systemd-networkd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-networkd/2023-08-08-00_27_00.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-networkd/2023-08-08-00_27_00.log) |  |
|  | oe_test_service_systemd-networkd-wait-online | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-networkd-wait-online/2023-08-08-00_27_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-networkd-wait-online/2023-08-08-00_27_34.log) |  |
|  | oe_test_service_systemd-portabled | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-portabled/2023-08-08-00_28_18.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-portabled/2023-08-08-00_28_18.log) |  |
|  | oe_test_service_systemd-quotacheck | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-quotacheck/2023-08-08-00_29_09.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-quotacheck/2023-08-08-00_29_09.log) |  |
|  | oe_test_service_systemd-resolved | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-resolved/2023-08-08-00_30_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-resolved/2023-08-08-00_30_59.log) |  |
|  | oe_test_service_systemd-sysext | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysext/2023-08-08-01_10_58.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-sysext/2023-08-08-01_10_58.log) |  |
|  | oe_test_service_systemd-udevd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udevd/2023-08-08-00_35_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-udevd/2023-08-08-00_35_59.log) |  |
|  | oe_test_service_systemd-userdbd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-userdbd/2023-08-08-01_11_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-userdbd/2023-08-08-01_11_34.log) |  |
|  | oe_test_socket_syslog | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_syslog/2023-08-08-00_40_32.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_syslog/2023-08-08-00_40_32.log) |  |
|  | oe_test_socket_systemd-journal-gatewayd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journal-gatewayd/2023-08-08-00_43_59.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journal-gatewayd/2023-08-08-00_43_59.log) |  |
|  | oe_test_socket_systemd-journal-remote | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journal-remote/2023-08-08-00_44_56.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journal-remote/2023-08-08-00_44_56.log) |  |
|  | oe_test_socket_systemd-journald-dev-log | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald-dev-log/2023-08-08-00_42_49.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-journald-dev-log/2023-08-08-00_42_49.log) |  |
|  | oe_test_socket_systemd-networkd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-networkd/2023-08-08-00_45_53.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-networkd/2023-08-08-00_45_53.log) |  |
|  | oe_test_socket_systemd-rfkill | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-rfkill/2023-08-08-00_46_27.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-rfkill/2023-08-08-00_46_27.log) |  |
|  | oe_test_target_cryptsetup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_cryptsetup/2023-08-08-00_49_33.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_cryptsetup/2023-08-08-00_49_33.log) |  |
|  | oe_test_target_cryptsetup-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_cryptsetup-pre/2023-08-08-00_49_20.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_cryptsetup-pre/2023-08-08-00_49_20.log) |  |
|  | oe_test_target_remote-cryptsetup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-cryptsetup/2023-08-08-00_59_54.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-cryptsetup/2023-08-08-00_59_54.log) |  |
|  | oe_test_target_remote-veritysetup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-veritysetup/2023-08-08-01_12_55.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_remote-veritysetup/2023-08-08-01_12_55.log) |  |
|  | oe_test_target_usb-gadget | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_usb-gadget/2023-08-08-01_13_30.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_usb-gadget/2023-08-08-01_13_30.log) |  |
|  | oe_test_target_veritysetup | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_veritysetup/2023-08-08-01_14_04.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_veritysetup/2023-08-08-01_14_04.log) |  |
|  | oe_test_target_veritysetup-pre | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_veritysetup-pre/2023-08-08-01_14_38.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_veritysetup-pre/2023-08-08-01_14_38.log) |  |
| tuned | oe_test_service_tuned | fail | [./oe-rv2309/mugen-riscv/logs/tuned/oe_test_service_tuned/2023-08-08-17_23_40.log](./oe-rv2309/mugen-riscv/logs/tuned/oe_test_service_tuned/2023-08-08-17_23_40.log) | Unit tuned.service not found. |
| unbound | oe_test_service_unbound-keygen | fail | [./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound-keygen/2023-08-08-15_30_38.log](./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound-keygen/2023-08-08-15_30_38.log) | /sbin/restorecon: No such file or directory |


下表内的测试套和测试用例均为在 23.03 上和 23.09 上均失败的 BaseOS 测试用例，且原因不同

| 测试套/软件包名 | 测试用例名 | 状态 | 日志文件 | 原因 | 
| :---: | :---: | :---: | :---: | :---: | 
| FS_FileSystem | oe_test_FSIO_change_fs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_change_fs/2023-08-09-02_13_49.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_change_fs/2023-08-09-02_13_49.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_create_ext3 | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_ext3/2023-08-09-02_14_46.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_ext3/2023-08-09-02_14_46.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_create_ext4 | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_ext4/2023-08-09-02_15_08.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_ext4/2023-08-09-02_15_08.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_create_squashfs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_squashfs/2023-08-11-02_29_40.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_squashfs/2023-08-11-02_29_40.log) | 软件squashfs-tools未安装 |
|  | oe_test_FSIO_create_xfs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_xfs/2023-08-09-02_15_26.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_create_xfs/2023-08-09-02_15_26.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_dd_mkfs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_dd_mkfs/2023-08-11-00_24_13.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_dd_mkfs/2023-08-11-00_24_13.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_loop_fs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_loop_fs/2023-08-11-00_32_47.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_loop_fs/2023-08-11-00_32_47.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_automount | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_automount/2023-08-11-00_33_37.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_automount/2023-08-11-00_33_37.log) | ssh连接不稳定，加之虚拟机重启后可能失联导致超时失败 |
|  | oe_test_FSIO_mount_block_dir | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_block_dir/2023-08-09-02_21_27.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_block_dir/2023-08-09-02_21_27.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_character_dir | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_character_dir/2023-08-11-01_04_36.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_character_dir/2023-08-11-01_04_36.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_empty_dir | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_empty_dir/2023-08-11-01_05_15.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_empty_dir/2023-08-11-01_05_15.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_nonempty_dir | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_nonempty_dir/2023-08-09-02_31_18.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_nonempty_dir/2023-08-09-02_31_18.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_reboot | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_reboot/2023-08-11-01_18_27.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_reboot/2023-08-11-01_18_27.log) | ssh连接不稳定，加之虚拟机重启后可能失联导致超时失败 |
|  | oe_test_FSIO_mount_remount | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_remount/2023-08-09-02_31_49.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_remount/2023-08-09-02_31_49.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_umount_xfs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_xfs/2023-08-09-02_33_12.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_xfs/2023-08-09-02_33_12.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_dax | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_dax/2023-08-09-02_33_42.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_dax/2023-08-09-02_33_42.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_discard | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_discard/2023-08-09-02_34_47.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_discard/2023-08-09-02_34_47.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_nodiscard | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_nodiscard/2023-08-09-02_35_08.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_nodiscard/2023-08-09-02_35_08.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_ro | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_ro/2023-08-09-02_35_30.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_ro/2023-08-09-02_35_30.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_rw | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_rw/2023-08-09-02_35_53.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_rw/2023-08-09-02_35_53.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_mount_xfs_sync | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_sync/2023-08-09-02_36_15.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_sync/2023-08-09-02_36_15.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_remkfs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_remkfs/2023-08-09-02_43_10.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_remkfs/2023-08-09-02_43_10.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_umount_busy | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_umount_busy/2023-08-11-02_27_02.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_umount_busy/2023-08-11-02_27_02.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_umount_by_openfile | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_umount_by_openfile/2023-08-09-02_54_55.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_umount_by_openfile/2023-08-09-02_54_55.log) | preinstall absent，软件xfsprogs未安装 |
|  | oe_test_FSIO_xfs_resize2fs | fail | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_xfs_resize2fs/2023-08-09-02_55_19.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_xfs_resize2fs/2023-08-09-02_55_19.log) | preinstall absent，软件xfsprogs未安装 |
| OpenIPMI | oe_test_service_ipmi | fail | [./oe-rv2309/mugen-riscv/logs/OpenIPMI/oe_test_service_ipmi/2023-08-08-11_24_14.log](./oe-rv2309/mugen-riscv/logs/OpenIPMI/oe_test_service_ipmi/2023-08-08-11_24_14.log) | 09异常退出 |
| PackageKit | oe_test_packagekit_pkcon | fail | [./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_packagekit_pkcon/2023-08-08-10_57_28.log](./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_packagekit_pkcon/2023-08-08-10_57_28.log) | 没有对应的软件包 |
| clevis | oe_test_install_clevis | fail | [./oe-rv2309/mugen-riscv/logs/clevis/oe_test_install_clevis/2023-08-08-13_33_02.log](./oe-rv2309/mugen-riscv/logs/clevis/oe_test_install_clevis/2023-08-08-13_33_02.log) | 软件包 clevis 依赖问题 nothing provides libcrypto.so.1.1()(64bit) nothing provides libcrypto.so.1.1(OPENSSL_1_1_0)(64bit) |
| cockpit | oe_test_service_cockpit | fail | [./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit/2023-08-08-10_37_01.log](./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit/2023-08-08-10_37_01.log) |  |
|  | oe_test_socket_cockpit | fail | [./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_socket_cockpit/2023-08-08-10_38_00.log](./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_socket_cockpit/2023-08-08-10_38_00.log) |  |
| gdm | oe_test_service_gdm | fail | [./oe-rv2309/mugen-riscv/logs/gdm/oe_test_service_gdm/2023-08-08-20_22_28.log](./oe-rv2309/mugen-riscv/logs/gdm/oe_test_service_gdm/2023-08-08-20_22_28.log) | 2309上没有gnome-shell的依赖 package gdm-1:43.0-1.oe2309.riscv64 requires gnome-shell, but none of the providers can be installed |
| iSulad | oe_test_iSulad_container | fail | [./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_container/2023-08-09-04_19_54.log](./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_container/2023-08-09-04_19_54.log) | 软件包安装时发生依赖错误 nothing provides libabsl_synchronization.so.2206.0.0()(64bit) needed by iSulad-2.1.2-4.oe2309.riscv64 |
|  | oe_test_iSulad_resource_mapping | fail | [./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_resource_mapping/2023-08-09-04_18_00.log](./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_resource_mapping/2023-08-09-04_18_00.log) | 软件包安装时发生依赖错误 |
|  | oe_test_iSulad_resource_restriction_cgroup | fail | [./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_resource_restriction_cgroup/2023-08-09-04_19_03.log](./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_iSulad_resource_restriction_cgroup/2023-08-09-04_19_03.log) | 软件包安装时发生依赖错误 |
|  | oe_test_service_isulad | fail | [./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_service_isulad/2023-08-09-04_16_32.log](./oe-rv2309/mugen-riscv/logs/iSulad/oe_test_service_isulad/2023-08-09-04_16_32.log) | 软件包安装时发生依赖错误 |
| initscripts | oe_test_service_import-state | fail | [./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_import-state/2023-08-09-10_01_31.log](./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_import-state/2023-08-09-10_01_31.log) | import-state.service启动命令返回值为零但启动失败 |
| ipmitool | oe_test_service_ipmievd | fail | [./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_ipmievd/2023-08-08-10_58_31.log](./oe-rv2309/mugen-riscv/logs/ipmitool/oe_test_service_ipmievd/2023-08-08-10_58_31.log) | 内核模块 ipmi_si 不存在 |
| javapackages-tools | oe_test_gradle-local | fail | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_gradle-local/2023-08-08-10_18_41.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_gradle-local/2023-08-08-10_18_41.log) |  |
| kmod | oe_test_depmod | fail | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_depmod/2023-08-09-03_48_11.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_depmod/2023-08-09-03_48_11.log) | 与oe2303x86的情况相同，find Module.symvers与System.map均为空 |
|  | oe_test_modinfo | fail | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_modinfo/2023-08-09-03_51_22.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_modinfo/2023-08-09-03_51_22.log) | modinfo -p raid1 无输出 |
| libosinfo | oe_test_osinfo-db-import | fail | [./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-db-import/2023-08-08-10_53_45.log](./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-db-import/2023-08-08-10_53_45.log) | 导出文件名按照当前时间命名，测试脚本中写死了 |
| lvm2 | oe_test_lvm2_pvcreate_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvcreate_001/2023-08-09-03_24_22.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvcreate_001/2023-08-09-03_24_22.log) | mugen中suite2cases文档中无add disk信息 |
|  | oe_test_lvm2_pvcreate_002 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvcreate_002/2023-08-09-03_23_06.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvcreate_002/2023-08-09-03_23_06.log) | mugen中suite2cases文档中无add disk信息 |
|  | oe_test_lvm2_pvmove_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvmove_001/2023-08-09-03_25_05.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvmove_001/2023-08-09-03_25_05.log) | mugen中suite2cases文档中无add disk信息 |
|  | oe_test_lvm2_pvmove_002 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvmove_002/2023-08-09-03_29_37.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvmove_002/2023-08-09-03_29_37.log) | mugen中suite2cases文档中无add disk信息 |
|  | oe_test_lvm2_pvresize_001 | fail | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvresize_001/2023-08-09-03_23_44.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_lvm2_pvresize_001/2023-08-09-03_23_44.log) | mugen中suite2cases文档中无add disk信息 |
| lxc | oe_test_lxc_autostart_cgroup | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_autostart_cgroup/2023-08-08-03_27_44.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_autostart_cgroup/2023-08-08-03_27_44.log) | 没有对应的软件包 |
|  | oe_test_lxc_create_attach | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_create_attach/2023-08-08-03_25_10.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_create_attach/2023-08-08-03_25_10.log) | 没有对应的软件包 |
|  | oe_test_lxc_device_copy | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_device_copy/2023-08-08-03_28_35.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_device_copy/2023-08-08-03_28_35.log) | 没有对应的软件包 |
|  | oe_test_lxc_info | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_info/2023-08-08-03_33_09.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_info/2023-08-08-03_33_09.log) | 没有对应的软件包 |
|  | oe_test_lxc_ls_monitor | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_ls_monitor/2023-08-08-03_26_01.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_ls_monitor/2023-08-08-03_26_01.log) | 没有对应的软件包 |
|  | oe_test_lxc_snapshot_start | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_snapshot_start/2023-08-08-03_29_24.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_snapshot_start/2023-08-08-03_29_24.log) | 没有对应的软件包 |
|  | oe_test_lxc_stop_top | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_stop_top/2023-08-08-03_32_18.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_stop_top/2023-08-08-03_32_18.log) | 没有对应的软件包 |
|  | oe_test_lxc_unfreeze_destroy | fail | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_unfreeze_destroy/2023-08-08-03_26_51.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_unfreeze_destroy/2023-08-08-03_26_51.log) | 没有对应的软件包 |
| multipath-tools | oe_test_multipath-tools_kpartx | fail | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_kpartx/2023-08-08-12_54_16.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_kpartx/2023-08-08-12_54_16.log) | 测试使用 /dev/sda ，与虚拟机环境不符 |
| os-basic | oe_test_disk_schedule_specific | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_schedule_specific/2023-08-08-19_53_59.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_schedule_specific/2023-08-08-19_53_59.log) | 多磁盘配置问题，重测通过 |
|  | oe_test_kernel_kdump | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_kdump/2023-08-08-20_56_24.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_kdump/2023-08-08-20_56_24.log) | systemd 问题 |
|  | oe_test_libsecret | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_libsecret/2023-08-09-00_15_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_libsecret/2023-08-09-00_15_07.log) | secret-tool 在 03 预装了但是在 09 没有预装 |
|  | oe_test_reboot | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_reboot/2023-08-08-16_44_02.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_reboot/2023-08-08-16_44_02.log) | 无法安装 dmidecode 软件包 |
|  | oe_test_server_mariadb_compatibilty | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_compatibilty/2023-08-08-15_03_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_mariadb_compatibilty/2023-08-08-15_03_30.log) | 软件包 mysql-server 与软件包 mariadb-server 冲突 |
|  | oe_test_server_squid_blacklist | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_blacklist/2023-08-08-13_23_17.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_squid_blacklist/2023-08-08-13_23_17.log) | firewall 通信失败 |
|  | oe_test_xmlsec | fail | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_xmlsec/2023-08-08-23_00_32.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_xmlsec/2023-08-08-23_00_32.log) | 测试程序链接失败 |
| quota | oe_test_service_quota_nld | fail | [./oe-rv2309/mugen-riscv/logs/quota/oe_test_service_quota_nld/2023-08-08-11_20_11.log](./oe-rv2309/mugen-riscv/logs/quota/oe_test_service_quota_nld/2023-08-08-11_20_11.log) | 重新测试通过 |
| rpmdevtools | oe_test_rpmdevtools_rpmargs | fail | [./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmargs/2023-08-09-04_08_00.log](./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmargs/2023-08-09-04_08_00.log) | 软件yumdownloader行为异常，导致下载失败 |
| rsyslog | oe_test_rsyslog_function_relp | fail | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_relp/2023-08-08-00_53_00.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_relp/2023-08-08-00_53_00.log) | 没有对应的软件包 |
| smoke-basic-os | oe_test_glibc_getaddrinfo_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_glibc_getaddrinfo_01/2023-08-08-11_50_54.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_glibc_getaddrinfo_01/2023-08-08-11_50_54.log) | 缺少host命令 |
|  | oe_test_ima_v2_manual_gen_digest_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01/2023-08-08-13_44_23.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01/2023-08-08-13_44_23.log) | 2309上没有digest-list-tools 的依赖 |
|  | oe_test_ip6tables_01 | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip6tables_01/2023-08-08-11_53_15.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip6tables_01/2023-08-08-11_53_15.log) | 没有预装iptables |
|  | oe_test_iptables-save | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iptables-save/2023-08-08-11_55_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iptables-save/2023-08-08-11_55_05.log) | 没有预装iptables |
|  | oe_test_skopeo | fail | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_skopeo/2023-08-08-10_47_43.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_skopeo/2023-08-08-10_47_43.log) | 2309中找不到skopeo软件包 |
| systemd | oe_test_socket_systemd-userdbd | fail | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-userdbd/2023-08-08-01_12_08.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_socket_systemd-userdbd/2023-08-08-01_12_08.log) | 找不到文件对象 |
| vdo | oe_test_service_vdo | fail | [./oe-rv2309/mugen-riscv/logs/vdo/oe_test_service_vdo/2023-08-08-17_27_38.log](./oe-rv2309/mugen-riscv/logs/vdo/oe_test_service_vdo/2023-08-08-17_27_38.log) | 测试超时 |




## BaseOS 在 23.09 上测试通过用例

此表中的测试用例均为在 RISC-V 上测试通过的测试用例

| 测试套/软件包名 | 测试用例名 | 状态 | 日志文件 |
| :---: | :---: | :---: | :---: |
| FS_File | oe_test_FSIO_create_hardlink_fail | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_hardlink_fail/2023-08-09-02_30_53.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_hardlink_fail/2023-08-09-02_30_53.log) |
|  | oe_test_FSIO_modify_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_modify_file/2023-08-09-02_34_38.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_modify_file/2023-08-09-02_34_38.log) |
|  | oe_test_FSIO_sys_fs_check | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_sys_fs_check/2023-08-09-02_38_10.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_sys_fs_check/2023-08-09-02_38_10.log) |
|  | oe_test_FSIO_act_alias | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_alias/2023-08-09-02_13_49.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_alias/2023-08-09-02_13_49.log) |
|  | oe_test_FSIO_act_hardlink | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_hardlink/2023-08-09-02_16_34.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_act_hardlink/2023-08-09-02_16_34.log) |
|  | oe_test_FSIO_bzip2 | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_bzip2/2023-08-09-02_16_52.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_bzip2/2023-08-09-02_16_52.log) |
|  | oe_test_FSIO_change_proc | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_change_proc/2023-08-09-02_17_17.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_change_proc/2023-08-09-02_17_17.log) |
|  | oe_test_FSIO_chattr | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chattr/2023-08-09-02_17_35.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chattr/2023-08-09-02_17_35.log) |
|  | oe_test_FSIO_check_block | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_block/2023-08-09-02_18_07.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_block/2023-08-09-02_18_07.log) |
|  | oe_test_FSIO_check_file_info | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_file_info/2023-08-09-02_18_22.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_check_file_info/2023-08-09-02_18_22.log) |
|  | oe_test_FSIO_chown | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chown/2023-08-09-02_19_09.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_chown/2023-08-09-02_19_09.log) |
|  | oe_test_FSIO_concurrency_read_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_concurrency_read_file/2023-08-09-02_19_29.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_concurrency_read_file/2023-08-09-02_19_29.log) |
|  | oe_test_FSIO_concurrency_write_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_concurrency_write_file/2023-08-09-02_21_43.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_concurrency_write_file/2023-08-09-02_21_43.log) |
|  | oe_test_FSIO_cp_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_cp_file/2023-08-09-02_29_36.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_cp_file/2023-08-09-02_29_36.log) |
|  | oe_test_FSIO_create_block | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_block/2023-08-09-02_30_03.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_block/2023-08-09-02_30_03.log) |
|  | oe_test_FSIO_create_character | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_character/2023-08-09-02_30_28.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_character/2023-08-09-02_30_28.log) |
|  | oe_test_FSIO_create_mv_hardlink | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_mv_hardlink/2023-08-09-02_31_18.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_mv_hardlink/2023-08-09-02_31_18.log) |
|  | oe_test_FSIO_create_softlink | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink/2023-08-09-02_31_43.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink/2023-08-09-02_31_43.log) |
|  | oe_test_FSIO_create_softlink_diff_fs | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_diff_fs/2023-08-09-02_32_01.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_diff_fs/2023-08-09-02_32_01.log) |
|  | oe_test_FSIO_create_softlink_dir | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_dir/2023-08-09-02_32_25.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_dir/2023-08-09-02_32_25.log) |
|  | oe_test_FSIO_create_softlink_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_file/2023-08-09-02_32_40.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_create_softlink_file/2023-08-09-02_32_40.log) |
|  | oe_test_FSIO_dd_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_dd_file/2023-08-09-02_32_58.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_dd_file/2023-08-09-02_32_58.log) |
|  | oe_test_FSIO_gzip | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_gzip/2023-08-09-02_33_49.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_gzip/2023-08-09-02_33_49.log) |
|  | oe_test_FSIO_gzip_overfs | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_gzip_overfs/2023-08-09-02_34_13.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_gzip_overfs/2023-08-09-02_34_13.log) |
|  | oe_test_FSIO_modify_magicfile | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_modify_magicfile/2023-08-09-02_35_13.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_modify_magicfile/2023-08-09-02_35_13.log) |
|  | oe_test_FSIO_mv_file_1 | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_mv_file_1/2023-08-09-02_35_29.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_mv_file_1/2023-08-09-02_35_29.log) |
|  | oe_test_FSIO_mv_file_2 | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_mv_file_2/2023-08-09-02_35_54.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_mv_file_2/2023-08-09-02_35_54.log) |
|  | oe_test_FSIO_rename_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rename_file/2023-08-09-02_36_19.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rename_file/2023-08-09-02_36_19.log) |
|  | oe_test_FSIO_rename_file_fail | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rename_file_fail/2023-08-09-02_36_47.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rename_file_fail/2023-08-09-02_36_47.log) |
|  | oe_test_FSIO_rm_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_file/2023-08-09-02_37_22.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_file/2023-08-09-02_37_22.log) |
|  | oe_test_FSIO_rm_hardlink | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_hardlink/2023-08-09-02_37_38.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_hardlink/2023-08-09-02_37_38.log) |
|  | oe_test_FSIO_rm_softlink | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_softlink/2023-08-09-02_37_54.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_rm_softlink/2023-08-09-02_37_54.log) |
|  | oe_test_FSIO_tar | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_tar/2023-08-09-02_38_29.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_tar/2023-08-09-02_38_29.log) |
|  | oe_test_FSIO_touch_file | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_touch_file/2023-08-09-02_39_08.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_touch_file/2023-08-09-02_39_08.log) |
|  | oe_test_FSIO_zip_unzip | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_zip_unzip/2023-08-09-02_39_25.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_zip_unzip/2023-08-09-02_39_25.log) |
|  | oe_test_FSIO_zip_unzip_overfs | success | [./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_zip_unzip_overfs/2023-08-09-02_39_52.log](./oe-rv2309/mugen-riscv/logs/FS_File/oe_test_FSIO_zip_unzip_overfs/2023-08-09-02_39_52.log) |
| FS_FileSystem | oe_test_FSIO_check_fs_mounted | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_check_fs_mounted/2023-08-11-00_21_41.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_check_fs_mounted/2023-08-11-00_21_41.log) |
|  | oe_test_FSIO_ext3_resize2fs | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext3_resize2fs/2023-08-09-02_19_03.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext3_resize2fs/2023-08-09-02_19_03.log) |
|  | oe_test_FSIO_ext4_e4defrag | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext4_e4defrag/2023-08-09-02_19_27.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext4_e4defrag/2023-08-09-02_19_27.log) |
|  | oe_test_FSIO_ext4_resize2fs | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext4_resize2fs/2023-08-09-02_20_35.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_ext4_resize2fs/2023-08-09-02_20_35.log) |
|  | oe_test_FSIO_mount_ext3_ro | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_ro/2023-08-09-02_22_58.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_ro/2023-08-09-02_22_58.log) |
|  | oe_test_FSIO_mount_ext3_rw | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_rw/2023-08-09-02_23_26.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_rw/2023-08-09-02_23_26.log) |
|  | oe_test_FSIO_mount_ext3_setData | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_setData/2023-08-09-02_23_53.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_setData/2023-08-09-02_23_53.log) |
|  | oe_test_FSIO_mount_ext3_setErrors | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_setErrors/2023-08-09-02_24_21.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_setErrors/2023-08-09-02_24_21.log) |
|  | oe_test_FSIO_mount_ext3_sync | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_sync/2023-08-09-02_24_46.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_sync/2023-08-09-02_24_46.log) |
|  | oe_test_FSIO_mount_ext3_to_ext4 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_to_ext4/2023-08-09-02_26_28.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext3_to_ext4/2023-08-09-02_26_28.log) |
|  | oe_test_FSIO_mount_ext4_block_validity | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_block_validity/2023-08-09-02_26_57.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_block_validity/2023-08-09-02_26_57.log) |
|  | oe_test_FSIO_mount_ext4_delalloc | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_delalloc/2023-08-09-02_27_19.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_delalloc/2023-08-09-02_27_19.log) |
|  | oe_test_FSIO_mount_ext4_discard | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_discard/2023-08-09-02_27_40.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_discard/2023-08-09-02_27_40.log) |
|  | oe_test_FSIO_mount_ext4_noblock_validity | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_noblock_validity/2023-08-09-02_28_04.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_noblock_validity/2023-08-09-02_28_04.log) |
|  | oe_test_FSIO_mount_ext4_nodiscard | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_nodiscard/2023-08-09-02_28_28.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_nodiscard/2023-08-09-02_28_28.log) |
|  | oe_test_FSIO_mount_ext4_ro | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_ro/2023-08-09-02_28_52.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_ro/2023-08-09-02_28_52.log) |
|  | oe_test_FSIO_mount_ext4_rw | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_rw/2023-08-09-02_29_15.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_rw/2023-08-09-02_29_15.log) |
|  | oe_test_FSIO_mount_ext4_setData | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_setData/2023-08-09-02_29_35.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_setData/2023-08-09-02_29_35.log) |
|  | oe_test_FSIO_mount_ext4_setErrors | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_setErrors/2023-08-09-02_29_55.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_setErrors/2023-08-09-02_29_55.log) |
|  | oe_test_FSIO_mount_ext4_sync | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_sync/2023-08-09-02_30_14.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_ext4_sync/2023-08-09-02_30_14.log) |
|  | oe_test_FSIO_mount_umount_ext3 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_ext3/2023-08-09-02_32_28.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_ext3/2023-08-09-02_32_28.log) |
|  | oe_test_FSIO_mount_umount_ext4 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_ext4/2023-08-09-02_32_51.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_umount_ext4/2023-08-09-02_32_51.log) |
|  | oe_test_FSIO_mount_xfs_xfsdump | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_xfsdump/2023-08-09-02_37_20.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_xfsdump/2023-08-09-02_37_20.log) |
|  | oe_test_FSIO_mount_xfs_xfsrestore | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_xfsrestore/2023-08-09-02_38_48.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mount_xfs_xfsrestore/2023-08-09-02_38_48.log) |
|  | oe_test_FSIO_mv_rm_mount | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mv_rm_mount/2023-08-09-02_40_17.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_mv_rm_mount/2023-08-09-02_40_17.log) |
|  | oe_test_FSIO_overlay_modify_1 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_1/2023-08-09-02_40_37.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_1/2023-08-09-02_40_37.log) |
|  | oe_test_FSIO_overlay_modify_2 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_2/2023-08-09-02_40_53.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_2/2023-08-09-02_40_53.log) |
|  | oe_test_FSIO_overlay_modify_3 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_3/2023-08-09-02_41_09.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_modify_3/2023-08-09-02_41_09.log) |
|  | oe_test_FSIO_overlay_mount | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_mount/2023-08-09-02_41_24.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_mount/2023-08-09-02_41_24.log) |
|  | oe_test_FSIO_overlay_rename_1 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_1/2023-08-09-02_41_39.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_1/2023-08-09-02_41_39.log) |
|  | oe_test_FSIO_overlay_rename_2 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_2/2023-08-09-02_41_54.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_2/2023-08-09-02_41_54.log) |
|  | oe_test_FSIO_overlay_rename_3 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_3/2023-08-09-02_42_09.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rename_3/2023-08-09-02_42_09.log) |
|  | oe_test_FSIO_overlay_rm_1 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_1/2023-08-09-02_42_24.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_1/2023-08-09-02_42_24.log) |
|  | oe_test_FSIO_overlay_rm_2 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_2/2023-08-09-02_42_39.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_2/2023-08-09-02_42_39.log) |
|  | oe_test_FSIO_overlay_rm_3 | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_3/2023-08-09-02_42_54.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_overlay_rm_3/2023-08-09-02_42_54.log) |
|  | oe_test_FSIO_rw_mount | success | [./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_rw_mount/2023-08-09-02_43_28.log](./oe-rv2309/mugen-riscv/logs/FS_FileSystem/oe_test_FSIO_rw_mount/2023-08-09-02_43_28.log) |
| NetworkManager | oe_test_libnetfilter_conntrack | success | [./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_libnetfilter_conntrack/2023-08-08-10_21_56.log](./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_libnetfilter_conntrack/2023-08-08-10_21_56.log) |
|  | oe_test_service_NetworkManager | success | [./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager/2023-08-08-10_20_35.log](./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager/2023-08-08-10_20_35.log) |
|  | oe_test_service_NetworkManager-dispatcher | success | [./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager-dispatcher/2023-08-08-10_20_06.log](./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager-dispatcher/2023-08-08-10_20_06.log) |
|  | oe_test_service_NetworkManager-wait-online | success | [./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager-wait-online/2023-08-08-10_21_11.log](./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_NetworkManager-wait-online/2023-08-08-10_21_11.log) |
|  | oe_test_service_nm-cloud-setup | success | [./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_nm-cloud-setup/2023-08-08-10_21_42.log](./oe-rv2309/mugen-riscv/logs/NetworkManager/oe_test_service_nm-cloud-setup/2023-08-08-10_21_42.log) |
| PackageKit | oe_test_service_packagekit | success | [./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_service_packagekit/2023-08-08-10_55_16.log](./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_service_packagekit/2023-08-08-10_55_16.log) |
|  | oe_test_service_packagekit-offline-update | success | [./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_service_packagekit-offline-update/2023-08-08-10_53_17.log](./oe-rv2309/mugen-riscv/logs/PackageKit/oe_test_service_packagekit-offline-update/2023-08-08-10_53_17.log) |
| accountsservice | oe_test_service_accounts-daemon | success | [./oe-rv2309/mugen-riscv/logs/accountsservice/oe_test_service_accounts-daemon/2023-08-09-04_50_12.log](./oe-rv2309/mugen-riscv/logs/accountsservice/oe_test_service_accounts-daemon/2023-08-09-04_50_12.log) |
| acl | oe_test_acl_default_kernel_setting | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_default_kernel_setting/2023-08-08-09_52_51.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_default_kernel_setting/2023-08-08-09_52_51.log) |
|  | oe_test_acl_add_write_permissions | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_add_write_permissions/2023-08-08-09_49_53.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_add_write_permissions/2023-08-08-09_49_53.log) |
|  | oe_test_acl_backup_recovery | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_backup_recovery/2023-08-08-09_53_00.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_backup_recovery/2023-08-08-09_53_00.log) |
|  | oe_test_acl_chacl | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_chacl/2023-08-08-09_51_07.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_chacl/2023-08-08-09_51_07.log) |
|  | oe_test_acl_change_mask | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_change_mask/2023-08-08-09_50_06.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_change_mask/2023-08-08-09_50_06.log) |
|  | oe_test_acl_check_create | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_check_create/2023-08-08-09_50_23.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_check_create/2023-08-08-09_50_23.log) |
|  | oe_test_acl_defaulr_rule | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_defaulr_rule/2023-08-08-09_50_35.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_defaulr_rule/2023-08-08-09_50_35.log) |
|  | oe_test_acl_delete_acl | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_delete_acl/2023-08-08-09_53_11.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_delete_acl/2023-08-08-09_53_11.log) |
|  | oe_test_acl_dir_read | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_dir_read/2023-08-08-09_53_56.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_dir_read/2023-08-08-09_53_56.log) |
|  | oe_test_acl_dir_write | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_dir_write/2023-08-08-09_53_22.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_dir_write/2023-08-08-09_53_22.log) |
|  | oe_test_acl_file | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_file/2023-08-08-09_52_22.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_file/2023-08-08-09_52_22.log) |
|  | oe_test_acl_getfacl | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_getfacl/2023-08-08-09_51_29.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_getfacl/2023-08-08-09_51_29.log) |
|  | oe_test_acl_only_root_permission | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_only_root_permission/2023-08-08-09_50_46.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_only_root_permission/2023-08-08-09_50_46.log) |
|  | oe_test_acl_setfacl | success | [./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_setfacl/2023-08-08-09_51_49.log](./oe-rv2309/mugen-riscv/logs/acl/oe_test_acl_setfacl/2023-08-08-09_51_49.log) |
| acpid | oe_test_acpid_01 | success | [./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_01/2023-08-09-04_04_06.log](./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_01/2023-08-09-04_04_06.log) |
|  | oe_test_acpid_02 | success | [./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_02/2023-08-09-04_05_22.log](./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_02/2023-08-09-04_05_22.log) |
|  | oe_test_acpid_03 | success | [./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_03/2023-08-09-04_06_38.log](./oe-rv2309/mugen-riscv/logs/acpid/oe_test_acpid_03/2023-08-09-04_06_38.log) |
|  | oe_test_service_acpid | success | [./oe-rv2309/mugen-riscv/logs/acpid/oe_test_service_acpid/2023-08-09-04_01_14.log](./oe-rv2309/mugen-riscv/logs/acpid/oe_test_service_acpid/2023-08-09-04_01_14.log) |
|  | oe_test_socket_acpid | success | [./oe-rv2309/mugen-riscv/logs/acpid/oe_test_socket_acpid/2023-08-09-04_02_41.log](./oe-rv2309/mugen-riscv/logs/acpid/oe_test_socket_acpid/2023-08-09-04_02_41.log) |
| amanda | oe_test_amanda_aespipe | success | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_aespipe/2023-08-12-20_50_25.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_amanda_aespipe/2023-08-12-20_50_25.log) |
|  | oe_test_service_amanda-udp | success | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_service_amanda-udp/2023-08-12-20_48_08.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_service_amanda-udp/2023-08-12-20_48_08.log) |
|  | oe_test_socket_amanda | success | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_amanda/2023-08-12-20_49_14.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_amanda/2023-08-12-20_49_14.log) |
|  | oe_test_socket_amanda-udp | success | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_amanda-udp/2023-08-12-20_53_45.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_amanda-udp/2023-08-12-20_53_45.log) |
|  | oe_test_socket_kamanda | success | [./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_kamanda/2023-08-12-20_56_49.log](./oe-rv2309/mugen-riscv/logs/amanda/oe_test_socket_kamanda/2023-08-12-20_56_49.log) |
| anaconda | oe_test_service_anaconda | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda/2023-08-08-03_50_53.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda/2023-08-08-03_50_53.log) |
|  | oe_test_service_anaconda-direct | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-direct/2023-08-08-03_35_33.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-direct/2023-08-08-03_35_33.log) |
|  | oe_test_service_anaconda-fips | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-fips/2023-08-08-03_52_45.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-fips/2023-08-08-03_52_45.log) |
|  | oe_test_service_anaconda-nm-config | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-nm-config/2023-08-08-03_50_00.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-nm-config/2023-08-08-03_50_00.log) |
|  | oe_test_service_anaconda-noshell | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-noshell/2023-08-08-03_50_17.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-noshell/2023-08-08-03_50_17.log) |
|  | oe_test_service_anaconda-pre | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-pre/2023-08-08-03_50_35.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-pre/2023-08-08-03_50_35.log) |
|  | oe_test_service_anaconda-sshd | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-sshd/2023-08-08-03_51_10.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_anaconda-sshd/2023-08-08-03_51_10.log) |
|  | oe_test_service_instperf | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_instperf/2023-08-08-03_51_28.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_service_instperf/2023-08-08-03_51_28.log) |
|  | oe_test_target_anaconda | success | [./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_target_anaconda/2023-08-08-03_52_29.log](./oe-rv2309/mugen-riscv/logs/anaconda/oe_test_target_anaconda/2023-08-08-03_52_29.log) |
| apr | oe_test_apr_001 | success | [./oe-rv2309/mugen-riscv/logs/apr/oe_test_apr_001/2023-08-13-00_37_32.log](./oe-rv2309/mugen-riscv/logs/apr/oe_test_apr_001/2023-08-13-00_37_32.log) |
|  | oe_test_apr_002 | success | [./oe-rv2309/mugen-riscv/logs/apr/oe_test_apr_002/2023-08-13-00_42_01.log](./oe-rv2309/mugen-riscv/logs/apr/oe_test_apr_002/2023-08-13-00_42_01.log) |
| arpwatch | oe_test_service_arpwatch | success | [./oe-rv2309/mugen-riscv/logs/arpwatch/oe_test_service_arpwatch/2023-08-08-16_19_46.log](./oe-rv2309/mugen-riscv/logs/arpwatch/oe_test_service_arpwatch/2023-08-08-16_19_46.log) |
| asciidoc | oe_test_asciidoc_a2x_01 | success | [./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_01/2023-08-08-10_31_50.log](./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_01/2023-08-08-10_31_50.log) |
|  | oe_test_asciidoc_a2x_02 | success | [./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_02/2023-08-08-10_33_27.log](./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_a2x_02/2023-08-08-10_33_27.log) |
|  | oe_test_asciidoc_asciidoc_01 | success | [./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_asciidoc_01/2023-08-08-10_25_42.log](./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_asciidoc_01/2023-08-08-10_25_42.log) |
|  | oe_test_asciidoc_asciidoc_02 | success | [./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_asciidoc_02/2023-08-08-10_31_04.log](./oe-rv2309/mugen-riscv/logs/asciidoc/oe_test_asciidoc_asciidoc_02/2023-08-08-10_31_04.log) |
| attr | oe_test_attr | success | [./oe-rv2309/mugen-riscv/logs/attr/oe_test_attr/2023-08-08-11_27_01.log](./oe-rv2309/mugen-riscv/logs/attr/oe_test_attr/2023-08-08-11_27_01.log) |
| audit | oe_test_audit_monitor_security_event | success | [./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_security_event/2023-08-09-02_57_24.log](./oe-rv2309/mugen-riscv/logs/audit/oe_test_audit_monitor_security_event/2023-08-09-02_57_24.log) |
| authd | oe_test_socket_auth | success | [./oe-rv2309/mugen-riscv/logs/authd/oe_test_socket_auth/2023-08-13-02_39_24.log](./oe-rv2309/mugen-riscv/logs/authd/oe_test_socket_auth/2023-08-13-02_39_24.log) |
| authz | oe_test_service_authz | success | [./oe-rv2309/mugen-riscv/logs/authz/oe_test_service_authz/2023-08-09-04_51_25.log](./oe-rv2309/mugen-riscv/logs/authz/oe_test_service_authz/2023-08-09-04_51_25.log) |
| automake | oe_test_automake | success | [./oe-rv2309/mugen-riscv/logs/automake/oe_test_automake/2023-08-09-04_49_01.log](./oe-rv2309/mugen-riscv/logs/automake/oe_test_automake/2023-08-09-04_49_01.log) |
| avahi | oe_test_service_avahi-daemon | success | [./oe-rv2309/mugen-riscv/logs/avahi/oe_test_service_avahi-daemon/2023-08-08-14_42_11.log](./oe-rv2309/mugen-riscv/logs/avahi/oe_test_service_avahi-daemon/2023-08-08-14_42_11.log) |
|  | oe_test_service_avahi-dnsconfd | success | [./oe-rv2309/mugen-riscv/logs/avahi/oe_test_service_avahi-dnsconfd/2023-08-08-14_50_10.log](./oe-rv2309/mugen-riscv/logs/avahi/oe_test_service_avahi-dnsconfd/2023-08-08-14_50_10.log) |
|  | oe_test_socket_avahi-daemon | success | [./oe-rv2309/mugen-riscv/logs/avahi/oe_test_socket_avahi-daemon/2023-08-08-14_57_59.log](./oe-rv2309/mugen-riscv/logs/avahi/oe_test_socket_avahi-daemon/2023-08-08-14_57_59.log) |
| babeltrace | oe_test_babeltrace | success | [./oe-rv2309/mugen-riscv/logs/babeltrace/oe_test_babeltrace/2023-08-08-11_27_31.log](./oe-rv2309/mugen-riscv/logs/babeltrace/oe_test_babeltrace/2023-08-08-11_27_31.log) |
| bind | oe_test_service_named-setup-rndc | success | [./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-setup-rndc/2023-08-09-04_12_19.log](./oe-rv2309/mugen-riscv/logs/bind/oe_test_service_named-setup-rndc/2023-08-09-04_12_19.log) |
| binutils | oe_test_binutils_addr2line | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_addr2line/2023-08-08-10_02_09.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_addr2line/2023-08-08-10_02_09.log) |
|  | oe_test_binutils_ar | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_ar/2023-08-08-10_02_29.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_ar/2023-08-08-10_02_29.log) |
|  | oe_test_binutils_nm | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_nm/2023-08-08-10_02_49.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_nm/2023-08-08-10_02_49.log) |
|  | oe_test_binutils_objcopy | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_objcopy/2023-08-08-10_01_04.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_objcopy/2023-08-08-10_01_04.log) |
|  | oe_test_binutils_objdump | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_objdump/2023-08-08-10_03_09.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_objdump/2023-08-08-10_03_09.log) |
|  | oe_test_binutils_readelf | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_readelf/2023-08-08-10_03_29.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_readelf/2023-08-08-10_03_29.log) |
|  | oe_test_binutils_size | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_size/2023-08-08-10_01_29.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_size/2023-08-08-10_01_29.log) |
|  | oe_test_binutils_strip | success | [./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_strip/2023-08-08-10_01_50.log](./oe-rv2309/mugen-riscv/logs/binutils/oe_test_binutils_strip/2023-08-08-10_01_50.log) |
| bison | oe_test_bison | success | [./oe-rv2309/mugen-riscv/logs/bison/oe_test_bison/2023-08-08-17_29_10.log](./oe-rv2309/mugen-riscv/logs/bison/oe_test_bison/2023-08-08-17_29_10.log) |
| bluez | oe_test_service_bluetooth | success | [./oe-rv2309/mugen-riscv/logs/bluez/oe_test_service_bluetooth/2023-08-09-04_34_26.log](./oe-rv2309/mugen-riscv/logs/bluez/oe_test_service_bluetooth/2023-08-09-04_34_26.log) |
|  | oe_test_service_bluetooth-mesh | success | [./oe-rv2309/mugen-riscv/logs/bluez/oe_test_service_bluetooth-mesh/2023-08-09-04_34_13.log](./oe-rv2309/mugen-riscv/logs/bluez/oe_test_service_bluetooth-mesh/2023-08-09-04_34_13.log) |
| bolt | oe_test_service_bolt | success | [./oe-rv2309/mugen-riscv/logs/bolt/oe_test_service_bolt/2023-08-08-16_20_50.log](./oe-rv2309/mugen-riscv/logs/bolt/oe_test_service_bolt/2023-08-08-16_20_50.log) |
| brltty | oe_test_service_brltty | success | [./oe-rv2309/mugen-riscv/logs/brltty/oe_test_service_brltty/2023-08-08-11_28_38.log](./oe-rv2309/mugen-riscv/logs/brltty/oe_test_service_brltty/2023-08-08-11_28_38.log) |
| byacc | oe_test_byacc_byacc_01 | success | [./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_byacc_01/2023-08-08-10_42_53.log](./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_byacc_01/2023-08-08-10_42_53.log) |
|  | oe_test_byacc_byacc_02 | success | [./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_byacc_02/2023-08-08-10_43_47.log](./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_byacc_02/2023-08-08-10_43_47.log) |
|  | oe_test_byacc_yacc_01 | success | [./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_yacc_01/2023-08-08-10_44_35.log](./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_yacc_01/2023-08-08-10_44_35.log) |
|  | oe_test_byacc_yacc_02 | success | [./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_yacc_02/2023-08-08-10_45_24.log](./oe-rv2309/mugen-riscv/logs/byacc/oe_test_byacc_yacc_02/2023-08-08-10_45_24.log) |
| bzip2 | oe_test_bzip2 | success | [./oe-rv2309/mugen-riscv/logs/bzip2/oe_test_bzip2/2023-08-13-02_46_31.log](./oe-rv2309/mugen-riscv/logs/bzip2/oe_test_bzip2/2023-08-13-02_46_31.log) |
| cgdcbxd | oe_test_service_cgdcbxd | success | [./oe-rv2309/mugen-riscv/logs/cgdcbxd/oe_test_service_cgdcbxd/2023-08-08-11_29_33.log](./oe-rv2309/mugen-riscv/logs/cgdcbxd/oe_test_service_cgdcbxd/2023-08-08-11_29_33.log) |
| clevis | oe_test_service_clevis-luks-askpass | success | [./oe-rv2309/mugen-riscv/logs/clevis/oe_test_service_clevis-luks-askpass/2023-08-08-13_14_35.log](./oe-rv2309/mugen-riscv/logs/clevis/oe_test_service_clevis-luks-askpass/2023-08-08-13_14_35.log) |
|  | oe_test_tang_encrypt | success | [./oe-rv2309/mugen-riscv/logs/clevis/oe_test_tang_encrypt/2023-08-08-13_40_05.log](./oe-rv2309/mugen-riscv/logs/clevis/oe_test_tang_encrypt/2023-08-08-13_40_05.log) |
| cloud-init | oe_test_service_cloud-config | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-config/2023-08-09-03_50_59.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-config/2023-08-09-03_50_59.log) |
|  | oe_test_service_cloud-final | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-final/2023-08-09-03_54_58.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-final/2023-08-09-03_54_58.log) |
|  | oe_test_service_cloud-init | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-init/2023-08-09-03_57_10.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-init/2023-08-09-03_57_10.log) |
|  | oe_test_service_cloud-init-local | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-init-local/2023-08-09-03_56_04.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_service_cloud-init-local/2023-08-09-03_56_04.log) |
|  | oe_test_target_cloud-config | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_target_cloud-config/2023-08-09-03_58_20.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_target_cloud-config/2023-08-09-03_58_20.log) |
|  | oe_test_target_cloud-init | success | [./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_target_cloud-init/2023-08-09-03_58_46.log](./oe-rv2309/mugen-riscv/logs/cloud-init/oe_test_target_cloud-init/2023-08-09-03_58_46.log) |
| cmake | oe_test_cmake | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cmake/2023-08-09-03_48_34.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cmake/2023-08-09-03_48_34.log) |
|  | oe_test_cmake3 | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cmake3/2023-08-09-03_51_22.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cmake3/2023-08-09-03_51_22.log) |
|  | oe_test_cpack | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cpack/2023-08-09-03_54_11.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cpack/2023-08-09-03_54_11.log) |
|  | oe_test_cpack3 | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cpack3/2023-08-09-03_56_25.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_cpack3/2023-08-09-03_56_25.log) |
|  | oe_test_ctest | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ctest/2023-08-09-03_58_37.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ctest/2023-08-09-03_58_37.log) |
|  | oe_test_ctest3 | success | [./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ctest3/2023-08-09-04_00_28.log](./oe-rv2309/mugen-riscv/logs/cmake/oe_test_ctest3/2023-08-09-04_00_28.log) |
| cockpit | oe_test_service_cockpit-motd | success | [./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit-motd/2023-08-08-10_36_27.log](./oe-rv2309/mugen-riscv/logs/cockpit/oe_test_service_cockpit-motd/2023-08-08-10_36_27.log) |
| colord | oe_test_service_colord | success | [./oe-rv2309/mugen-riscv/logs/colord/oe_test_service_colord/2023-08-08-11_31_30.log](./oe-rv2309/mugen-riscv/logs/colord/oe_test_service_colord/2023-08-08-11_31_30.log) |
| cpio | oe_test_cpio | success | [./oe-rv2309/mugen-riscv/logs/cpio/oe_test_cpio/2023-08-13-02_51_31.log](./oe-rv2309/mugen-riscv/logs/cpio/oe_test_cpio/2023-08-13-02_51_31.log) |
|  | oe_test_cpio | success | [./oe-rv2309/mugen-riscv/logs/cpio/oe_test_cpio/2023-08-13-02_51_31.log](./oe-rv2309/mugen-riscv/logs/cpio/oe_test_cpio/2023-08-13-02_51_31.log) |
| cracklib | oe_test_cracklib | success | [./oe-rv2309/mugen-riscv/logs/cracklib/oe_test_cracklib/2023-08-09-04_53_39.log](./oe-rv2309/mugen-riscv/logs/cracklib/oe_test_cracklib/2023-08-09-04_53_39.log) |
| createrepo_c | oe_test_createrepo_c_local_repo | success | [./oe-rv2309/mugen-riscv/logs/createrepo_c/oe_test_createrepo_c_local_repo/2023-08-08-12_38_05.log](./oe-rv2309/mugen-riscv/logs/createrepo_c/oe_test_createrepo_c_local_repo/2023-08-08-12_38_05.log) |
| cronie | oe_test_cronie | success | [./oe-rv2309/mugen-riscv/logs/cronie/oe_test_cronie/2023-08-09-04_36_28.log](./oe-rv2309/mugen-riscv/logs/cronie/oe_test_cronie/2023-08-09-04_36_28.log) |
|  | oe_test_service_crond | success | [./oe-rv2309/mugen-riscv/logs/cronie/oe_test_service_crond/2023-08-09-04_35_43.log](./oe-rv2309/mugen-riscv/logs/cronie/oe_test_service_crond/2023-08-09-04_35_43.log) |
| ctags | oe_test_ctags | success | [./oe-rv2309/mugen-riscv/logs/ctags/oe_test_ctags/2023-08-13-05_08_02.log](./oe-rv2309/mugen-riscv/logs/ctags/oe_test_ctags/2023-08-13-05_08_02.log) |
|  | oe_test_ctags | success | [./oe-rv2309/mugen-riscv/logs/ctags/oe_test_ctags/2023-08-13-05_08_02.log](./oe-rv2309/mugen-riscv/logs/ctags/oe_test_ctags/2023-08-13-05_08_02.log) |
| cups | oe_test_service_cups | success | [./oe-rv2309/mugen-riscv/logs/cups/oe_test_service_cups/2023-08-12-23_06_19.log](./oe-rv2309/mugen-riscv/logs/cups/oe_test_service_cups/2023-08-12-23_06_19.log) |
|  | oe_test_socket_cups | success | [./oe-rv2309/mugen-riscv/logs/cups/oe_test_socket_cups/2023-08-12-23_17_01.log](./oe-rv2309/mugen-riscv/logs/cups/oe_test_socket_cups/2023-08-12-23_17_01.log) |
|  | oe_test_socket_cups-lpd | success | [./oe-rv2309/mugen-riscv/logs/cups/oe_test_socket_cups-lpd/2023-08-12-23_14_43.log](./oe-rv2309/mugen-riscv/logs/cups/oe_test_socket_cups-lpd/2023-08-12-23_14_43.log) |
| cups-filters | oe_test_service_cups-browsed | success | [./oe-rv2309/mugen-riscv/logs/cups-filters/oe_test_service_cups-browsed/2023-08-08-16_27_12.log](./oe-rv2309/mugen-riscv/logs/cups-filters/oe_test_service_cups-browsed/2023-08-08-16_27_12.log) |
| cvs | oe_test_cvs_cvs_02 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_02/2023-08-08-12_34_02.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_02/2023-08-08-12_34_02.log) |
|  | oe_test_cvs_cvs_03 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_03/2023-08-08-12_39_36.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_03/2023-08-08-12_39_36.log) |
|  | oe_test_cvs_cvs_04 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_04/2023-08-08-12_45_26.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_04/2023-08-08-12_45_26.log) |
|  | oe_test_cvs_cvs_05 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_05/2023-08-08-12_51_29.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_05/2023-08-08-12_51_29.log) |
|  | oe_test_cvs_cvs_06 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_06/2023-08-08-12_57_03.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_06/2023-08-08-12_57_03.log) |
|  | oe_test_cvs_cvs_01 | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_01/2023-08-08-12_28_47.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_cvs_cvs_01/2023-08-08-12_28_47.log) |
|  | oe_test_socket_cvs | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_socket_cvs/2023-08-08-12_17_50.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_socket_cvs/2023-08-08-12_17_50.log) |
|  | oe_test_target_cvs | success | [./oe-rv2309/mugen-riscv/logs/cvs/oe_test_target_cvs/2023-08-08-12_23_14.log](./oe-rv2309/mugen-riscv/logs/cvs/oe_test_target_cvs/2023-08-08-12_23_14.log) |
| cyrus-sasl | oe_test_service_saslauthd | success | [./oe-rv2309/mugen-riscv/logs/cyrus-sasl/oe_test_service_saslauthd/2023-08-08-11_34_05.log](./oe-rv2309/mugen-riscv/logs/cyrus-sasl/oe_test_service_saslauthd/2023-08-08-11_34_05.log) |
| dbus | oe_test_service_dbus | success | [./oe-rv2309/mugen-riscv/logs/dbus/oe_test_service_dbus/2023-08-08-11_13_47.log](./oe-rv2309/mugen-riscv/logs/dbus/oe_test_service_dbus/2023-08-08-11_13_47.log) |
| dhcp | oe_test_allocate_ipv4_addresses_dhcpd | success | [./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_allocate_ipv4_addresses_dhcpd/2023-08-08-10_22_18.log](./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_allocate_ipv4_addresses_dhcpd/2023-08-08-10_22_18.log) |
|  | oe_test_allocate_ipv6_addresses_dhcpd | success | [./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_allocate_ipv6_addresses_dhcpd/2023-08-08-10_23_20.log](./oe-rv2309/mugen-riscv/logs/dhcp/oe_test_allocate_ipv6_addresses_dhcpd/2023-08-08-10_23_20.log) |
| djvulibre | oe_test_djvulibre_01 | success | [./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_01/2023-08-12-21_48_51.log](./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_01/2023-08-12-21_48_51.log) |
|  | oe_test_djvulibre_02 | success | [./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_02/2023-08-12-21_59_23.log](./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_02/2023-08-12-21_59_23.log) |
|  | oe_test_djvulibre_03 | success | [./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_03/2023-08-12-22_00_07.log](./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_03/2023-08-12-22_00_07.log) |
|  | oe_test_djvulibre_04 | success | [./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_04/2023-08-12-22_01_04.log](./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_04/2023-08-12-22_01_04.log) |
|  | oe_test_djvulibre_05 | success | [./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_05/2023-08-12-22_01_59.log](./oe-rv2309/mugen-riscv/logs/djvulibre/oe_test_djvulibre_05/2023-08-12-22_01_59.log) |
| dkms | oe_test_service_dkms | success | [./oe-rv2309/mugen-riscv/logs/dkms/oe_test_service_dkms/2023-08-09-04_54_51.log](./oe-rv2309/mugen-riscv/logs/dkms/oe_test_service_dkms/2023-08-09-04_54_51.log) |
| dnf | oe_test_dnf_alias | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_alias/2023-08-08-10_53_36.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_alias/2023-08-08-10_53_36.log) |
|  | oe_test_dnf_assumeno_autoremove | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_assumeno_autoremove/2023-08-08-10_57_51.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_assumeno_autoremove/2023-08-08-10_57_51.log) |
|  | oe_test_dnf_automatic | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_automatic/2023-08-08-11_05_54.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_automatic/2023-08-08-11_05_54.log) |
|  | oe_test_dnf_distro-sync_dnf-3 | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_distro-sync_dnf-3/2023-08-08-11_24_58.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_distro-sync_dnf-3/2023-08-08-11_24_58.log) |
|  | oe_test_dnf_help_history_info | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_help_history_info/2023-08-08-11_54_10.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_dnf_help_history_info/2023-08-08-11_54_10.log) |
|  | oe_test_service_dnf-automatic | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic/2023-08-08-11_45_54.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic/2023-08-08-11_45_54.log) |
|  | oe_test_service_dnf-automatic-download | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-download/2023-08-08-11_36_48.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-download/2023-08-08-11_36_48.log) |
|  | oe_test_service_dnf-automatic-install | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-install/2023-08-08-11_39_50.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-install/2023-08-08-11_39_50.log) |
|  | oe_test_service_dnf-automatic-notifyonly | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-notifyonly/2023-08-08-11_42_52.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-automatic-notifyonly/2023-08-08-11_42_52.log) |
|  | oe_test_service_dnf-makecache | success | [./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-makecache/2023-08-08-11_48_59.log](./oe-rv2309/mugen-riscv/logs/dnf/oe_test_service_dnf-makecache/2023-08-08-11_48_59.log) |
| dnsmasq | oe_test_service_dnsmasq | success | [./oe-rv2309/mugen-riscv/logs/dnsmasq/oe_test_service_dnsmasq/2023-08-08-16_29_34.log](./oe-rv2309/mugen-riscv/logs/dnsmasq/oe_test_service_dnsmasq/2023-08-08-16_29_34.log) |
| dos2unix | oe_test_dos2unix | success | [./oe-rv2309/mugen-riscv/logs/dos2unix/oe_test_dos2unix/2023-08-08-11_36_40.log](./oe-rv2309/mugen-riscv/logs/dos2unix/oe_test_dos2unix/2023-08-08-11_36_40.log) |
| dracut | oe_test_service_dracut-shutdown | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-shutdown/2023-08-08-10_10_47.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-shutdown/2023-08-08-10_10_47.log) |
|  | oe_test_service_dracut-cmdline | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-cmdline/2023-08-08-10_09_36.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-cmdline/2023-08-08-10_09_36.log) |
|  | oe_test_service_dracut-initqueue | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-initqueue/2023-08-08-10_09_46.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-initqueue/2023-08-08-10_09_46.log) |
|  | oe_test_service_dracut-mount | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-mount/2023-08-08-10_09_57.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-mount/2023-08-08-10_09_57.log) |
|  | oe_test_service_dracut-pre-mount | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-mount/2023-08-08-10_10_07.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-mount/2023-08-08-10_10_07.log) |
|  | oe_test_service_dracut-pre-pivot | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-pivot/2023-08-08-10_10_17.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-pivot/2023-08-08-10_10_17.log) |
|  | oe_test_service_dracut-pre-trigger | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-trigger/2023-08-08-10_10_27.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-trigger/2023-08-08-10_10_27.log) |
|  | oe_test_service_dracut-pre-udev | success | [./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-udev/2023-08-08-10_10_37.log](./oe-rv2309/mugen-riscv/logs/dracut/oe_test_service_dracut-pre-udev/2023-08-08-10_10_37.log) |
| e2fsprogs | oe_test_service_e2scrub_all | success | [./oe-rv2309/mugen-riscv/logs/e2fsprogs/oe_test_service_e2scrub_all/2023-08-08-15_51_04.log](./oe-rv2309/mugen-riscv/logs/e2fsprogs/oe_test_service_e2scrub_all/2023-08-08-15_51_04.log) |
|  | oe_test_service_e2scrub_reap | success | [./oe-rv2309/mugen-riscv/logs/e2fsprogs/oe_test_service_e2scrub_reap/2023-08-08-15_52_05.log](./oe-rv2309/mugen-riscv/logs/e2fsprogs/oe_test_service_e2scrub_reap/2023-08-08-15_52_05.log) |
| fakeroot | oe_test_fakeroot_base | success | [./oe-rv2309/mugen-riscv/logs/fakeroot/oe_test_fakeroot_base/2023-08-08-11_01_20.log](./oe-rv2309/mugen-riscv/logs/fakeroot/oe_test_fakeroot_base/2023-08-08-11_01_20.log) |
|  | oe_test_fakeroot_faked | success | [./oe-rv2309/mugen-riscv/logs/fakeroot/oe_test_fakeroot_faked/2023-08-08-11_02_05.log](./oe-rv2309/mugen-riscv/logs/fakeroot/oe_test_fakeroot_faked/2023-08-08-11_02_05.log) |
| fcoe-utils | oe_test_service_fcoe | success | [./oe-rv2309/mugen-riscv/logs/fcoe-utils/oe_test_service_fcoe/2023-08-09-04_56_41.log](./oe-rv2309/mugen-riscv/logs/fcoe-utils/oe_test_service_fcoe/2023-08-09-04_56_41.log) |
| fftw | oe_test_fftw_fftw-wisdom_01 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_01/2023-08-09-03_39_28.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_01/2023-08-09-03_39_28.log) |
|  | oe_test_fftw_fftw-wisdom_03 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_03/2023-08-09-03_41_40.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftw-wisdom_03/2023-08-09-03_41_40.log) |
|  | oe_test_fftw_fftwf-wisdom_01 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_01/2023-08-09-03_42_40.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_01/2023-08-09-03_42_40.log) |
|  | oe_test_fftw_fftwf-wisdom_03 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_03/2023-08-09-03_44_50.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwf-wisdom_03/2023-08-09-03_44_50.log) |
|  | oe_test_fftw_fftwl-wisdom_01 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_01/2023-08-09-03_45_53.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_01/2023-08-09-03_45_53.log) |
|  | oe_test_fftw_fftwl-wisdom_03 | success | [./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_03/2023-08-09-03_48_02.log](./oe-rv2309/mugen-riscv/logs/fftw/oe_test_fftw_fftwl-wisdom_03/2023-08-09-03_48_02.log) |
| firewalld | oe_test_firewalld_block_all_icmp | success | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_block_all_icmp/2023-08-09-02_14_27.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_block_all_icmp/2023-08-09-02_14_27.log) |
|  | oe_test_firewalld_default_rules | success | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_default_rules/2023-08-09-02_23_15.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_default_rules/2023-08-09-02_23_15.log) |
|  | oe_test_firewalld_manage_icmp | success | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_manage_icmp/2023-08-09-02_17_40.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_manage_icmp/2023-08-09-02_17_40.log) |
|  | oe_test_firewalld_permanent_rules_in_effect | success | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_permanent_rules_in_effect/2023-08-11-11_47_58.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_permanent_rules_in_effect/2023-08-11-11_47_58.log) |
|  | oe_test_firewalld_set_priority | success | [./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_priority/2023-08-09-02_18_37.log](./oe-rv2309/mugen-riscv/logs/firewalld/oe_test_firewalld_set_priority/2023-08-09-02_18_37.log) |
| flac | oe_test_flac | success | [./oe-rv2309/mugen-riscv/logs/flac/oe_test_flac/2023-08-08-11_36_18.log](./oe-rv2309/mugen-riscv/logs/flac/oe_test_flac/2023-08-08-11_36_18.log) |
| flatpak | oe_test_service_flatpak-system-helper | success | [./oe-rv2309/mugen-riscv/logs/flatpak/oe_test_service_flatpak-system-helper/2023-08-08-16_35_35.log](./oe-rv2309/mugen-riscv/logs/flatpak/oe_test_service_flatpak-system-helper/2023-08-08-16_35_35.log) |
| fprintd | oe_test_service_fprintd | success | [./oe-rv2309/mugen-riscv/logs/fprintd/oe_test_service_fprintd/2023-08-08-11_38_50.log](./oe-rv2309/mugen-riscv/logs/fprintd/oe_test_service_fprintd/2023-08-08-11_38_50.log) |
| freeradius | oe_test_freeradius_freeradius-utils_radclient | success | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radclient/2023-08-12-18_17_02.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radclient/2023-08-12-18_17_02.log) |
|  | oe_test_freeradius_freeradius-utils_radeapclient | success | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radeapclient/2023-08-12-18_38_04.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radeapclient/2023-08-12-18_38_04.log) |
|  | oe_test_freeradius_freeradius_radiusd | success | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_radiusd/2023-08-12-17_44_53.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_radiusd/2023-08-12-17_44_53.log) |
|  | oe_test_freeradius_freeradius_radiusdAndRadmin | success | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_radiusdAndRadmin/2023-08-12-17_52_56.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius_radiusdAndRadmin/2023-08-12-17_52_56.log) |
|  | oe_test_freeradius_freeradius-utils_radcryptAndRadeapclient | success | [./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radcryptAndRadeapclient/2023-08-12-18_35_57.log](./oe-rv2309/mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radcryptAndRadeapclient/2023-08-12-18_35_57.log) |
| fribidi | oe_test_fribidi | success | [./oe-rv2309/mugen-riscv/logs/fribidi/oe_test_fribidi/2023-08-13-03_07_51.log](./oe-rv2309/mugen-riscv/logs/fribidi/oe_test_fribidi/2023-08-13-03_07_51.log) |
| gdb | oe_test_gdb_recovery_database | success | [./oe-rv2309/mugen-riscv/logs/gdb/oe_test_gdb_recovery_database/2023-08-09-04_58_42.log](./oe-rv2309/mugen-riscv/logs/gdb/oe_test_gdb_recovery_database/2023-08-09-04_58_42.log) |
| gdbm | oe_test_gdbm_01 | success | [./oe-rv2309/mugen-riscv/logs/gdbm/oe_test_gdbm_01/2023-08-13-02_36_15.log](./oe-rv2309/mugen-riscv/logs/gdbm/oe_test_gdbm_01/2023-08-13-02_36_15.log) |
|  | oe_test_gdbm_02 | success | [./oe-rv2309/mugen-riscv/logs/gdbm/oe_test_gdbm_02/2023-08-13-02_36_42.log](./oe-rv2309/mugen-riscv/logs/gdbm/oe_test_gdbm_02/2023-08-13-02_36_42.log) |
| geoclue2 | oe_test_service_geoclue | success | [./oe-rv2309/mugen-riscv/logs/geoclue2/oe_test_service_geoclue/2023-08-08-16_37_51.log](./oe-rv2309/mugen-riscv/logs/geoclue2/oe_test_service_geoclue/2023-08-08-16_37_51.log) |
| ghostscript | oe_test_ghostscript_001 | success | [./oe-rv2309/mugen-riscv/logs/ghostscript/oe_test_ghostscript_001/2023-08-13-00_50_08.log](./oe-rv2309/mugen-riscv/logs/ghostscript/oe_test_ghostscript_001/2023-08-13-00_50_08.log) |
|  | oe_test_ghostscript_002 | success | [./oe-rv2309/mugen-riscv/logs/ghostscript/oe_test_ghostscript_002/2023-08-13-01_01_43.log](./oe-rv2309/mugen-riscv/logs/ghostscript/oe_test_ghostscript_002/2023-08-13-01_01_43.log) |
| git | oe_test_socket_git | success | [./oe-rv2309/mugen-riscv/logs/git/oe_test_socket_git/2023-08-08-11_41_41.log](./oe-rv2309/mugen-riscv/logs/git/oe_test_socket_git/2023-08-08-11_41_41.log) |
| glibc | oe_test_service_nscd | success | [./oe-rv2309/mugen-riscv/logs/glibc/oe_test_service_nscd/2023-08-09-04_36_39.log](./oe-rv2309/mugen-riscv/logs/glibc/oe_test_service_nscd/2023-08-09-04_36_39.log) |
|  | oe_test_socket_nscd | success | [./oe-rv2309/mugen-riscv/logs/glibc/oe_test_socket_nscd/2023-08-09-04_38_30.log](./oe-rv2309/mugen-riscv/logs/glibc/oe_test_socket_nscd/2023-08-09-04_38_30.log) |
| glusterfs | oe_test_service_gluster-ta-volume | success | [./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_gluster-ta-volume/2023-08-08-10_30_18.log](./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_gluster-ta-volume/2023-08-08-10_30_18.log) |
|  | oe_test_service_glusterd | success | [./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterd/2023-08-08-10_24_27.log](./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterd/2023-08-08-10_24_27.log) |
|  | oe_test_service_glustereventsd | success | [./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glustereventsd/2023-08-08-10_26_04.log](./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glustereventsd/2023-08-08-10_26_04.log) |
|  | oe_test_service_glusterfsd | success | [./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterfsd/2023-08-08-10_28_00.log](./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterfsd/2023-08-08-10_28_00.log) |
|  | oe_test_service_glusterfssharedstorage | success | [./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterfssharedstorage/2023-08-08-10_28_55.log](./oe-rv2309/mugen-riscv/logs/glusterfs/oe_test_service_glusterfssharedstorage/2023-08-08-10_28_55.log) |
| gpm | oe_test_service_gpm | success | [./oe-rv2309/mugen-riscv/logs/gpm/oe_test_service_gpm/2023-08-08-11_38_41.log](./oe-rv2309/mugen-riscv/logs/gpm/oe_test_service_gpm/2023-08-08-11_38_41.log) |
| graphviz | oe_test_graphviz | success | [./oe-rv2309/mugen-riscv/logs/graphviz/oe_test_graphviz/2023-08-09-05_17_07.log](./oe-rv2309/mugen-riscv/logs/graphviz/oe_test_graphviz/2023-08-09-05_17_07.log) |
| grub2 | oe_test_service_grub-boot-indeterminate | success | [./oe-rv2309/mugen-riscv/logs/grub2/oe_test_service_grub-boot-indeterminate/2023-08-08-16_16_32.log](./oe-rv2309/mugen-riscv/logs/grub2/oe_test_service_grub-boot-indeterminate/2023-08-08-16_16_32.log) |
|  | oe_test_service_grub2-systemd-integration | success | [./oe-rv2309/mugen-riscv/logs/grub2/oe_test_service_grub2-systemd-integration/2023-08-08-16_17_24.log](./oe-rv2309/mugen-riscv/logs/grub2/oe_test_service_grub2-systemd-integration/2023-08-08-16_17_24.log) |
| gsl | oe_test_gsl_randlist_01 | success | [./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_01/2023-08-09-04_05_21.log](./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_01/2023-08-09-04_05_21.log) |
|  | oe_test_gsl_randlist_02 | success | [./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_02/2023-08-09-04_06_19.log](./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_02/2023-08-09-04_06_19.log) |
|  | oe_test_gsl_randlist_03 | success | [./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_03/2023-08-09-04_07_15.log](./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_03/2023-08-09-04_07_15.log) |
|  | oe_test_gsl_randlist_04 | success | [./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_04/2023-08-09-04_08_14.log](./oe-rv2309/mugen-riscv/logs/gsl/oe_test_gsl_randlist_04/2023-08-09-04_08_14.log) |
| gssproxy | oe_test_service_gssproxy | success | [./oe-rv2309/mugen-riscv/logs/gssproxy/oe_test_service_gssproxy/2023-08-08-16_43_47.log](./oe-rv2309/mugen-riscv/logs/gssproxy/oe_test_service_gssproxy/2023-08-08-16_43_47.log) |
| haveged | oe_test_service_haveged | success | [./oe-rv2309/mugen-riscv/logs/haveged/oe_test_service_haveged/2023-08-08-11_44_26.log](./oe-rv2309/mugen-riscv/logs/haveged/oe_test_service_haveged/2023-08-08-11_44_26.log) |
| hdf5 | oe_test_hdf5_01 | success | [./oe-rv2309/mugen-riscv/logs/hdf5/oe_test_hdf5_01/2023-08-09-05_18_03.log](./oe-rv2309/mugen-riscv/logs/hdf5/oe_test_hdf5_01/2023-08-09-05_18_03.log) |
| httpd | oe_test_service_htcacheclean | success | [./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_htcacheclean/2023-08-08-10_26_47.log](./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_htcacheclean/2023-08-08-10_26_47.log) |
|  | oe_test_service_httpd | success | [./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_httpd/2023-08-08-10_28_56.log](./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_httpd/2023-08-08-10_28_56.log) |
|  | oe_test_service_httpd-init | success | [./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_httpd-init/2023-08-08-10_28_02.log](./oe-rv2309/mugen-riscv/logs/httpd/oe_test_service_httpd-init/2023-08-08-10_28_02.log) |
|  | oe_test_socket_httpd | success | [./oe-rv2309/mugen-riscv/logs/httpd/oe_test_socket_httpd/2023-08-08-10_30_20.log](./oe-rv2309/mugen-riscv/logs/httpd/oe_test_socket_httpd/2023-08-08-10_30_20.log) |
| initial-setup | oe_test_service_initial-setup-reconfiguration | success | [./oe-rv2309/mugen-riscv/logs/initial-setup/oe_test_service_initial-setup-reconfiguration/2023-08-08-11_41_35.log](./oe-rv2309/mugen-riscv/logs/initial-setup/oe_test_service_initial-setup-reconfiguration/2023-08-08-11_41_35.log) |
| initscripts | oe_test_service_loadmodules | success | [./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_loadmodules/2023-08-09-10_02_20.log](./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_loadmodules/2023-08-09-10_02_20.log) |
|  | oe_test_service_network | success | [./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_network/2023-08-09-10_03_03.log](./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_network/2023-08-09-10_03_03.log) |
|  | oe_test_service_readonly-root | success | [./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_readonly-root/2023-08-09-10_06_31.log](./oe-rv2309/mugen-riscv/logs/initscripts/oe_test_service_readonly-root/2023-08-09-10_06_31.log) |
| intltool | oe_test_intltool_intltool-extract | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-extract/2023-08-08-13_13_45.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-extract/2023-08-08-13_13_45.log) |
|  | oe_test_intltool_intltool-merge_01 | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-merge_01/2023-08-08-12_54_12.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-merge_01/2023-08-08-12_54_12.log) |
|  | oe_test_intltool_intltool-merge_02 | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-merge_02/2023-08-08-13_00_26.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-merge_02/2023-08-08-13_00_26.log) |
|  | oe_test_intltool_intltool-prepare | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-prepare/2023-08-08-13_27_38.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-prepare/2023-08-08-13_27_38.log) |
|  | oe_test_intltool_intltool-update | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-update/2023-08-08-13_06_15.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltool-update/2023-08-08-13_06_15.log) |
|  | oe_test_intltool_intltoolize | success | [./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltoolize/2023-08-08-13_34_14.log](./oe-rv2309/mugen-riscv/logs/intltool/oe_test_intltool_intltoolize/2023-08-08-13_34_14.log) |
| iperf3 | oe_test_iperf3_client | success | [./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_client/2023-08-12-22_31_04.log](./oe-rv2309/mugen-riscv/logs/iperf3/oe_test_iperf3_client/2023-08-12-22_31_04.log) |
| ipvsadm | oe_test_ipvs_SCEN_DR_01 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_01/2023-08-12-16_01_28.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_01/2023-08-12-16_01_28.log) |
|  | oe_test_ipvs_SCEN_DR_02 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_02/2023-08-12-16_04_46.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_02/2023-08-12-16_04_46.log) |
|  | oe_test_ipvs_SCEN_DR_03 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_03/2023-08-12-16_08_02.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_03/2023-08-12-16_08_02.log) |
|  | oe_test_ipvs_SCEN_DR_04 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_04/2023-08-12-16_11_23.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_04/2023-08-12-16_11_23.log) |
|  | oe_test_ipvs_SCEN_DR_07 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_07/2023-08-12-16_21_11.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_07/2023-08-12-16_21_11.log) |
|  | oe_test_ipvs_SCEN_DR_08 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_08/2023-08-12-16_24_34.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_DR_08/2023-08-12-16_24_34.log) |
|  | oe_test_ipvs_SCEN_TUN_01 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_01/2023-08-12-16_27_59.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_01/2023-08-12-16_27_59.log) |
|  | oe_test_ipvs_SCEN_TUN_02 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_02/2023-08-12-16_31_19.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_02/2023-08-12-16_31_19.log) |
|  | oe_test_ipvs_SCEN_TUN_03 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_03/2023-08-12-16_34_40.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_03/2023-08-12-16_34_40.log) |
|  | oe_test_ipvs_SCEN_TUN_04 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_04/2023-08-12-16_38_02.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_04/2023-08-12-16_38_02.log) |
|  | oe_test_ipvs_SCEN_TUN_07 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_07/2023-08-12-16_48_08.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_07/2023-08-12-16_48_08.log) |
|  | oe_test_ipvs_SCEN_TUN_08 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_08/2023-08-12-16_51_37.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_TUN_08/2023-08-12-16_51_37.log) |
|  | oe_test_ipvs_SCEN_off_httpd_01 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_off_httpd_01/2023-08-12-15_53_39.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_off_httpd_01/2023-08-12-15_53_39.log) |
|  | oe_test_ipvs_SCEN_on_firewalld_01 | success | [./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_on_firewalld_01/2023-08-12-15_57_34.log](./oe-rv2309/mugen-riscv/logs/ipvsadm/oe_test_ipvs_SCEN_on_firewalld_01/2023-08-12-15_57_34.log) |
| javapackages-tools | oe_test_binary_files_operation | success | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_binary_files_operation/2023-08-08-10_12_42.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_binary_files_operation/2023-08-08-10_12_42.log) |
|  | oe_test_build-classpath | success | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_build-classpath/2023-08-08-10_16_14.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_build-classpath/2023-08-08-10_16_14.log) |
|  | oe_test_build-jar-repository | success | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_build-jar-repository/2023-08-08-10_16_39.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_build-jar-repository/2023-08-08-10_16_39.log) |
|  | oe_test_find-shade-jar | success | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_find-shade-jar/2023-08-08-10_17_11.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_find-shade-jar/2023-08-08-10_17_11.log) |
|  | oe_test_javapackages-local | success | [./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_javapackages-local/2023-08-08-10_19_26.log](./oe-rv2309/mugen-riscv/logs/javapackages-tools/oe_test_javapackages-local/2023-08-08-10_19_26.log) |
| kernel | oe_test_hqlogic | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hqlogic/2023-08-08-11_11_48.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_hqlogic/2023-08-08-11_11_48.log) |
|  | oe_test_ipip | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_ipip/2023-08-08-11_05_29.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_ipip/2023-08-08-11_05_29.log) |
|  | oe_test_nbd | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_nbd/2023-08-08-11_21_49.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_nbd/2023-08-08-11_21_49.log) |
|  | oe_test_qla2xxx | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_qla2xxx/2023-08-08-11_25_52.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_qla2xxx/2023-08-08-11_25_52.log) |
|  | oe_test_qxl | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_qxl/2023-08-08-11_14_31.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_qxl/2023-08-08-11_14_31.log) |
|  | oe_test_softdog | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_softdog/2023-08-08-11_18_38.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_softdog/2023-08-08-11_18_38.log) |
|  | oe_test_xfs | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_xfs/2023-08-08-11_19_41.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_xfs/2023-08-08-11_19_41.log) |
|  | oe_test_cache_rw | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cache_rw/2023-08-08-11_07_33.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_cache_rw/2023-08-08-11_07_33.log) |
|  | oe_test_kernel_cmd_02 | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_02/2023-08-08-10_56_06.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_02/2023-08-08-10_56_06.log) |
|  | oe_test_kernel_cmd_03 | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_03/2023-08-08-10_58_49.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_kernel_cmd_03/2023-08-08-10_58_49.log) |
|  | oe_test_swappiness | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_swappiness/2023-08-08-11_18_00.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_swappiness/2023-08-08-11_18_00.log) |
|  | oe_test_vlan_8021q | success | [./oe-rv2309/mugen-riscv/logs/kernel/oe_test_vlan_8021q/2023-08-08-11_21_20.log](./oe-rv2309/mugen-riscv/logs/kernel/oe_test_vlan_8021q/2023-08-08-11_21_20.log) |
| keyutils | oe_test_keyutils-keyctl | success | [./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-keyctl/2023-08-08-10_52_39.log](./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-keyctl/2023-08-08-10_52_39.log) |
|  | oe_test_keyutils-libs | success | [./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-libs/2023-08-08-10_53_26.log](./oe-rv2309/mugen-riscv/logs/keyutils/oe_test_keyutils-libs/2023-08-08-10_53_26.log) |
| kmod | oe_test_modprobe | fail | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_modprobe/2023-08-09-03_51_36.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_modprobe/2023-08-09-03_51_36.log) |
|  | oe_test_rmmod | success | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_rmmod/2023-08-09-03_52_17.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_rmmod/2023-08-09-03_52_17.log) |
|  | oe_test_weak-modules | success | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_weak-modules/2023-08-09-03_52_30.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_weak-modules/2023-08-09-03_52_30.log) |
|  | oe_test_kmod | success | [./oe-rv2309/mugen-riscv/logs/kmod/oe_test_kmod/2023-08-09-03_51_08.log](./oe-rv2309/mugen-riscv/logs/kmod/oe_test_kmod/2023-08-09-03_51_08.log) |
| krb5 | oe_test_service_kadmin | success | [./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_kadmin/2023-08-08-15_08_32.log](./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_kadmin/2023-08-08-15_08_32.log) |
|  | oe_test_service_kprop | success | [./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_kprop/2023-08-08-15_17_06.log](./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_kprop/2023-08-08-15_17_06.log) |
|  | oe_test_service_krb5kdc | success | [./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_krb5kdc/2023-08-08-15_21_18.log](./oe-rv2309/mugen-riscv/logs/krb5/oe_test_service_krb5kdc/2023-08-08-15_21_18.log) |
| libaec | oe_test_libaec_aec | success | [./oe-rv2309/mugen-riscv/logs/libaec/oe_test_libaec_aec/2023-08-08-12_11_43.log](./oe-rv2309/mugen-riscv/logs/libaec/oe_test_libaec_aec/2023-08-08-12_11_43.log) |
| libappstream-glib | oe_test_libappstream-glib_appstream-builder_01 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-builder_01/2023-08-12-21_00_04.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-builder_01/2023-08-12-21_00_04.log) |
|  | oe_test_libappstream-glib_appstream-builder_02 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-builder_02/2023-08-12-21_07_03.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-builder_02/2023-08-12-21_07_03.log) |
|  | oe_test_libappstream-glib_appstream-compose | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-compose/2023-08-12-21_07_47.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-compose/2023-08-12-21_07_47.log) |
|  | oe_test_libappstream-glib_appstream-util_01 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_01/2023-08-12-21_08_41.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_01/2023-08-12-21_08_41.log) |
|  | oe_test_libappstream-glib_appstream-util_02 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_02/2023-08-12-21_09_28.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_02/2023-08-12-21_09_28.log) |
|  | oe_test_libappstream-glib_appstream-util_04 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_04/2023-08-12-21_11_10.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_04/2023-08-12-21_11_10.log) |
|  | oe_test_libappstream-glib_appstream-util_05 | success | [./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_05/2023-08-12-21_12_01.log](./oe-rv2309/mugen-riscv/logs/libappstream-glib/oe_test_libappstream-glib_appstream-util_05/2023-08-12-21_12_01.log) |
| libarchive | oe_test_libarchive_bsdcpio | success | [./oe-rv2309/mugen-riscv/logs/libarchive/oe_test_libarchive_bsdcpio/2023-08-08-15_58_05.log](./oe-rv2309/mugen-riscv/logs/libarchive/oe_test_libarchive_bsdcpio/2023-08-08-15_58_05.log) |
|  | oe_test_libarchive_bsdtar | success | [./oe-rv2309/mugen-riscv/logs/libarchive/oe_test_libarchive_bsdtar/2023-08-08-15_56_19.log](./oe-rv2309/mugen-riscv/logs/libarchive/oe_test_libarchive_bsdtar/2023-08-08-15_56_19.log) |
| libcanberra | oe_test_service_canberra-system-bootup | success | [./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-bootup/2023-08-08-10_41_25.log](./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-bootup/2023-08-08-10_41_25.log) |
|  | oe_test_service_canberra-system-shutdown | success | [./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-shutdown/2023-08-08-10_43_20.log](./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-shutdown/2023-08-08-10_43_20.log) |
|  | oe_test_service_canberra-system-shutdown-reboot | success | [./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-shutdown-reboot/2023-08-08-10_42_23.log](./oe-rv2309/mugen-riscv/logs/libcanberra/oe_test_service_canberra-system-shutdown-reboot/2023-08-08-10_42_23.log) |
| libcap | oe_test_acl_allow_change_nochange | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_allow_change_nochange/2023-08-08-09_57_01.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_allow_change_nochange/2023-08-08-09_57_01.log) |
|  | oe_test_acl_allow_change_ownership | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_allow_change_ownership/2023-08-08-09_56_48.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_allow_change_ownership/2023-08-08-09_56_48.log) |
|  | oe_test_acl_ignore_dal_across | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_ignore_dal_across/2023-08-08-09_56_24.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_ignore_dal_across/2023-08-08-09_56_24.log) |
|  | oe_test_acl_manage_net | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_manage_net/2023-08-08-09_59_00.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_manage_net/2023-08-08-09_59_00.log) |
|  | oe_test_acl_ordinary_users_setgid | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_ordinary_users_setgid/2023-08-08-09_57_38.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_ordinary_users_setgid/2023-08-08-09_57_38.log) |
|  | oe_test_acl_send_kill_notbelong | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_send_kill_notbelong/2023-08-08-09_57_20.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_send_kill_notbelong/2023-08-08-09_57_20.log) |
|  | oe_test_acl_verwrite_previous_rules | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_verwrite_previous_rules/2023-08-08-09_56_39.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_verwrite_previous_rules/2023-08-08-09_56_39.log) |
|  | oe_test_acl_bind_port | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_bind_port/2023-08-08-09_57_57.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_acl_bind_port/2023-08-08-09_57_57.log) |
|  | oe_test_libcap | success | [./oe-rv2309/mugen-riscv/logs/libcap/oe_test_libcap/2023-08-08-09_56_12.log](./oe-rv2309/mugen-riscv/logs/libcap/oe_test_libcap/2023-08-08-09_56_12.log) |
| libcgroup | oe_test_service_cgconfig | success | [./oe-rv2309/mugen-riscv/logs/libcgroup/oe_test_service_cgconfig/2023-08-08-20_19_29.log](./oe-rv2309/mugen-riscv/logs/libcgroup/oe_test_service_cgconfig/2023-08-08-20_19_29.log) |
| libdb | oe_test_libdb | success | [./oe-rv2309/mugen-riscv/logs/libdb/oe_test_libdb/2023-08-08-17_29_11.log](./oe-rv2309/mugen-riscv/logs/libdb/oe_test_libdb/2023-08-08-17_29_11.log) |
| libesmtp | oe_test_libesmtp | success | [./oe-rv2309/mugen-riscv/logs/libesmtp/oe_test_libesmtp/2023-08-08-16_50_22.log](./oe-rv2309/mugen-riscv/logs/libesmtp/oe_test_libesmtp/2023-08-08-16_50_22.log) |
| libgpg-error | oe_test_libgpg-error | success | [./oe-rv2309/mugen-riscv/logs/libgpg-error/oe_test_libgpg-error/2023-08-08-11_50_00.log](./oe-rv2309/mugen-riscv/logs/libgpg-error/oe_test_libgpg-error/2023-08-08-11_50_00.log) |
| libksba | oe_test_libksba | success | [./oe-rv2309/mugen-riscv/logs/libksba/oe_test_libksba/2023-08-08-17_35_02.log](./oe-rv2309/mugen-riscv/logs/libksba/oe_test_libksba/2023-08-08-17_35_02.log) |
| libldb | oe_test_libldb | success | [./oe-rv2309/mugen-riscv/logs/libldb/oe_test_libldb/2023-08-13-03_26_36.log](./oe-rv2309/mugen-riscv/logs/libldb/oe_test_libldb/2023-08-13-03_26_36.log) |
| libmemcached | oe_test_memaslap | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memaslap/2023-08-12-19_39_25.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memaslap/2023-08-12-19_39_25.log) |
|  | oe_test_memcapable | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcapable/2023-08-12-19_44_31.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcapable/2023-08-12-19_44_31.log) |
|  | oe_test_memcat | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcat/2023-08-12-19_46_58.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcat/2023-08-12-19_46_58.log) |
|  | oe_test_memcp | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcp/2023-08-12-19_49_01.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memcp/2023-08-12-19_49_01.log) |
|  | oe_test_memdump-memerror | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memdump-memerror/2023-08-12-19_51_20.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memdump-memerror/2023-08-12-19_51_20.log) |
|  | oe_test_memexist | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memexist/2023-08-12-19_53_22.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memexist/2023-08-12-19_53_22.log) |
|  | oe_test_memflush | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memflush/2023-08-12-19_55_26.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memflush/2023-08-12-19_55_26.log) |
|  | oe_test_memping | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memping/2023-08-12-19_57_26.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memping/2023-08-12-19_57_26.log) |
|  | oe_test_memrm | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memrm/2023-08-12-19_59_25.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memrm/2023-08-12-19_59_25.log) |
|  | oe_test_memslap | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memslap/2023-08-12-20_01_26.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memslap/2023-08-12-20_01_26.log) |
|  | oe_test_memstat | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memstat/2023-08-12-20_07_37.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memstat/2023-08-12-20_07_37.log) |
|  | oe_test_memtouch | success | [./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memtouch/2023-08-12-20_09_37.log](./oe-rv2309/mugen-riscv/logs/libmemcached/oe_test_memtouch/2023-08-12-20_09_37.log) |
| libmicrohttpd | oe_test_libmicrohttpd | success | [./oe-rv2309/mugen-riscv/logs/libmicrohttpd/oe_test_libmicrohttpd/2023-08-09-05_03_13.log](./oe-rv2309/mugen-riscv/logs/libmicrohttpd/oe_test_libmicrohttpd/2023-08-09-05_03_13.log) |
| libosinfo | oe_test_osinfo-query | success | [./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-query/2023-08-08-10_51_59.log](./oe-rv2309/mugen-riscv/logs/libosinfo/oe_test_osinfo-query/2023-08-08-10_51_59.log) |
| libreswan | oe_test_libreswan_ipsec_01 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_01/2023-08-08-02_34_59.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_01/2023-08-08-02_34_59.log) |
|  | oe_test_libreswan_ipsec_02 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_02/2023-08-08-02_36_33.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_02/2023-08-08-02_36_33.log) |
|  | oe_test_libreswan_ipsec_03 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_03/2023-08-08-02_37_32.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_03/2023-08-08-02_37_32.log) |
|  | oe_test_libreswan_ipsec_algparse | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_algparse/2023-08-08-02_39_49.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_algparse/2023-08-08-02_39_49.log) |
|  | oe_test_libreswan_ipsec_check | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_check/2023-08-08-02_43_15.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_check/2023-08-08-02_43_15.log) |
|  | oe_test_libreswan_ipsec_pluto | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_pluto/2023-08-08-02_44_29.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_pluto/2023-08-08-02_44_29.log) |
|  | oe_test_libreswan_ipsec_whack_1 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_1/2023-08-08-02_48_49.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_1/2023-08-08-02_48_49.log) |
|  | oe_test_libreswan_ipsec_whack_2 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_2/2023-08-08-02_49_49.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_2/2023-08-08-02_49_49.log) |
|  | oe_test_libreswan_ipsec_whack_3 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_3/2023-08-08-02_50_47.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_3/2023-08-08-02_50_47.log) |
|  | oe_test_libreswan_ipsec_whack_4 | success | [./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_4/2023-08-08-02_51_46.log](./oe-rv2309/mugen-riscv/logs/libreswan/oe_test_libreswan_ipsec_whack_4/2023-08-08-02_51_46.log) |
| libtar | oe_test_libtar | success | [./oe-rv2309/mugen-riscv/logs/libtar/oe_test_libtar/2023-08-09-09_59_36.log](./oe-rv2309/mugen-riscv/logs/libtar/oe_test_libtar/2023-08-09-09_59_36.log) |
| libunistring | oe_test_libunistring | success | [./oe-rv2309/mugen-riscv/logs/libunistring/oe_test_libunistring/2023-08-08-11_51_45.log](./oe-rv2309/mugen-riscv/logs/libunistring/oe_test_libunistring/2023-08-08-11_51_45.log) |
|  | oe_test_libunistring | success | [./oe-rv2309/mugen-riscv/logs/libunistring/oe_test_libunistring/2023-08-08-11_51_45.log](./oe-rv2309/mugen-riscv/logs/libunistring/oe_test_libunistring/2023-08-08-11_51_45.log) |
| libvirt | oe_test_socket_virtinterfaced-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced-ro/2023-08-09-02_47_26.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced-ro/2023-08-09-02_47_26.log) |
|  | oe_test_socket_virtnetworkd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd-admin/2023-08-09-02_53_59.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd-admin/2023-08-09-02_53_59.log) |
|  | oe_test_service_libvirt-guests | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_libvirt-guests/2023-08-09-02_16_11.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_libvirt-guests/2023-08-09-02_16_11.log) |
|  | oe_test_service_libvirtd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_libvirtd/2023-08-09-02_13_49.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_libvirtd/2023-08-09-02_13_49.log) |
|  | oe_test_service_virtinterfaced | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtinterfaced/2023-08-09-02_18_33.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtinterfaced/2023-08-09-02_18_33.log) |
|  | oe_test_service_virtlockd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtlockd/2023-08-09-02_20_53.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtlockd/2023-08-09-02_20_53.log) |
|  | oe_test_service_virtlogd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtlogd/2023-08-09-02_23_07.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtlogd/2023-08-09-02_23_07.log) |
|  | oe_test_service_virtnetworkd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnetworkd/2023-08-09-02_25_46.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnetworkd/2023-08-09-02_25_46.log) |
|  | oe_test_service_virtnodedevd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnodedevd/2023-08-09-02_28_40.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnodedevd/2023-08-09-02_28_40.log) |
|  | oe_test_service_virtnwfilterd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnwfilterd/2023-08-09-02_31_05.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtnwfilterd/2023-08-09-02_31_05.log) |
|  | oe_test_service_virtproxyd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtproxyd/2023-08-09-02_33_26.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtproxyd/2023-08-09-02_33_26.log) |
|  | oe_test_service_virtqemud | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtqemud/2023-08-09-02_35_43.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtqemud/2023-08-09-02_35_43.log) |
|  | oe_test_service_virtsecretd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtsecretd/2023-08-09-02_37_59.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtsecretd/2023-08-09-02_37_59.log) |
|  | oe_test_service_virtstoraged | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtstoraged/2023-08-09-02_39_34.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_service_virtstoraged/2023-08-09-02_39_34.log) |
|  | oe_test_socket_libvirtd-tcp | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-tcp/2023-08-09-02_44_08.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-tcp/2023-08-09-02_44_08.log) |
|  | oe_test_socket_libvirtd-tls | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-tls/2023-08-09-02_45_02.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_libvirtd-tls/2023-08-09-02_45_02.log) |
|  | oe_test_socket_virtinterfaced | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced/2023-08-09-02_48_56.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced/2023-08-09-02_48_56.log) |
|  | oe_test_socket_virtinterfaced-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced-admin/2023-08-09-02_45_56.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtinterfaced-admin/2023-08-09-02_45_56.log) |
|  | oe_test_socket_virtlockd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlockd-admin/2023-08-09-02_50_26.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlockd-admin/2023-08-09-02_50_26.log) |
|  | oe_test_socket_virtlogd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlogd-admin/2023-08-09-02_52_12.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtlogd-admin/2023-08-09-02_52_12.log) |
|  | oe_test_socket_virtnetworkd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd/2023-08-09-02_57_04.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd/2023-08-09-02_57_04.log) |
|  | oe_test_socket_virtnetworkd-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd-ro/2023-08-09-02_55_31.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnetworkd-ro/2023-08-09-02_55_31.log) |
|  | oe_test_socket_virtnodedevd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd/2023-08-09-03_01_35.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd/2023-08-09-03_01_35.log) |
|  | oe_test_socket_virtnodedevd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd-admin/2023-08-09-02_58_34.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd-admin/2023-08-09-02_58_34.log) |
|  | oe_test_socket_virtnodedevd-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd-ro/2023-08-09-03_00_05.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnodedevd-ro/2023-08-09-03_00_05.log) |
|  | oe_test_socket_virtnwfilterd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd/2023-08-09-03_06_04.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd/2023-08-09-03_06_04.log) |
|  | oe_test_socket_virtnwfilterd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd-admin/2023-08-09-03_03_06.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd-admin/2023-08-09-03_03_06.log) |
|  | oe_test_socket_virtnwfilterd-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd-ro/2023-08-09-03_04_34.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtnwfilterd-ro/2023-08-09-03_04_34.log) |
|  | oe_test_socket_virtproxyd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd/2023-08-09-03_09_21.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd/2023-08-09-03_09_21.log) |
|  | oe_test_socket_virtproxyd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-admin/2023-08-09-03_07_32.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-admin/2023-08-09-03_07_32.log) |
|  | oe_test_socket_virtproxyd-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-ro/2023-08-09-03_08_27.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-ro/2023-08-09-03_08_27.log) |
|  | oe_test_socket_virtproxyd-tcp | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-tcp/2023-08-09-03_10_15.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-tcp/2023-08-09-03_10_15.log) |
|  | oe_test_socket_virtproxyd-tls | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-tls/2023-08-09-03_11_10.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtproxyd-tls/2023-08-09-03_11_10.log) |
|  | oe_test_socket_virtqemud | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud/2023-08-09-03_13_57.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud/2023-08-09-03_13_57.log) |
|  | oe_test_socket_virtqemud-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud-admin/2023-08-09-03_12_05.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud-admin/2023-08-09-03_12_05.log) |
|  | oe_test_socket_virtqemud-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud-ro/2023-08-09-03_13_00.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtqemud-ro/2023-08-09-03_13_00.log) |
|  | oe_test_socket_virtsecretd | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd/2023-08-09-03_17_53.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd/2023-08-09-03_17_53.log) |
|  | oe_test_socket_virtsecretd-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd-admin/2023-08-09-03_14_53.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd-admin/2023-08-09-03_14_53.log) |
|  | oe_test_socket_virtsecretd-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd-ro/2023-08-09-03_16_23.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtsecretd-ro/2023-08-09-03_16_23.log) |
|  | oe_test_socket_virtstoraged | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged/2023-08-09-03_22_44.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged/2023-08-09-03_22_44.log) |
|  | oe_test_socket_virtstoraged-admin | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged-admin/2023-08-09-03_19_22.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged-admin/2023-08-09-03_19_22.log) |
|  | oe_test_socket_virtstoraged-ro | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged-ro/2023-08-09-03_21_03.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_socket_virtstoraged-ro/2023-08-09-03_21_03.log) |
|  | oe_test_target_virt-guest-shutdown | success | [./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_target_virt-guest-shutdown/2023-08-09-03_24_23.log](./oe-rv2309/mugen-riscv/logs/libvirt/oe_test_target_virt-guest-shutdown/2023-08-09-03_24_23.log) |
| libxslt | oe_test_libxslt | success | [./oe-rv2309/mugen-riscv/logs/libxslt/oe_test_libxslt/2023-08-09-05_03_15.log](./oe-rv2309/mugen-riscv/logs/libxslt/oe_test_libxslt/2023-08-09-05_03_15.log) |
| lksctp-tools | oe_test_lksctp-tools_checksctp | success | [./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_checksctp/2023-08-12-22_04_43.log](./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_checksctp/2023-08-12-22_04_43.log) |
|  | oe_test_lksctp-tools_sctp_darn_01 | success | [./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_darn_01/2023-08-12-22_07_57.log](./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_darn_01/2023-08-12-22_07_57.log) |
|  | oe_test_lksctp-tools_sctp_darn_02 | success | [./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_darn_02/2023-08-12-22_10_01.log](./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_darn_02/2023-08-12-22_10_01.log) |
|  | oe_test_lksctp-tools_sctp_status | success | [./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_status/2023-08-12-22_11_33.log](./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_status/2023-08-12-22_11_33.log) |
|  | oe_test_lksctp-tools_sctp_test | success | [./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_test/2023-08-12-22_13_24.log](./oe-rv2309/mugen-riscv/logs/lksctp-tools/oe_test_lksctp-tools_sctp_test/2023-08-12-22_13_24.log) |
| lldpad | oe_test_service_lldpad | success | [./oe-rv2309/mugen-riscv/logs/lldpad/oe_test_service_lldpad/2023-08-08-11_04_16.log](./oe-rv2309/mugen-riscv/logs/lldpad/oe_test_service_lldpad/2023-08-08-11_04_16.log) |
|  | oe_test_socket_lldpad | success | [./oe-rv2309/mugen-riscv/logs/lldpad/oe_test_socket_lldpad/2023-08-08-11_05_30.log](./oe-rv2309/mugen-riscv/logs/lldpad/oe_test_socket_lldpad/2023-08-08-11_05_30.log) |
| lm_sensors | oe_test_service_sensord | success | [./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_sensord/2023-08-12-23_24_57.log](./oe-rv2309/mugen-riscv/logs/lm_sensors/oe_test_service_sensord/2023-08-12-23_24_57.log) |
| lmdb | oe_test_lmdb | success | [./oe-rv2309/mugen-riscv/logs/lmdb/oe_test_lmdb/2023-08-08-20_06_27.log](./oe-rv2309/mugen-riscv/logs/lmdb/oe_test_lmdb/2023-08-08-20_06_27.log) |
| lorax | oe_test_socket_lorax-composer | success | [./oe-rv2309/mugen-riscv/logs/lorax/oe_test_socket_lorax-composer/2023-08-13-01_35_49.log](./oe-rv2309/mugen-riscv/logs/lorax/oe_test_socket_lorax-composer/2023-08-13-01_35_49.log) |
| ltrace | oe_test_ltrace_01 | success | [./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_01/2023-08-08-14_16_53.log](./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_01/2023-08-08-14_16_53.log) |
|  | oe_test_ltrace_02 | success | [./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_02/2023-08-08-14_22_14.log](./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_02/2023-08-08-14_22_14.log) |
|  | oe_test_ltrace_03 | success | [./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_03/2023-08-08-14_27_27.log](./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_03/2023-08-08-14_27_27.log) |
|  | oe_test_ltrace_04 | success | [./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_04/2023-08-08-14_32_57.log](./oe-rv2309/mugen-riscv/logs/ltrace/oe_test_ltrace_04/2023-08-08-14_32_57.log) |
| lvm2 | oe_test_service_lvm2-lvmpolld | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-lvmpolld/2023-08-09-03_17_13.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-lvmpolld/2023-08-09-03_17_13.log) |
|  | oe_test_service_lvm2-monitor | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-monitor/2023-08-09-03_17_48.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-monitor/2023-08-09-03_17_48.log) |
|  | oe_test_service_blk-availability | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_blk-availability/2023-08-09-03_14_11.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_blk-availability/2023-08-09-03_14_11.log) |
|  | oe_test_service_dm-event | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_dm-event/2023-08-09-03_14_52.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_dm-event/2023-08-09-03_14_52.log) |
|  | oe_test_service_lvm2-lvmdbusd | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-lvmdbusd/2023-08-09-03_15_27.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_service_lvm2-lvmdbusd/2023-08-09-03_15_27.log) |
|  | oe_test_socket_dm-event | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_socket_dm-event/2023-08-09-03_21_06.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_socket_dm-event/2023-08-09-03_21_06.log) |
|  | oe_test_socket_lvm2-lvmpolld | success | [./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_socket_lvm2-lvmpolld/2023-08-09-03_21_46.log](./oe-rv2309/mugen-riscv/logs/lvm2/oe_test_socket_lvm2-lvmpolld/2023-08-09-03_21_46.log) |
| lxc | oe_test_service_lxc-net | success | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_service_lxc-net/2023-08-08-03_22_14.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_service_lxc-net/2023-08-08-03_22_14.log) |
|  | oe_test_lxc_checkconfig_console | success | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_checkconfig_console/2023-08-08-03_31_09.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_checkconfig_console/2023-08-08-03_31_09.log) |
|  | oe_test_lxc_execute_freeze | success | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_execute_freeze/2023-08-08-03_31_46.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_lxc_execute_freeze/2023-08-08-03_31_46.log) |
|  | oe_test_service_lxc | success | [./oe-rv2309/mugen-riscv/logs/lxc/oe_test_service_lxc/2023-08-08-03_23_29.log](./oe-rv2309/mugen-riscv/logs/lxc/oe_test_service_lxc/2023-08-08-03_23_29.log) |
| man-db | oe_test_service_man-db | success | [./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db/2023-08-09-04_25_02.log](./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db/2023-08-09-04_25_02.log) |
|  | oe_test_service_man-db-cache-update | success | [./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db-cache-update/2023-08-09-04_24_43.log](./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db-cache-update/2023-08-09-04_24_43.log) |
|  | oe_test_service_man-db_01 | success | [./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db_01/2023-08-09-04_25_21.log](./oe-rv2309/mugen-riscv/logs/man-db/oe_test_service_man-db_01/2023-08-09-04_25_21.log) |
| mariadb | oe_test_mariadb-db | success | [./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_mariadb-db/2023-08-08-11_03_33.log](./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_mariadb-db/2023-08-08-11_03_33.log) |
|  | oe_test_mariadb-table | success | [./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_mariadb-table/2023-08-08-11_05_21.log](./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_mariadb-table/2023-08-08-11_05_21.log) |
|  | oe_test_service_mariadb | success | [./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_service_mariadb/2023-08-08-11_01_22.log](./oe-rv2309/mugen-riscv/logs/mariadb/oe_test_service_mariadb/2023-08-08-11_01_22.log) |
| mc | oe_test_mcdiff_01 | success | [./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcdiff_01/2023-08-08-10_33_46.log](./oe-rv2309/mugen-riscv/logs/mc/oe_test_mcdiff_01/2023-08-08-10_33_46.log) |
| memcached | oe_test_memcached_01 | success | [./oe-rv2309/mugen-riscv/logs/memcached/oe_test_memcached_01/2023-08-08-15_09_50.log](./oe-rv2309/mugen-riscv/logs/memcached/oe_test_memcached_01/2023-08-08-15_09_50.log) |
|  | oe_test_memcached_02 | success | [./oe-rv2309/mugen-riscv/logs/memcached/oe_test_memcached_02/2023-08-08-15_16_19.log](./oe-rv2309/mugen-riscv/logs/memcached/oe_test_memcached_02/2023-08-08-15_16_19.log) |
|  | oe_test_service_memcached | success | [./oe-rv2309/mugen-riscv/logs/memcached/oe_test_service_memcached/2023-08-08-15_23_06.log](./oe-rv2309/mugen-riscv/logs/memcached/oe_test_service_memcached/2023-08-08-15_23_06.log) |
| mksh | oe_test_mksh | success | [./oe-rv2309/mugen-riscv/logs/mksh/oe_test_mksh/2023-08-08-11_53_54.log](./oe-rv2309/mugen-riscv/logs/mksh/oe_test_mksh/2023-08-08-11_53_54.log) |
| mlocate | oe_test_service_mlocate-updatedb | success | [./oe-rv2309/mugen-riscv/logs/mlocate/oe_test_service_mlocate-updatedb/2023-08-13-03_37_36.log](./oe-rv2309/mugen-riscv/logs/mlocate/oe_test_service_mlocate-updatedb/2023-08-13-03_37_36.log) |
| multipath-tools | oe_test_multipath-tools_mpathconf | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_mpathconf/2023-08-08-13_07_59.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_mpathconf/2023-08-08-13_07_59.log) |
|  | oe_test_multipath-tools_mpathpersist | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_mpathpersist/2023-08-08-13_14_06.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_mpathpersist/2023-08-08-13_14_06.log) |
|  | oe_test_multipath-tools_multipath_01 | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_multipath_01/2023-08-08-13_26_11.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_multipath_01/2023-08-08-13_26_11.log) |
|  | oe_test_multipath-tools_multipath_02 | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_multipath_02/2023-08-08-13_41_49.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_multipath-tools_multipath_02/2023-08-08-13_41_49.log) |
|  | oe_test_service_multipathd | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_service_multipathd/2023-08-08-13_56_38.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_service_multipathd/2023-08-08-13_56_38.log) |
|  | oe_test_socket_multipathd | success | [./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_socket_multipathd/2023-08-08-14_04_26.log](./oe-rv2309/mugen-riscv/logs/multipath-tools/oe_test_socket_multipathd/2023-08-08-14_04_26.log) |
| ndisc6 | oe_test_ndisc6_addr2name | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_addr2name/2023-08-11-00_20_46.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_addr2name/2023-08-11-00_20_46.log) |
|  | oe_test_ndisc6_ndisc6 | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_ndisc6/2023-08-11-00_26_38.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_ndisc6/2023-08-11-00_26_38.log) |
|  | oe_test_ndisc6_rdnssd | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rdnssd/2023-08-11-00_32_16.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rdnssd/2023-08-11-00_32_16.log) |
|  | oe_test_ndisc6_rltraceroute6 | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rltraceroute6/2023-08-11-00_34_53.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_rltraceroute6/2023-08-11-00_34_53.log) |
|  | oe_test_ndisc6_tcpspray | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tcpspray/2023-08-11-00_37_47.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tcpspray/2023-08-11-00_37_47.log) |
|  | oe_test_ndisc6_tcptraceroute6 | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tcptraceroute6/2023-08-11-00_41_09.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tcptraceroute6/2023-08-11-00_41_09.log) |
|  | oe_test_ndisc6_tracert6 | success | [./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tracert6/2023-08-11-00_44_23.log](./oe-rv2309/mugen-riscv/logs/ndisc6/oe_test_ndisc6_tracert6/2023-08-11-00_44_23.log) |
| net-snmp | oe_test_service_snmpd | success | [./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_service_snmpd/2023-08-12-22_33_46.log](./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_service_snmpd/2023-08-12-22_33_46.log) |
|  | oe_test_service_snmptrapd | success | [./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_service_snmptrapd/2023-08-12-22_39_30.log](./oe-rv2309/mugen-riscv/logs/net-snmp/oe_test_service_snmptrapd/2023-08-12-22_39_30.log) |
| net-tools | oe_test_net-tools_iptunnel | success | [./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_iptunnel/2023-08-12-22_22_18.log](./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_iptunnel/2023-08-12-22_22_18.log) |
|  | oe_test_net-tools_netstat | success | [./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_netstat/2023-08-12-22_20_11.log](./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_net-tools_netstat/2023-08-12-22_20_11.log) |
|  | oe_test_service_arp-ethers | success | [./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_service_arp-ethers/2023-08-12-22_17_21.log](./oe-rv2309/mugen-riscv/logs/net-tools/oe_test_service_arp-ethers/2023-08-12-22_17_21.log) |
| nfs-utils | oe_test_service_auth-rpcgss-module | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_auth-rpcgss-module/2023-08-08-02_59_16.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_auth-rpcgss-module/2023-08-08-02_59_16.log) |
|  | oe_test_service_nfs | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs/2023-08-08-03_12_05.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs/2023-08-08-03_12_05.log) |
|  | oe_test_service_nfs-blkmap | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-blkmap/2023-08-08-03_00_32.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-blkmap/2023-08-08-03_00_32.log) |
|  | oe_test_service_nfs-idmap | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-idmap/2023-08-08-03_04_17.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-idmap/2023-08-08-03_04_17.log) |
|  | oe_test_service_nfs-idmapd | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-idmapd/2023-08-08-03_03_03.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-idmapd/2023-08-08-03_03_03.log) |
|  | oe_test_service_nfs-lock | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-lock/2023-08-08-03_05_31.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-lock/2023-08-08-03_05_31.log) |
|  | oe_test_service_nfs-mountd | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-mountd/2023-08-08-03_08_14.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-mountd/2023-08-08-03_08_14.log) |
|  | oe_test_service_nfs-secure | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-secure/2023-08-08-03_09_29.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-secure/2023-08-08-03_09_29.log) |
|  | oe_test_service_nfs-server | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-server/2023-08-08-03_10_43.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-server/2023-08-08-03_10_43.log) |
|  | oe_test_service_nfs-utils | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-utils/2023-08-08-03_13_26.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfs-utils/2023-08-08-03_13_26.log) |
|  | oe_test_service_nfsdcld | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfsdcld/2023-08-08-03_01_49.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_nfsdcld/2023-08-08-03_01_49.log) |
|  | oe_test_service_rpc-gssd | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-gssd/2023-08-08-03_14_39.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-gssd/2023-08-08-03_14_39.log) |
|  | oe_test_service_rpc-statd | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-statd/2023-08-08-03_17_05.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-statd/2023-08-08-03_17_05.log) |
|  | oe_test_service_rpc-statd-notify | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-statd-notify/2023-08-08-03_15_52.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_service_rpc-statd-notify/2023-08-08-03_15_52.log) |
|  | oe_test_target_nfs-client | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_target_nfs-client/2023-08-08-03_18_19.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_target_nfs-client/2023-08-08-03_18_19.log) |
|  | oe_test_target_rpc_pipefs | success | [./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_target_rpc_pipefs/2023-08-08-03_19_35.log](./oe-rv2309/mugen-riscv/logs/nfs-utils/oe_test_target_rpc_pipefs/2023-08-08-03_19_35.log) |
| nftables | oe_test_iptables_to_nftables | success | [./oe-rv2309/mugen-riscv/logs/nftables/oe_test_iptables_to_nftables/2023-08-08-11_50_08.log](./oe-rv2309/mugen-riscv/logs/nftables/oe_test_iptables_to_nftables/2023-08-08-11_50_08.log) |
| nghttp2 | oe_test_service_nghttpx | success | [./oe-rv2309/mugen-riscv/logs/nghttp2/oe_test_service_nghttpx/2023-08-08-16_56_50.log](./oe-rv2309/mugen-riscv/logs/nghttp2/oe_test_service_nghttpx/2023-08-08-16_56_50.log) |
| nspr | oe_test_nspr_001 | success | [./oe-rv2309/mugen-riscv/logs/nspr/oe_test_nspr_001/2023-08-08-11_16_40.log](./oe-rv2309/mugen-riscv/logs/nspr/oe_test_nspr_001/2023-08-08-11_16_40.log) |
|  | oe_test_nspr_002 | success | [./oe-rv2309/mugen-riscv/logs/nspr/oe_test_nspr_002/2023-08-08-11_17_39.log](./oe-rv2309/mugen-riscv/logs/nspr/oe_test_nspr_002/2023-08-08-11_17_39.log) |
| nss | oe_test_nss | success | [./oe-rv2309/mugen-riscv/logs/nss/oe_test_nss/2023-08-08-11_24_37.log](./oe-rv2309/mugen-riscv/logs/nss/oe_test_nss/2023-08-08-11_24_37.log) |
| nss-pam-ldapd | oe_test_service_nslcd | success | [./oe-rv2309/mugen-riscv/logs/nss-pam-ldapd/oe_test_service_nslcd/2023-08-08-11_56_09.log](./oe-rv2309/mugen-riscv/logs/nss-pam-ldapd/oe_test_service_nslcd/2023-08-08-11_56_09.log) |
| ntp | oe_test_service_ntp-wait | success | [./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntp-wait/2023-08-09-04_19_36.log](./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntp-wait/2023-08-09-04_19_36.log) |
|  | oe_test_service_ntpd | success | [./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntpd/2023-08-09-04_17_59.log](./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntpd/2023-08-09-04_17_59.log) |
|  | oe_test_service_ntpdate | success | [./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntpdate/2023-08-09-04_16_05.log](./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_ntpdate/2023-08-09-04_16_05.log) |
|  | oe_test_service_sntp | success | [./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_sntp/2023-08-09-04_21_23.log](./oe-rv2309/mugen-riscv/logs/ntp/oe_test_service_sntp/2023-08-09-04_21_23.log) |
| numad | oe_test_service_numad | success | [./oe-rv2309/mugen-riscv/logs/numad/oe_test_service_numad/2023-08-13-03_43_39.log](./oe-rv2309/mugen-riscv/logs/numad/oe_test_service_numad/2023-08-13-03_43_39.log) |
| oddjob | oe_test_service_oddjobd | success | [./oe-rv2309/mugen-riscv/logs/oddjob/oe_test_service_oddjobd/2023-08-09-05_06_11.log](./oe-rv2309/mugen-riscv/logs/oddjob/oe_test_service_oddjobd/2023-08-09-05_06_11.log) |
| open-iscsi | oe_test_service_iscsid | success | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsid/2023-08-08-18_53_35.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsid/2023-08-08-18_53_35.log) |
|  | oe_test_service_iscsi | success | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsi/2023-08-08-18_54_50.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_service_iscsi/2023-08-08-18_54_50.log) |
|  | oe_test_socket_iscsid | success | [./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_socket_iscsid/2023-08-08-18_57_24.log](./oe-rv2309/mugen-riscv/logs/open-iscsi/oe_test_socket_iscsid/2023-08-08-18_57_24.log) |
| open-isns | oe_test_service_isnsd | success | [./oe-rv2309/mugen-riscv/logs/open-isns/oe_test_service_isnsd/2023-08-08-11_50_02.log](./oe-rv2309/mugen-riscv/logs/open-isns/oe_test_service_isnsd/2023-08-08-11_50_02.log) |
| openblas | oe_test_openblas | success | [./oe-rv2309/mugen-riscv/logs/openblas/oe_test_openblas/2023-08-08-17_02_47.log](./oe-rv2309/mugen-riscv/logs/openblas/oe_test_openblas/2023-08-08-17_02_47.log) |
| openldap | oe_test_service_slapd | success | [./oe-rv2309/mugen-riscv/logs/openldap/oe_test_service_slapd/2023-08-08-11_58_46.log](./oe-rv2309/mugen-riscv/logs/openldap/oe_test_service_slapd/2023-08-08-11_58_46.log) |
| openscap | oe_test_fix_anisible | success | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_fix_anisible/2023-08-08-21_13_18.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_fix_anisible/2023-08-08-21_13_18.log) |
|  | oe_test_openscap_01 | success | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_openscap_01/2023-08-08-20_25_28.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_openscap_01/2023-08-08-20_25_28.log) |
|  | oe_test_scanning_system | success | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_scanning_system/2023-08-08-20_38_47.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_scanning_system/2023-08-08-20_38_47.log) |
|  | oe_test_service_oscap-remediate | success | [./oe-rv2309/mugen-riscv/logs/openscap/oe_test_service_oscap-remediate/2023-08-08-21_17_23.log](./oe-rv2309/mugen-riscv/logs/openscap/oe_test_service_oscap-remediate/2023-08-08-21_17_23.log) |
| openslp | oe_test_service_slpd | success | [./oe-rv2309/mugen-riscv/logs/openslp/oe_test_service_slpd/2023-08-13-03_50_17.log](./oe-rv2309/mugen-riscv/logs/openslp/oe_test_service_slpd/2023-08-13-03_50_17.log) |
| opensm | oe_test_service_opensm | success | [./oe-rv2309/mugen-riscv/logs/opensm/oe_test_service_opensm/2023-08-09-05_07_09.log](./oe-rv2309/mugen-riscv/logs/opensm/oe_test_service_opensm/2023-08-09-05_07_09.log) |
| openssh | oe_test_openssh_keychecking_no | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_keychecking_no/2023-08-11-00_21_52.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_keychecking_no/2023-08-11-00_21_52.log) |
|  | oe_test_openssh_keyscan | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_keyscan/2023-08-11-00_22_32.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_keyscan/2023-08-11-00_22_32.log) |
|  | oe_test_openssh_scp_r | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_r/2023-08-11-00_29_50.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_scp_r/2023-08-11-00_29_50.log) |
|  | oe_test_openssh_sftp | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_sftp/2023-08-11-00_31_52.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_sftp/2023-08-11-00_31_52.log) |
|  | oe_test_openssh_ssh_agent | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_agent/2023-08-11-00_33_00.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_agent/2023-08-11-00_33_00.log) |
|  | oe_test_openssh_ssh_command | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_command/2023-08-11-00_34_23.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_command/2023-08-11-00_34_23.log) |
|  | oe_test_openssh_ssh_user | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_user/2023-08-11-00_37_06.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_openssh_ssh_user/2023-08-11-00_37_06.log) |
|  | oe_test_sec_jump_login | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_sec_jump_login/2023-08-09-03_31_52.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_sec_jump_login/2023-08-09-03_31_52.log) |
|  | oe_test_service_sshd | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_service_sshd/2023-08-09-03_27_40.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_service_sshd/2023-08-09-03_27_40.log) |
|  | oe_test_socket_sshd | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_socket_sshd/2023-08-09-03_28_25.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_socket_sshd/2023-08-09-03_28_25.log) |
|  | oe_test_start_ssh | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_start_ssh/2023-08-09-03_29_40.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_start_ssh/2023-08-09-03_29_40.log) |
|  | oe_test_target_sshd-keygen | success | [./oe-rv2309/mugen-riscv/logs/openssh/oe_test_target_sshd-keygen/2023-08-09-03_29_06.log](./oe-rv2309/mugen-riscv/logs/openssh/oe_test_target_sshd-keygen/2023-08-09-03_29_06.log) |
| openssl | oe_test_openssl_concurrent_operations | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_concurrent_operations/2023-08-12-14_56_31.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_concurrent_operations/2023-08-12-14_56_31.log) |
|  | oe_test_openssl_enc | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_enc/2023-08-12-15_37_12.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_enc/2023-08-12-15_37_12.log) |
|  | oe_test_openssl_encrypting_character_string | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypting_character_string/2023-08-12-15_08_25.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_encrypting_character_string/2023-08-12-15_08_25.log) |
|  | oe_test_openssl_generate_random_number | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_random_number/2023-08-12-15_12_58.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_generate_random_number/2023-08-12-15_12_58.log) |
|  | oe_test_openssl_hmac | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_hmac/2023-08-12-15_39_32.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_hmac/2023-08-12-15_39_32.log) |
|  | oe_test_openssl_message_digest | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_message_digest/2023-08-12-15_16_37.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_message_digest/2023-08-12-15_16_37.log) |
|  | oe_test_openssl_one_way_encryption | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_one_way_encryption/2023-08-12-15_16_58.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_one_way_encryption/2023-08-12-15_16_58.log) |
|  | oe_test_openssl_related_concurrent_operations | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_related_concurrent_operations/2023-08-12-15_17_20.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_related_concurrent_operations/2023-08-12-15_17_20.log) |
|  | oe_test_openssl_repeated_execution | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_repeated_execution/2023-08-12-15_24_06.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_repeated_execution/2023-08-12-15_24_06.log) |
|  | oe_test_openssl_sm2 | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_sm2/2023-08-12-15_38_38.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_sm2/2023-08-12-15_38_38.log) |
|  | oe_test_openssl_sm4 | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_sm4/2023-08-12-15_39_00.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_sm4/2023-08-12-15_39_00.log) |
|  | oe_test_openssl_symmetric_encryption_decryption | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_symmetric_encryption_decryption/2023-08-12-15_36_50.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_symmetric_encryption_decryption/2023-08-12-15_36_50.log) |
|  | oe_test_openssl_symmetrically_encrypts_decrypts_files | success | [./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_symmetrically_encrypts_decrypts_files/2023-08-12-15_35_57.log](./oe-rv2309/mugen-riscv/logs/openssl/oe_test_openssl_symmetrically_encrypts_decrypts_files/2023-08-12-15_35_57.log) |
| openvswitch | oe_test_service_ovsdb-server | success | [./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovsdb-server/2023-08-12-21_19_36.log](./oe-rv2309/mugen-riscv/logs/openvswitch/oe_test_service_ovsdb-server/2023-08-12-21_19_36.log) |
| openwsman | oe_test_service_openwsmand | success | [./oe-rv2309/mugen-riscv/logs/openwsman/oe_test_service_openwsmand/2023-08-08-17_03_21.log](./oe-rv2309/mugen-riscv/logs/openwsman/oe_test_service_openwsmand/2023-08-08-17_03_21.log) |
| os-basic | oe_test_accessdb | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_accessdb/2023-08-08-11_31_57.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_accessdb/2023-08-08-11_31_57.log) |
|  | oe_test_aureport | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_aureport/2023-08-08-23_09_45.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_aureport/2023-08-08-23_09_45.log) |
|  | oe_test_c++ | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_c++/2023-08-08-11_16_36.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_c++/2023-08-08-11_16_36.log) |
|  | oe_test_disk_io_sched | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_io_sched/2023-08-08-23_05_25.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_disk_io_sched/2023-08-08-23_05_25.log) |
|  | oe_test_fuse | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_fuse/2023-08-08-11_26_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_fuse/2023-08-08-11_26_55.log) |
|  | oe_test_gmp | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gmp/2023-08-08-22_50_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gmp/2023-08-08-22_50_13.log) |
|  | oe_test_kernel_module_operation | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_module_operation/2023-08-08-21_42_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_module_operation/2023-08-08-21_42_07.log) |
|  | oe_test_libffi | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_libffi/2023-08-08-22_53_26.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_libffi/2023-08-08-22_53_26.log) |
|  | oe_test_net_VRF | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_VRF/2023-08-08-21_54_17.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_VRF/2023-08-08-21_54_17.log) |
|  | oe_test_nmcli_macsec | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_macsec/2023-08-08-19_12_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_macsec/2023-08-08-19_12_13.log) |
|  | oe_test_nmcli_set_bond | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_set_bond/2023-08-08-22_14_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_set_bond/2023-08-08-22_14_30.log) |
|  | oe_test_nmcli_set_team | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_set_team/2023-08-08-22_20_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_set_team/2023-08-08-22_20_48.log) |
|  | oe_test_system_log_recorded | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_recorded/2023-08-08-12_23_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_recorded/2023-08-08-12_23_29.log) |
|  | oe_test_zlib | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zlib/2023-08-08-22_32_02.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zlib/2023-08-08-22_32_02.log) |
|  | oe_test_ACL_dir | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ACL_dir/2023-08-08-22_43_35.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ACL_dir/2023-08-08-22_43_35.log) |
|  | oe_test_IOaccess_100Mfile | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_100Mfile/2023-08-08-20_57_10.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_IOaccess_100Mfile/2023-08-08-20_57_10.log) |
|  | oe_test_ProcMgmt_crontab_cmd | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_crontab_cmd/2023-08-08-19_47_06.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_crontab_cmd/2023-08-08-19_47_06.log) |
|  | oe_test_ProcMgmt_crontab_cronfile | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_crontab_cronfile/2023-08-08-19_45_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_crontab_cronfile/2023-08-08-19_45_39.log) |
|  | oe_test_ProcMgmt_iostat | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_iostat/2023-08-08-19_42_57.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_iostat/2023-08-08-19_42_57.log) |
|  | oe_test_ProcMgmt_pgrep | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_pgrep/2023-08-08-19_42_21.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_pgrep/2023-08-08-19_42_21.log) |
|  | oe_test_ProcMgmt_ps | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_ps/2023-08-08-19_41_37.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_ps/2023-08-08-19_41_37.log) |
|  | oe_test_ProcMgmt_start_kill | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_start_kill/2023-08-08-19_36_59.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_start_kill/2023-08-08-19_36_59.log) |
|  | oe_test_ProcMgmt_top | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_top/2023-08-08-19_36_26.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_top/2023-08-08-19_36_26.log) |
|  | oe_test_ProcMgmt_vmstat | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_vmstat/2023-08-08-19_33_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ProcMgmt_vmstat/2023-08-08-19_33_44.log) |
|  | oe_test_USBinfo | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_USBinfo/2023-08-08-19_31_11.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_USBinfo/2023-08-08-19_31_11.log) |
|  | oe_test_ar | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ar/2023-08-08-22_45_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ar/2023-08-08-22_45_44.log) |
|  | oe_test_auditc_01 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditc_01/2023-08-08-22_49_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_auditc_01/2023-08-08-22_49_28.log) |
|  | oe_test_autoscan | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_autoscan/2023-08-08-11_09_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_autoscan/2023-08-08-11_09_28.log) |
|  | oe_test_b2sum | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_b2sum/2023-08-08-11_41_36.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_b2sum/2023-08-08-11_41_36.log) |
|  | oe_test_base32 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_base32/2023-08-08-11_05_51.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_base32/2023-08-08-11_05_51.log) |
|  | oe_test_base64 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_base64/2023-08-08-11_10_52.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_base64/2023-08-08-11_10_52.log) |
|  | oe_test_basename | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basename/2023-08-08-22_47_24.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basename/2023-08-08-22_47_24.log) |
|  | oe_test_basic_UserMgmt_permission | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_UserMgmt_permission/2023-08-08-22_15_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_UserMgmt_permission/2023-08-08-22_15_39.log) |
|  | oe_test_basic_create_group | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_create_group/2023-08-08-20_07_31.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_create_group/2023-08-08-20_07_31.log) |
|  | oe_test_basic_modify_group | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_modify_group/2023-08-08-20_08_09.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_modify_group/2023-08-08-20_08_09.log) |
|  | oe_test_basic_modify_user | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_modify_user/2023-08-08-20_08_50.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_modify_user/2023-08-08-20_08_50.log) |
|  | oe_test_basic_set_account_expiration_time | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_set_account_expiration_time/2023-08-08-22_21_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_set_account_expiration_time/2023-08-08-22_21_55.log) |
|  | oe_test_basic_set_permissions | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_set_permissions/2023-08-08-22_22_42.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_basic_set_permissions/2023-08-08-22_22_42.log) |
|  | oe_test_brotli | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_brotli/2023-08-08-22_34_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_brotli/2023-08-08-22_34_48.log) |
|  | oe_test_bsdcat | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bsdcat/2023-08-08-11_37_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bsdcat/2023-08-08-11_37_28.log) |
|  | oe_test_bzcmp | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bzcmp/2023-08-09-00_06_17.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_bzcmp/2023-08-09-00_06_17.log) |
|  | oe_test_c_stat | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_c_stat/2023-08-08-23_54_10.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_c_stat/2023-08-08-23_54_10.log) |
|  | oe_test_cairo | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cairo/2023-08-08-23_51_14.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cairo/2023-08-08-23_51_14.log) |
|  | oe_test_cal | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cal/2023-08-08-11_08_22.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cal/2023-08-08-11_08_22.log) |
|  | oe_test_chgrp | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chgrp/2023-08-08-11_35_10.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chgrp/2023-08-08-11_35_10.log) |
|  | oe_test_chroot | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chroot/2023-08-08-23_55_03.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_chroot/2023-08-08-23_55_03.log) |
|  | oe_test_cksum | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cksum/2023-08-08-11_33_00.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cksum/2023-08-08-11_33_00.log) |
|  | oe_test_cmd_id | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_id/2023-08-08-20_21_33.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_id/2023-08-08-20_21_33.log) |
|  | oe_test_cmd_su_user | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_su_user/2023-08-08-19_59_49.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_su_user/2023-08-08-19_59_49.log) |
|  | oe_test_cmd_sudo | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_sudo/2023-08-08-20_22_09.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cmd_sudo/2023-08-08-20_22_09.log) |
|  | oe_test_comm | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_comm/2023-08-08-11_02_20.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_comm/2023-08-08-11_02_20.log) |
|  | oe_test_cut | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cut/2023-08-08-11_36_21.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_cut/2023-08-08-11_36_21.log) |
|  | oe_test_diff3 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_diff3/2023-08-08-11_25_11.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_diff3/2023-08-08-11_25_11.log) |
|  | oe_test_dircolors | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_dircolors/2023-08-08-10_56_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_dircolors/2023-08-08-10_56_29.log) |
|  | oe_test_du | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_du/2023-08-08-22_44_57.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_du/2023-08-08-22_44_57.log) |
|  | oe_test_exiv2 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_exiv2/2023-08-08-22_24_41.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_exiv2/2023-08-08-22_24_41.log) |
|  | oe_test_expand | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expand/2023-08-08-10_58_12.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expand/2023-08-08-10_58_12.log) |
|  | oe_test_expr | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expr/2023-08-08-11_00_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_expr/2023-08-08-11_00_13.log) |
|  | oe_test_factor | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_factor/2023-08-08-10_57_02.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_factor/2023-08-08-10_57_02.log) |
|  | oe_test_fold | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_fold/2023-08-08-11_01_14.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_fold/2023-08-08-11_01_14.log) |
|  | oe_test_groff | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_groff/2023-08-08-22_48_04.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_groff/2023-08-08-22_48_04.log) |
|  | oe_test_group_access | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_group_access/2023-08-08-20_53_45.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_group_access/2023-08-08-20_53_45.log) |
|  | oe_test_groupdel | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_groupdel/2023-08-08-21_44_25.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_groupdel/2023-08-08-21_44_25.log) |
|  | oe_test_gsettings | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gsettings/2023-08-08-22_40_18.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_gsettings/2023-08-08-22_40_18.log) |
|  | oe_test_history | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_history/2023-08-08-23_58_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_history/2023-08-08-23_58_28.log) |
|  | oe_test_home_directory | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_home_directory/2023-08-08-21_43_33.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_home_directory/2023-08-08-21_43_33.log) |
|  | oe_test_hwclock | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hwclock/2023-08-08-20_22_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hwclock/2023-08-08-20_22_55.log) |
|  | oe_test_hwclock | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hwclock/2023-08-08-20_22_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_hwclock/2023-08-08-20_22_55.log) |
|  | oe_test_json-c | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_json-c/2023-08-08-22_27_29.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_json-c/2023-08-08-22_27_29.log) |
|  | oe_test_kernel_cgroup | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_cgroup/2023-08-08-21_42_49.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_kernel_cgroup/2023-08-08-21_42_49.log) |
|  | oe_test_locate_001 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_locate_001/2023-08-09-00_02_47.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_locate_001/2023-08-09-00_02_47.log) |
|  | oe_test_look | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_look/2023-08-08-11_03_27.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_look/2023-08-08-11_03_27.log) |
|  | oe_test_lsusb | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsusb/2023-08-08-23_06_16.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lsusb/2023-08-08-23_06_16.log) |
|  | oe_test_lua | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lua/2023-08-08-23_42_46.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lua/2023-08-08-23_42_46.log) |
|  | oe_test_lz4 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lz4/2023-08-08-22_30_51.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_lz4/2023-08-08-22_30_51.log) |
|  | oe_test_man | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_man/2023-08-08-21_56_45.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_man/2023-08-08-21_56_45.log) |
|  | oe_test_md5sum | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_md5sum/2023-08-08-11_17_58.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_md5sum/2023-08-08-11_17_58.log) |
|  | oe_test_namei | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_namei/2023-08-09-00_15_51.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_namei/2023-08-09-00_15_51.log) |
|  | oe_test_net_IPVLAN | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_IPVLAN/2023-08-08-21_45_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_IPVLAN/2023-08-08-21_45_07.log) |
|  | oe_test_net_cmd_ifconfig | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_ifconfig/2023-08-08-21_52_08.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_ifconfig/2023-08-08-21_52_08.log) |
|  | oe_test_net_cmd_ping | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_ping/2023-08-08-22_01_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_ping/2023-08-08-22_01_30.log) |
|  | oe_test_net_cmd_telnet | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_telnet/2023-08-08-22_07_20.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_cmd_telnet/2023-08-08-22_07_20.log) |
|  | oe_test_net_conninfo_lsof | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_conninfo_lsof/2023-08-08-22_02_09.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_conninfo_lsof/2023-08-08-22_02_09.log) |
|  | oe_test_net_scriprts | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_scriprts/2023-08-08-21_49_02.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_scriprts/2023-08-08-21_49_02.log) |
|  | oe_test_net_setmode | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_setmode/2023-08-08-21_46_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_net_setmode/2023-08-08-21_46_48.log) |
|  | oe_test_nmcli_8023link | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_8023link/2023-08-08-19_29_28.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_8023link/2023-08-08-19_29_28.log) |
|  | oe_test_nmcli_Mgntconnect | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_Mgntconnect/2023-08-08-19_27_43.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_Mgntconnect/2023-08-08-19_27_43.log) |
|  | oe_test_nmcli_add_connect | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_add_connect/2023-08-08-19_26_00.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_add_connect/2023-08-08-19_26_00.log) |
|  | oe_test_nmcli_bridge | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_bridge/2023-08-08-19_23_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_bridge/2023-08-08-19_23_44.log) |
|  | oe_test_nmcli_con_reload | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_con_reload/2023-08-08-19_21_27.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_con_reload/2023-08-08-19_21_27.log) |
|  | oe_test_nmcli_config_gw | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_config_gw/2023-08-08-19_17_04.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_config_gw/2023-08-08-19_17_04.log) |
|  | oe_test_nmcli_device | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_device/2023-08-08-19_15_51.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_device/2023-08-08-19_15_51.log) |
|  | oe_test_nmcli_general | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_general/2023-08-08-19_15_18.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_general/2023-08-08-19_15_18.log) |
|  | oe_test_nmcli_route | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_route/2023-08-08-19_10_56.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_route/2023-08-08-19_10_56.log) |
|  | oe_test_nmcli_vlan | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_vlan/2023-08-08-19_06_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_nmcli_vlan/2023-08-08-19_06_30.log) |
|  | oe_test_password_blank | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_password_blank/2023-08-08-19_05_50.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_password_blank/2023-08-08-19_05_50.log) |
|  | oe_test_paste | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_paste/2023-08-08-11_34_05.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_paste/2023-08-08-11_34_05.log) |
|  | oe_test_patch | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_patch/2023-08-08-22_23_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_patch/2023-08-08-22_23_30.log) |
|  | oe_test_pcre_use | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_pcre_use/2023-08-08-22_41_05.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_pcre_use/2023-08-08-22_41_05.log) |
|  | oe_test_performance_monitor_pcp | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_performance_monitor_pcp/2023-08-08-18_58_42.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_performance_monitor_pcp/2023-08-08-18_58_42.log) |
|  | oe_test_popd_001 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_popd_001/2023-08-08-23_49_55.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_popd_001/2023-08-08-23_49_55.log) |
|  | oe_test_power_output_HTML | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_output_HTML/2023-08-08-18_53_25.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_output_HTML/2023-08-08-18_53_25.log) |
|  | oe_test_power_powertop_calibrate | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_calibrate/2023-08-08-18_49_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_calibrate/2023-08-08-18_49_13.log) |
|  | oe_test_power_powertop_startup | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_startup/2023-08-08-18_44_23.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_power_powertop_startup/2023-08-08-18_44_23.log) |
|  | oe_test_rev | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_rev/2023-08-09-00_16_20.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_rev/2023-08-09-00_16_20.log) |
|  | oe_test_sdiff | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sdiff/2023-08-08-11_24_17.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sdiff/2023-08-08-11_24_17.log) |
|  | oe_test_seq | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_seq/2023-08-09-00_21_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_seq/2023-08-09-00_21_34.log) |
|  | oe_test_server_fontconfig | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_fontconfig/2023-08-08-23_43_24.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_fontconfig/2023-08-08-23_43_24.log) |
|  | oe_test_server_git_install | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_git_install/2023-08-08-16_09_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_git_install/2023-08-08-16_09_07.log) |
|  | oe_test_server_httpd_port | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_port/2023-08-08-16_00_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_port/2023-08-08-16_00_39.log) |
|  | oe_test_server_httpd_recover | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_recover/2023-08-08-15_55_09.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_recover/2023-08-08-15_55_09.log) |
|  | oe_test_server_httpd_restart | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_restart/2023-08-08-15_40_07.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_restart/2023-08-08-15_40_07.log) |
|  | oe_test_server_httpd_tls | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_tls/2023-08-08-15_32_36.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_httpd_tls/2023-08-08-15_32_36.log) |
|  | oe_test_server_libtasn1 | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_libtasn1/2023-08-08-23_44_01.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_libtasn1/2023-08-08-23_44_01.log) |
|  | oe_test_server_openssh_key | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_key/2023-08-08-14_15_04.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_key/2023-08-08-14_15_04.log) |
|  | oe_test_server_openssh_restart | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_restart/2023-08-08-14_04_54.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_openssh_restart/2023-08-08-14_04_54.log) |
|  | oe_test_server_postgresql | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_postgresql/2023-08-08-13_40_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_postgresql/2023-08-08-13_40_13.log) |
|  | oe_test_server_subversion | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_subversion/2023-08-09-00_08_58.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_subversion/2023-08-09-00_08_58.log) |
|  | oe_test_server_vsftpd_restart | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_restart/2023-08-08-12_31_15.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_server_vsftpd_restart/2023-08-08-12_31_15.log) |
|  | oe_test_sha256sum | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sha256sum/2023-08-08-11_11_59.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_sha256sum/2023-08-08-11_11_59.log) |
|  | oe_test_shred | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_shred/2023-08-08-22_58_42.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_shred/2023-08-08-22_58_42.log) |
|  | oe_test_split | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_split/2023-08-08-23_49_21.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_split/2023-08-08-23_49_21.log) |
|  | oe_test_system_log_basic | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_basic/2023-08-08-12_28_20.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_basic/2023-08-08-12_28_20.log) |
|  | oe_test_system_log_device | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_device/2023-08-08-12_27_14.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_device/2023-08-08-12_27_14.log) |
|  | oe_test_system_log_process | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_process/2023-08-08-12_24_49.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_process/2023-08-08-12_24_49.log) |
|  | oe_test_system_log_security | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_security/2023-08-08-12_22_22.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_log_security/2023-08-08-12_22_22.log) |
|  | oe_test_system_monitor_process | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_process/2023-08-08-12_17_23.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_system_monitor_process/2023-08-08-12_17_23.log) |
|  | oe_test_systool | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_systool/2023-08-08-11_21_23.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_systool/2023-08-08-11_21_23.log) |
|  | oe_test_tail | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_tail/2023-08-08-11_18_36.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_tail/2023-08-08-11_18_36.log) |
|  | oe_test_tcl | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_tcl/2023-08-08-23_04_44.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_tcl/2023-08-08-23_04_44.log) |
|  | oe_test_ulimit | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ulimit/2023-08-08-11_42_39.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_ulimit/2023-08-08-11_42_39.log) |
|  | oe_test_unlink | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_unlink/2023-08-09-00_07_00.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_unlink/2023-08-09-00_07_00.log) |
|  | oe_test_versioninfo | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_versioninfo/2023-08-08-11_43_48.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_versioninfo/2023-08-08-11_43_48.log) |
|  | oe_test_whatis | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whatis/2023-08-08-22_48_50.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whatis/2023-08-08-22_48_50.log) |
|  | oe_test_whoami | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whoami/2023-08-08-22_30_13.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_whoami/2023-08-08-22_30_13.log) |
|  | oe_test_zcat | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zcat/2023-08-09-00_14_34.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zcat/2023-08-09-00_14_34.log) |
|  | oe_test_zgrep | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zgrep/2023-08-09-00_08_19.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zgrep/2023-08-09-00_08_19.log) |
|  | oe_test_zipdetails | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zipdetails/2023-08-09-00_13_53.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zipdetails/2023-08-09-00_13_53.log) |
|  | oe_test_zstd | success | [./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zstd/2023-08-08-11_28_30.log](./oe-rv2309/mugen-riscv/logs/os-basic/oe_test_zstd/2023-08-08-11_28_30.log) |
| os-storage | oe_test_storage_lvm_raid_synchronization | same | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_raid_synchronization/2023-08-13-14_43_27.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_raid_synchronization/2023-08-13-14_43_27.log) |
|  | oe_test_storage_Mutipath_configure_blacklist | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_blacklist/2023-08-13-12_44_47.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_blacklist/2023-08-13-12_44_47.log) |
|  | oe_test_storage_Mutipath_configure_defaults | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_defaults/2023-08-13-12_16_49.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_defaults/2023-08-13-12_16_49.log) |
|  | oe_test_storage_Mutipath_configure_device | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_device/2023-08-13-12_11_39.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_device/2023-08-13-12_11_39.log) |
|  | oe_test_storage_Mutipath_configure_section | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_section/2023-08-13-14_11_01.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_section/2023-08-13-14_11_01.log) |
|  | oe_test_storage_Mutipath_mpathconf | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_mpathconf/2023-08-13-12_48_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_mpathconf/2023-08-13-12_48_08.log) |
|  | oe_test_storage_Mutipath_various_fields | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_various_fields/2023-08-13-13_13_19.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_various_fields/2023-08-13-13_13_19.log) |
|  | oe_test_storage_Mutipath_view_info | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_view_info/2023-08-13-14_32_13.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_view_info/2023-08-13-14_32_13.log) |
|  | oe_test_storage_lvm_set_regionsize | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_regionsize/2023-08-13-15_16_49.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_set_regionsize/2023-08-13-15_16_49.log) |
|  | oe_test_storage_smb_cmd_rpcclient | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_rpcclient/2023-08-13-14_50_56.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_rpcclient/2023-08-13-14_50_56.log) |
|  | oe_test_storage_smb_cmd_smbcontrol | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbcontrol/2023-08-13-14_13_47.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbcontrol/2023-08-13-14_13_47.log) |
|  | oe_test_storage_smb_cmd_smbstatus | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbstatus/2023-08-13-15_17_22.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_smbstatus/2023-08-13-15_17_22.log) |
|  | oe_test_storage_smb_cmd_testparm | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_testparm/2023-08-13-12_14_33.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_cmd_testparm/2023-08-13-12_14_33.log) |
|  | oe_test_storage_smb_guest_share | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_guest_share/2023-08-13-14_17_02.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_smb_guest_share/2023-08-13-14_17_02.log) |
|  | oe_test_storage_Mutipath_configure_parameter | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_parameter/2023-08-13-12_51_29.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_configure_parameter/2023-08-13-12_51_29.log) |
|  | oe_test_storage_Mutipath_initramfs | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_initramfs/2023-08-13-13_40_13.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_Mutipath_initramfs/2023-08-13-13_40_13.log) |
|  | oe_test_storage_btrfs_001 | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_btrfs_001/2023-08-13-14_52_23.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_btrfs_001/2023-08-13-14_52_23.log) |
|  | oe_test_storage_btrfs_002 | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_btrfs_002/2023-08-13-13_23_27.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_btrfs_002/2023-08-13-13_23_27.log) |
|  | oe_test_storage_diskpartiton_view_lsblk | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_view_lsblk/2023-08-13-14_21_43.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_diskpartiton_view_lsblk/2023-08-13-14_21_43.log) |
|  | oe_test_storage_fileCMD_cd | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cd/2023-08-13-13_04_08.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cd/2023-08-13-13_04_08.log) |
|  | oe_test_storage_fileCMD_chmod | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_chmod/2023-08-13-13_04_35.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_chmod/2023-08-13-13_04_35.log) |
|  | oe_test_storage_fileCMD_chown | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_chown/2023-08-13-14_51_53.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_chown/2023-08-13-14_51_53.log) |
|  | oe_test_storage_fileCMD_cp | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cp/2023-08-13-14_55_21.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_cp/2023-08-13-14_55_21.log) |
|  | oe_test_storage_fileCMD_df | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_df/2023-08-13-13_03_06.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_df/2023-08-13-13_03_06.log) |
|  | oe_test_storage_fileCMD_file | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_file/2023-08-13-15_23_17.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_file/2023-08-13-15_23_17.log) |
|  | oe_test_storage_fileCMD_grep | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_grep/2023-08-13-13_01_46.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_grep/2023-08-13-13_01_46.log) |
|  | oe_test_storage_fileCMD_gzip | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_gzip/2023-08-13-15_16_27.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_gzip/2023-08-13-15_16_27.log) |
|  | oe_test_storage_fileCMD_ln | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_ln/2023-08-13-14_28_42.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_ln/2023-08-13-14_28_42.log) |
|  | oe_test_storage_fileCMD_ls | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_ls/2023-08-13-14_50_32.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_ls/2023-08-13-14_50_32.log) |
|  | oe_test_storage_fileCMD_mkdir | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mkdir/2023-08-13-15_14_29.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mkdir/2023-08-13-15_14_29.log) |
|  | oe_test_storage_fileCMD_mv | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mv/2023-08-13-13_39_48.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_mv/2023-08-13-13_39_48.log) |
|  | oe_test_storage_fileCMD_rmdir | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_rmdir/2023-08-13-14_31_46.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_rmdir/2023-08-13-14_31_46.log) |
|  | oe_test_storage_fileCMD_sort | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_sort/2023-08-13-15_11_31.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_sort/2023-08-13-15_11_31.log) |
|  | oe_test_storage_fileCMD_tar | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_tar/2023-08-13-14_27_10.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_tar/2023-08-13-14_27_10.log) |
|  | oe_test_storage_fileCMD_touch | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_touch/2023-08-13-14_22_05.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_touch/2023-08-13-14_22_05.log) |
|  | oe_test_storage_fileCMD_unzip | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_unzip/2023-08-13-14_19_32.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_unzip/2023-08-13-14_19_32.log) |
|  | oe_test_storage_fileCMD_wc | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_wc/2023-08-13-14_39_18.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_wc/2023-08-13-14_39_18.log) |
|  | oe_test_storage_fileCMD_zip | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_zip/2023-08-13-14_40_31.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileCMD_zip/2023-08-13-14_40_31.log) |
|  | oe_test_storage_fileaccess_bashrc | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileaccess_bashrc/2023-08-13-14_44_20.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_fileaccess_bashrc/2023-08-13-14_44_20.log) |
|  | oe_test_storage_lvm_help_info | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_help_info/2023-08-13-13_33_04.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_help_info/2023-08-13-13_33_04.log) |
|  | oe_test_storage_lvm_print_info | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print_info/2023-08-13-15_15_26.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_lvm_print_info/2023-08-13-15_15_26.log) |
|  | oe_test_storage_mount_prevent_copy | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_prevent_copy/2023-08-13-14_13_25.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_prevent_copy/2023-08-13-14_13_25.log) |
|  | oe_test_storage_mount_shared | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_shared/2023-08-13-13_21_31.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_mount_shared/2023-08-13-13_21_31.log) |
|  | oe_test_storage_nfs_RPC | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_RPC/2023-08-13-14_47_13.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_RPC/2023-08-13-14_47_13.log) |
|  | oe_test_storage_nfs_exportfs | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_exportfs/2023-08-13-14_49_06.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_exportfs/2023-08-13-14_49_06.log) |
|  | oe_test_storage_nfs_modify_port | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_modify_port/2023-08-13-12_53_28.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_modify_port/2023-08-13-12_53_28.log) |
|  | oe_test_storage_nfs_rpcinfo | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_rpcinfo/2023-08-13-14_57_17.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_rpcinfo/2023-08-13-14_57_17.log) |
|  | oe_test_storage_nfs_showmount | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_showmount/2023-08-13-14_35_30.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_showmount/2023-08-13-14_35_30.log) |
|  | oe_test_storage_nfs_start_server | success | [./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_start_server/2023-08-13-14_20_09.log](./oe-rv2309/mugen-riscv/logs/os-storage/oe_test_storage_nfs_start_server/2023-08-13-14_20_09.log) |
| ostree | oe_test_service_ostree-finalize-staged | success | [./oe-rv2309/mugen-riscv/logs/ostree/oe_test_service_ostree-finalize-staged/2023-08-08-12_01_23.log](./oe-rv2309/mugen-riscv/logs/ostree/oe_test_service_ostree-finalize-staged/2023-08-08-12_01_23.log) |
| pam | oe_test_service_pam_namespace | success | [./oe-rv2309/mugen-riscv/logs/pam/oe_test_service_pam_namespace/2023-08-13-03_56_57.log](./oe-rv2309/mugen-riscv/logs/pam/oe_test_service_pam_namespace/2023-08-13-03_56_57.log) |
| papi | oe_test_papi_avail_01 | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_avail_01/2023-08-12-21_34_01.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_avail_01/2023-08-12-21_34_01.log) |
|  | oe_test_papi_avail_02 | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_avail_02/2023-08-12-21_37_20.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_avail_02/2023-08-12-21_37_20.log) |
|  | oe_test_papi_command_line | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_command_line/2023-08-12-21_39_05.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_command_line/2023-08-12-21_39_05.log) |
|  | oe_test_papi_native_avail_01 | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_native_avail_01/2023-08-12-21_41_52.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_native_avail_01/2023-08-12-21_41_52.log) |
|  | oe_test_papi_native_avail_02 | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_native_avail_02/2023-08-12-21_43_34.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_native_avail_02/2023-08-12-21_43_34.log) |
|  | oe_test_papi_xml_event_info | success | [./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_xml_event_info/2023-08-12-21_45_17.log](./oe-rv2309/mugen-riscv/logs/papi/oe_test_papi_xml_event_info/2023-08-12-21_45_17.log) |
| patchutils | oe_test_patchutils_combinediff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_combinediff_02/2023-08-08-09_50_46.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_combinediff_02/2023-08-08-09_50_46.log) |
|  | oe_test_patchutils_filterdiff_01 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_01/2023-08-08-09_51_38.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_01/2023-08-08-09_51_38.log) |
|  | oe_test_patchutils_filterdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_02/2023-08-08-09_52_26.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_02/2023-08-08-09_52_26.log) |
|  | oe_test_patchutils_filterdiff_03 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_03/2023-08-08-09_53_12.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_filterdiff_03/2023-08-08-09_53_12.log) |
|  | oe_test_patchutils_flipdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_flipdiff_02/2023-08-08-09_54_52.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_flipdiff_02/2023-08-08-09_54_52.log) |
|  | oe_test_patchutils_grepdiff_01 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_01/2023-08-08-09_55_33.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_01/2023-08-08-09_55_33.log) |
|  | oe_test_patchutils_grepdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_02/2023-08-08-09_56_18.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_02/2023-08-08-09_56_18.log) |
|  | oe_test_patchutils_grepdiff_03 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_03/2023-08-08-09_57_04.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_03/2023-08-08-09_57_04.log) |
|  | oe_test_patchutils_grepdiff_04 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_04/2023-08-08-09_57_50.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_grepdiff_04/2023-08-08-09_57_50.log) |
|  | oe_test_patchutils_interdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_interdiff_02/2023-08-08-09_59_23.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_interdiff_02/2023-08-08-09_59_23.log) |
|  | oe_test_patchutils_lsdiff_01 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_01/2023-08-08-10_00_11.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_01/2023-08-08-10_00_11.log) |
|  | oe_test_patchutils_lsdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_02/2023-08-08-10_00_56.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_02/2023-08-08-10_00_56.log) |
|  | oe_test_patchutils_lsdiff_03 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_03/2023-08-08-10_01_42.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_03/2023-08-08-10_01_42.log) |
|  | oe_test_patchutils_lsdiff_04 | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_04/2023-08-08-10_02_27.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_lsdiff_04/2023-08-08-10_02_27.log) |
|  | oe_test_patchutils_splitdiff | success | [./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_splitdiff/2023-08-08-10_03_14.log](./oe-rv2309/mugen-riscv/logs/patchutils/oe_test_patchutils_splitdiff/2023-08-08-10_03_14.log) |
| pbzip2 | oe_test_pbzip2_01 | success | [./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_01/2023-08-08-10_45_42.log](./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_01/2023-08-08-10_45_42.log) |
|  | oe_test_pbzip2_02 | success | [./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_02/2023-08-08-10_46_22.log](./oe-rv2309/mugen-riscv/logs/pbzip2/oe_test_pbzip2_02/2023-08-08-10_46_22.log) |
| pcp | oe_test_dbpmda | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_dbpmda/2023-08-12-12_49_23.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_dbpmda/2023-08-12-12_49_23.log) |
|  | oe_test_pcp | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp/2023-08-12-12_57_01.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp/2023-08-12-12_57_01.log) |
|  | oe_test_pcp-summary_pcp-vmstat_pmcd_wait | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp-summary_pcp-vmstat_pmcd_wait/2023-08-12-13_32_56.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp-summary_pcp-vmstat_pmcd_wait/2023-08-12-13_32_56.log) |
|  | oe_test_pcp_pcp-import-collectl2pcp | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-collectl2pcp/2023-08-12-14_19_57.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pcp_pcp-import-collectl2pcp/2023-08-12-14_19_57.log) |
|  | oe_test_pmconfig_pmie_check | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmconfig_pmie_check/2023-08-12-13_34_48.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmconfig_pmie_check/2023-08-12-13_34_48.log) |
|  | oe_test_pmdate | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdate/2023-08-12-12_59_14.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdate/2023-08-12-12_59_14.log) |
|  | oe_test_pmdumplog_01 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdumplog_01/2023-08-12-13_00_21.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdumplog_01/2023-08-12-13_00_21.log) |
|  | oe_test_pmdumplog_02 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdumplog_02/2023-08-12-13_01_36.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmdumplog_02/2023-08-12-13_01_36.log) |
|  | oe_test_pmevent_01 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmevent_01/2023-08-12-13_02_52.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmevent_01/2023-08-12-13_02_52.log) |
|  | oe_test_pmfind_pmgenmap_pmie2col_pminfo_01 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmfind_pmgenmap_pmie2col_pminfo_01/2023-08-12-13_06_45.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmfind_pmgenmap_pmie2col_pminfo_01/2023-08-12-13_06_45.log) |
|  | oe_test_pminfo_02 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pminfo_02/2023-08-12-13_08_31.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pminfo_02/2023-08-12-13_08_31.log) |
|  | oe_test_pminfo_03 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pminfo_03/2023-08-12-13_09_44.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pminfo_03/2023-08-12-13_09_44.log) |
|  | oe_test_pmjson | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmjson/2023-08-12-13_10_53.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmjson/2023-08-12-13_10_53.log) |
|  | oe_test_pmlogconf_pmlogsize | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogconf_pmlogsize/2023-08-12-13_13_15.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogconf_pmlogsize/2023-08-12-13_13_15.log) |
|  | oe_test_pmlogger_daily_02 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_02/2023-08-12-13_45_52.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_daily_02/2023-08-12-13_45_52.log) |
|  | oe_test_pmlogger_merge_pmlogger_rewrite | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_merge_pmlogger_rewrite/2023-08-12-13_52_28.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogger_merge_pmlogger_rewrite/2023-08-12-13_52_28.log) |
|  | oe_test_pmloglabel | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmloglabel/2023-08-12-13_14_29.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmloglabel/2023-08-12-13_14_29.log) |
|  | oe_test_pmlogreduce_pmpause_pmpost_pmsleep | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogreduce_pmpause_pmpost_pmsleep/2023-08-12-13_54_09.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogreduce_pmpause_pmpost_pmsleep/2023-08-12-13_54_09.log) |
|  | oe_test_pmlogsummary_01 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogsummary_01/2023-08-12-13_15_41.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogsummary_01/2023-08-12-13_15_41.log) |
|  | oe_test_pmlogsummary_02 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogsummary_02/2023-08-12-13_17_59.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmlogsummary_02/2023-08-12-13_17_59.log) |
|  | oe_test_pmprobe_02 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmprobe_02/2023-08-12-13_21_30.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmprobe_02/2023-08-12-13_21_30.log) |
|  | oe_test_pmpython_mkaf_pcp-python | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmpython_mkaf_pcp-python/2023-08-12-13_22_42.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmpython_mkaf_pcp-python/2023-08-12-13_22_42.log) |
|  | oe_test_pmstat | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmstat/2023-08-12-13_24_01.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmstat/2023-08-12-13_24_01.log) |
|  | oe_test_pmstore_install-sh | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmstore_install-sh/2023-08-12-13_27_08.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmstore_install-sh/2023-08-12-13_27_08.log) |
|  | oe_test_pmval_01 | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmval_01/2023-08-12-13_28_20.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_pmval_01/2023-08-12-13_28_20.log) |
|  | oe_test_service_pmcd | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmcd/2023-08-12-14_41_09.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmcd/2023-08-12-14_41_09.log) |
|  | oe_test_service_pmie | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmie/2023-08-12-14_44_01.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmie/2023-08-12-14_44_01.log) |
|  | oe_test_service_pmproxy | success | [./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmproxy/2023-08-12-14_51_47.log](./oe-rv2309/mugen-riscv/logs/pcp/oe_test_service_pmproxy/2023-08-12-14_51_47.log) |
| pcre2 | oe_test_pcre2grep | success | [./oe-rv2309/mugen-riscv/logs/pcre2/oe_test_pcre2grep/2023-08-09-05_08_26.log](./oe-rv2309/mugen-riscv/logs/pcre2/oe_test_pcre2grep/2023-08-09-05_08_26.log) |
| pcsc-lite | oe_test_service_pcscd | success | [./oe-rv2309/mugen-riscv/logs/pcsc-lite/oe_test_service_pcscd/2023-08-08-16_03_33.log](./oe-rv2309/mugen-riscv/logs/pcsc-lite/oe_test_service_pcscd/2023-08-08-16_03_33.log) |
|  | oe_test_socket_pcscd | success | [./oe-rv2309/mugen-riscv/logs/pcsc-lite/oe_test_socket_pcscd/2023-08-08-16_08_51.log](./oe-rv2309/mugen-riscv/logs/pcsc-lite/oe_test_socket_pcscd/2023-08-08-16_08_51.log) |
| pesign | oe_test_pesign_authvar | success | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_authvar/2023-08-08-10_15_18.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_authvar/2023-08-08-10_15_18.log) |
|  | oe_test_pesign_base_01 | success | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_base_01/2023-08-08-10_13_26.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_base_01/2023-08-08-10_13_26.log) |
|  | oe_test_pesign_pesign-client_01 | success | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesign-client_01/2023-08-08-10_16_09.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesign-client_01/2023-08-08-10_16_09.log) |
|  | oe_test_pesign_pesign-client_02 | success | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesign-client_02/2023-08-08-10_17_00.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_pesign_pesign-client_02/2023-08-08-10_17_00.log) |
|  | oe_test_service_pesign | success | [./oe-rv2309/mugen-riscv/logs/pesign/oe_test_service_pesign/2023-08-08-10_09_36.log](./oe-rv2309/mugen-riscv/logs/pesign/oe_test_service_pesign/2023-08-08-10_09_36.log) |
| pigz | oe_test_pigz | success | [./oe-rv2309/mugen-riscv/logs/pigz/oe_test_pigz/2023-08-08-11_53_07.log](./oe-rv2309/mugen-riscv/logs/pigz/oe_test_pigz/2023-08-08-11_53_07.log) |
| pixman | oe_test_pixman | success | [./oe-rv2309/mugen-riscv/logs/pixman/oe_test_pixman/2023-08-08-17_09_58.log](./oe-rv2309/mugen-riscv/logs/pixman/oe_test_pixman/2023-08-08-17_09_58.log) |
| pkgconf | oe_test_pkgconf | success | [./oe-rv2309/mugen-riscv/logs/pkgconf/oe_test_pkgconf/2023-08-08-12_03_52.log](./oe-rv2309/mugen-riscv/logs/pkgconf/oe_test_pkgconf/2023-08-08-12_03_52.log) |
| plymouth | oe_test_service_plymouth-halt | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-halt/2023-08-08-09_49_53.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-halt/2023-08-08-09_49_53.log) |
|  | oe_test_service_plymouth-kexec | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-kexec/2023-08-08-09_51_48.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-kexec/2023-08-08-09_51_48.log) |
|  | oe_test_service_plymouth-poweroff | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-poweroff/2023-08-08-09_53_40.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-poweroff/2023-08-08-09_53_40.log) |
|  | oe_test_service_plymouth-quit | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-quit/2023-08-08-09_55_28.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-quit/2023-08-08-09_55_28.log) |
|  | oe_test_service_plymouth-quit-wait | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-quit-wait/2023-08-08-09_57_14.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-quit-wait/2023-08-08-09_57_14.log) |
|  | oe_test_service_plymouth-read-write | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-read-write/2023-08-08-09_59_01.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-read-write/2023-08-08-09_59_01.log) |
|  | oe_test_service_plymouth-reboot | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-reboot/2023-08-08-10_00_45.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-reboot/2023-08-08-10_00_45.log) |
|  | oe_test_service_plymouth-start | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-start/2023-08-08-10_02_32.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-start/2023-08-08-10_02_32.log) |
|  | oe_test_service_plymouth-switch-root | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-switch-root/2023-08-08-10_04_19.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_plymouth-switch-root/2023-08-08-10_04_19.log) |
|  | oe_test_service_systemd-ask-password-plymouth | success | [./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_systemd-ask-password-plymouth/2023-08-08-10_05_57.log](./oe-rv2309/mugen-riscv/logs/plymouth/oe_test_service_systemd-ask-password-plymouth/2023-08-08-10_05_57.log) |
| pngquant | oe_test_pngquant | success | [./oe-rv2309/mugen-riscv/logs/pngquant/oe_test_pngquant/2023-08-13-03_59_35.log](./oe-rv2309/mugen-riscv/logs/pngquant/oe_test_pngquant/2023-08-13-03_59_35.log) |
| policycoreutils | oe_test_service_selinux-autorelabel | success | [./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_selinux-autorelabel/2023-08-08-20_07_14.log](./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_selinux-autorelabel/2023-08-08-20_07_14.log) |
|  | oe_test_service_selinux-autorelabel-mark | success | [./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_selinux-autorelabel-mark/2023-08-08-20_07_05.log](./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_service_selinux-autorelabel-mark/2023-08-08-20_07_05.log) |
|  | oe_test_target_selinux-autorelabel | success | [./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_target_selinux-autorelabel/2023-08-08-20_07_22.log](./oe-rv2309/mugen-riscv/logs/policycoreutils/oe_test_target_selinux-autorelabel/2023-08-08-20_07_22.log) |
| poppler | oe_test_poppler | success | [./oe-rv2309/mugen-riscv/logs/poppler/oe_test_poppler/2023-08-08-11_54_59.log](./oe-rv2309/mugen-riscv/logs/poppler/oe_test_poppler/2023-08-08-11_54_59.log) |
| postfix | oe_test_postfix_configuration | success | [./oe-rv2309/mugen-riscv/logs/postfix/oe_test_postfix_configuration/2023-08-08-11_10_21.log](./oe-rv2309/mugen-riscv/logs/postfix/oe_test_postfix_configuration/2023-08-08-11_10_21.log) |
|  | oe_test_service_postfix | success | [./oe-rv2309/mugen-riscv/logs/postfix/oe_test_service_postfix/2023-08-08-11_08_03.log](./oe-rv2309/mugen-riscv/logs/postfix/oe_test_service_postfix/2023-08-08-11_08_03.log) |
| powertop | oe_test_powertop | success | [./oe-rv2309/mugen-riscv/logs/powertop/oe_test_powertop/2023-08-13-02_08_00.log](./oe-rv2309/mugen-riscv/logs/powertop/oe_test_powertop/2023-08-13-02_08_00.log) |
|  | oe_test_service_powertop | success | [./oe-rv2309/mugen-riscv/logs/powertop/oe_test_service_powertop/2023-08-13-02_07_26.log](./oe-rv2309/mugen-riscv/logs/powertop/oe_test_service_powertop/2023-08-13-02_07_26.log) |
| procps-ng | oe_test_procps-ng-pmap | success | [./oe-rv2309/mugen-riscv/logs/procps-ng/oe_test_procps-ng-pmap/2023-08-09-09_59_35.log](./oe-rv2309/mugen-riscv/logs/procps-ng/oe_test_procps-ng-pmap/2023-08-09-09_59_35.log) |
| protobuf | oe_test_protobuf | success | [./oe-rv2309/mugen-riscv/logs/protobuf/oe_test_protobuf/2023-08-08-12_06_06.log](./oe-rv2309/mugen-riscv/logs/protobuf/oe_test_protobuf/2023-08-08-12_06_06.log) |
| protobuf-c | oe_test_protobuf-c_01 | success | [./oe-rv2309/mugen-riscv/logs/protobuf-c/oe_test_protobuf-c_01/2023-08-08-12_42_34.log](./oe-rv2309/mugen-riscv/logs/protobuf-c/oe_test_protobuf-c_01/2023-08-08-12_42_34.log) |
| psacct | oe_test_psacct | success | [./oe-rv2309/mugen-riscv/logs/psacct/oe_test_psacct/2023-08-09-04_44_01.log](./oe-rv2309/mugen-riscv/logs/psacct/oe_test_psacct/2023-08-09-04_44_01.log) |
|  | oe_test_service_psacct | success | [./oe-rv2309/mugen-riscv/logs/psacct/oe_test_service_psacct/2023-08-09-04_45_11.log](./oe-rv2309/mugen-riscv/logs/psacct/oe_test_service_psacct/2023-08-09-04_45_11.log) |
| python-rtslib | oe_test_service_target | success | [./oe-rv2309/mugen-riscv/logs/python-rtslib/oe_test_service_target/2023-08-09-05_10_40.log](./oe-rv2309/mugen-riscv/logs/python-rtslib/oe_test_service_target/2023-08-09-05_10_40.log) |
| qpdf | oe_test_qpdf_qpdf_04 | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_04/2023-08-09-03_38_01.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_04/2023-08-09-03_38_01.log) |
|  | oe_test_qpdf_qpdf_05 | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_05/2023-08-09-03_39_04.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_05/2023-08-09-03_39_04.log) |
|  | oe_test_qpdf_qpdf_06 | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_06/2023-08-09-03_40_07.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_06/2023-08-09-03_40_07.log) |
|  | oe_test_qpdf_qpdf_07 | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_07/2023-08-09-03_41_09.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_07/2023-08-09-03_41_09.log) |
|  | oe_test_qpdf_qpdf_09 | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_09/2023-08-09-03_43_11.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_qpdf_09/2023-08-09-03_43_11.log) |
|  | oe_test_qpdf_zlib-flate | success | [./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_zlib-flate/2023-08-09-03_45_10.log](./oe-rv2309/mugen-riscv/logs/qpdf/oe_test_qpdf_zlib-flate/2023-08-09-03_45_10.log) |
| qperf | oe_test_qperf_01 | success | [./oe-rv2309/mugen-riscv/logs/qperf/oe_test_qperf_01/2023-08-15-08_07_52.log](./oe-rv2309/mugen-riscv/logs/qperf/oe_test_qperf_01/2023-08-15-08_07_52.log) |
| rasdaemon | oe_test_service_rasdaemon | success | [./oe-rv2309/mugen-riscv/logs/rasdaemon/oe_test_service_rasdaemon/2023-08-08-16_05_42.log](./oe-rv2309/mugen-riscv/logs/rasdaemon/oe_test_service_rasdaemon/2023-08-08-16_05_42.log) |
| rdate | oe_test_rdate | success | [./oe-rv2309/mugen-riscv/logs/rdate/oe_test_rdate/2023-08-08-12_09_36.log](./oe-rv2309/mugen-riscv/logs/rdate/oe_test_rdate/2023-08-08-12_09_36.log) |
| rdma-core | oe_test_socket_ibacm | success | [./oe-rv2309/mugen-riscv/logs/rdma-core/oe_test_socket_ibacm/2023-08-08-11_14_27.log](./oe-rv2309/mugen-riscv/logs/rdma-core/oe_test_socket_ibacm/2023-08-08-11_14_27.log) |
|  | oe_test_service_srp_daemon | success | [./oe-rv2309/mugen-riscv/logs/rdma-core/oe_test_service_srp_daemon/2023-08-08-11_13_12.log](./oe-rv2309/mugen-riscv/logs/rdma-core/oe_test_service_srp_daemon/2023-08-08-11_13_12.log) |
| realmd | oe_test_service_realmd | success | [./oe-rv2309/mugen-riscv/logs/realmd/oe_test_service_realmd/2023-08-13-04_38_11.log](./oe-rv2309/mugen-riscv/logs/realmd/oe_test_service_realmd/2023-08-13-04_38_11.log) |
| rpcbind | oe_test_service_rpcbind | success | [./oe-rv2309/mugen-riscv/logs/rpcbind/oe_test_service_rpcbind/2023-08-13-02_15_51.log](./oe-rv2309/mugen-riscv/logs/rpcbind/oe_test_service_rpcbind/2023-08-13-02_15_51.log) |
|  | oe_test_socket_rpcbind | success | [./oe-rv2309/mugen-riscv/logs/rpcbind/oe_test_socket_rpcbind/2023-08-13-02_20_44.log](./oe-rv2309/mugen-riscv/logs/rpcbind/oe_test_socket_rpcbind/2023-08-13-02_20_44.log) |
| rpmdevtools | oe_test_rpmdevtools_rpmdev-bumpspec | success | [./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-bumpspec/2023-08-09-04_43_16.log](./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-bumpspec/2023-08-09-04_43_16.log) |
|  | oe_test_rpmdevtools_rpmdev-vercmp | success | [./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-vercmp/2023-08-09-04_11_30.log](./oe-rv2309/mugen-riscv/logs/rpmdevtools/oe_test_rpmdevtools_rpmdev-vercmp/2023-08-09-04_11_30.log) |
| rrdtool | oe_test_rrdtool_create_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_create_01/2023-08-08-00_06_54.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_create_01/2023-08-08-00_06_54.log) |
|  | oe_test_rrdtool_create_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_create_02/2023-08-08-00_08_00.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_create_02/2023-08-08-00_08_00.log) |
|  | oe_test_rrdtool_dump | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_dump/2023-08-08-00_32_30.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_dump/2023-08-08-00_32_30.log) |
|  | oe_test_rrdtool_fetch | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_fetch/2023-08-08-00_34_39.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_fetch/2023-08-08-00_34_39.log) |
|  | oe_test_rrdtool_flushcached | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_flushcached/2023-08-08-00_39_04.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_flushcached/2023-08-08-00_39_04.log) |
|  | oe_test_rrdtool_graph_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_01/2023-08-08-00_10_11.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_01/2023-08-08-00_10_11.log) |
|  | oe_test_rrdtool_graph_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_02/2023-08-08-00_11_14.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_02/2023-08-08-00_11_14.log) |
|  | oe_test_rrdtool_graph_03 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_03/2023-08-08-00_12_19.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_03/2023-08-08-00_12_19.log) |
|  | oe_test_rrdtool_graph_04 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_04/2023-08-08-00_13_25.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_04/2023-08-08-00_13_25.log) |
|  | oe_test_rrdtool_graph_05 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_05/2023-08-08-00_14_33.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_05/2023-08-08-00_14_33.log) |
|  | oe_test_rrdtool_graph_06 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_06/2023-08-08-00_15_41.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_06/2023-08-08-00_15_41.log) |
|  | oe_test_rrdtool_graph_07 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_07/2023-08-08-00_16_47.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_07/2023-08-08-00_16_47.log) |
|  | oe_test_rrdtool_graph_08 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_08/2023-08-08-00_17_54.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_08/2023-08-08-00_17_54.log) |
|  | oe_test_rrdtool_graph_09 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_09/2023-08-08-00_19_01.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_09/2023-08-08-00_19_01.log) |
|  | oe_test_rrdtool_graph_10 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_10/2023-08-08-00_20_09.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graph_10/2023-08-08-00_20_09.log) |
|  | oe_test_rrdtool_graphv_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_01/2023-08-08-00_21_15.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_01/2023-08-08-00_21_15.log) |
|  | oe_test_rrdtool_graphv_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_02/2023-08-08-00_22_21.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_02/2023-08-08-00_22_21.log) |
|  | oe_test_rrdtool_graphv_03 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_03/2023-08-08-00_23_29.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_03/2023-08-08-00_23_29.log) |
|  | oe_test_rrdtool_graphv_04 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_04/2023-08-08-00_24_36.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_04/2023-08-08-00_24_36.log) |
|  | oe_test_rrdtool_graphv_05 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_05/2023-08-08-00_25_44.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_05/2023-08-08-00_25_44.log) |
|  | oe_test_rrdtool_graphv_06 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_06/2023-08-08-00_26_51.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_06/2023-08-08-00_26_51.log) |
|  | oe_test_rrdtool_graphv_07 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_07/2023-08-08-00_28_01.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_07/2023-08-08-00_28_01.log) |
|  | oe_test_rrdtool_graphv_08 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_08/2023-08-08-00_29_09.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_08/2023-08-08-00_29_09.log) |
|  | oe_test_rrdtool_graphv_09 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_09/2023-08-08-00_30_17.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_09/2023-08-08-00_30_17.log) |
|  | oe_test_rrdtool_graphv_10 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_10/2023-08-08-00_31_25.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_graphv_10/2023-08-08-00_31_25.log) |
|  | oe_test_rrdtool_info | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_info/2023-08-08-00_35_52.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_info/2023-08-08-00_35_52.log) |
|  | oe_test_rrdtool_restore | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_restore/2023-08-08-00_33_34.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_restore/2023-08-08-00_33_34.log) |
|  | oe_test_rrdtool_rrdcached_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcached_01/2023-08-08-00_40_10.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcached_01/2023-08-08-00_40_10.log) |
|  | oe_test_rrdtool_rrdcached_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcached_02/2023-08-08-00_41_14.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcached_02/2023-08-08-00_41_14.log) |
|  | oe_test_rrdtool_rrdcreate_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcreate_01/2023-08-08-00_42_20.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcreate_01/2023-08-08-00_42_20.log) |
|  | oe_test_rrdtool_rrdcreate_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcreate_02/2023-08-08-00_43_24.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_rrdcreate_02/2023-08-08-00_43_24.log) |
|  | oe_test_rrdtool_update | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_update/2023-08-08-00_09_04.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_update/2023-08-08-00_09_04.log) |
|  | oe_test_rrdtool_xport_01 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_xport_01/2023-08-08-00_36_57.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_xport_01/2023-08-08-00_36_57.log) |
|  | oe_test_rrdtool_xport_02 | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_xport_02/2023-08-08-00_38_01.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_rrdtool_xport_02/2023-08-08-00_38_01.log) |
|  | oe_test_service_rrdcached | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_service_rrdcached/2023-08-08-00_03_59.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_service_rrdcached/2023-08-08-00_03_59.log) |
|  | oe_test_socket_rrdcached | success | [./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_socket_rrdcached/2023-08-08-00_05_30.log](./oe-rv2309/mugen-riscv/logs/rrdtool/oe_test_socket_rrdcached/2023-08-08-00_05_30.log) |
| rsync | oe_test_service_rsyncd | success | [./oe-rv2309/mugen-riscv/logs/rsync/oe_test_service_rsyncd/2023-08-08-20_12_16.log](./oe-rv2309/mugen-riscv/logs/rsync/oe_test_service_rsyncd/2023-08-08-20_12_16.log) |
|  | oe_test_socket_rsyncd | success | [./oe-rv2309/mugen-riscv/logs/rsync/oe_test_socket_rsyncd/2023-08-08-20_13_37.log](./oe-rv2309/mugen-riscv/logs/rsync/oe_test_socket_rsyncd/2023-08-08-20_13_37.log) |
| rsyslog | oe_test_rsyslog_function_postgreSQL | success | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_postgreSQL/2023-08-08-00_50_14.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_function_postgreSQL/2023-08-08-00_50_14.log) |
|  | oe_test_rsyslog_reliability_operation | success | [./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_operation/2023-08-08-02_28_49.log](./oe-rv2309/mugen-riscv/logs/rsyslog/oe_test_rsyslog_reliability_operation/2023-08-08-02_28_49.log) |
| rtkit | oe_test_service_rtkit-daemon | success | [./oe-rv2309/mugen-riscv/logs/rtkit/oe_test_service_rtkit-daemon/2023-08-08-11_57_58.log](./oe-rv2309/mugen-riscv/logs/rtkit/oe_test_service_rtkit-daemon/2023-08-08-11_57_58.log) |
| ruby | oe_test_gem | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_gem/2023-08-08-10_57_05.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_gem/2023-08-08-10_57_05.log) |
|  | oe_test_irb_02 | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_02/2023-08-08-11_09_39.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_irb_02/2023-08-08-11_09_39.log) |
|  | oe_test_rake_01 | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_01/2023-08-08-11_21_25.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_01/2023-08-08-11_21_25.log) |
|  | oe_test_rake_03 | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_03/2023-08-08-11_32_12.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rake_03/2023-08-08-11_32_12.log) |
|  | oe_test_rdoc_02 | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rdoc_02/2023-08-08-11_49_20.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_rdoc_02/2023-08-08-11_49_20.log) |
|  | oe_test_ri | success | [./oe-rv2309/mugen-riscv/logs/ruby/oe_test_ri/2023-08-08-11_58_43.log](./oe-rv2309/mugen-riscv/logs/ruby/oe_test_ri/2023-08-08-11_58_43.log) |
| samba | oe_test_service_nmb | success | [./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_nmb/2023-08-08-13_54_52.log](./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_nmb/2023-08-08-13_54_52.log) |
|  | oe_test_service_smb | success | [./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_smb/2023-08-08-14_23_37.log](./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_smb/2023-08-08-14_23_37.log) |
|  | oe_test_service_winbind | success | [./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_winbind/2023-08-08-14_27_49.log](./oe-rv2309/mugen-riscv/logs/samba/oe_test_service_winbind/2023-08-08-14_27_49.log) |
| sane-backends | oe_test_socket_saned | success | [./oe-rv2309/mugen-riscv/logs/sane-backends/oe_test_socket_saned/2023-08-08-17_15_45.log](./oe-rv2309/mugen-riscv/logs/sane-backends/oe_test_socket_saned/2023-08-08-17_15_45.log) |
| sanlock | oe_test_sanlock_wdmd | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_wdmd/2023-08-08-10_11_42.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_wdmd/2023-08-08-10_11_42.log) |
|  | oe_test_sanlock_base_01 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_01/2023-08-08-10_05_30.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_01/2023-08-08-10_05_30.log) |
|  | oe_test_sanlock_base_02 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_02/2023-08-08-10_07_23.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_02/2023-08-08-10_07_23.log) |
|  | oe_test_sanlock_base_03 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_03/2023-08-08-10_08_55.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_03/2023-08-08-10_08_55.log) |
|  | oe_test_sanlock_base_04 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_04/2023-08-08-10_10_23.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_base_04/2023-08-08-10_10_23.log) |
|  | oe_test_sanlock_sanlk-reset_01 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk-reset_01/2023-08-08-10_13_00.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk-reset_01/2023-08-08-10_13_00.log) |
|  | oe_test_sanlock_sanlk-reset_02 | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk-reset_02/2023-08-08-10_14_50.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk-reset_02/2023-08-08-10_14_50.log) |
|  | oe_test_sanlock_sanlk_resetd | success | [./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk_resetd/2023-08-08-10_16_59.log](./oe-rv2309/mugen-riscv/logs/sanlock/oe_test_sanlock_sanlk_resetd/2023-08-08-10_16_59.log) |
| sendmail | oe_test_service_sendmail | success | [./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_service_sendmail/2023-08-08-15_32_49.log](./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_service_sendmail/2023-08-08-15_32_49.log) |
|  | oe_test_service_sm-client | success | [./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_service_sm-client/2023-08-08-15_45_31.log](./oe-rv2309/mugen-riscv/logs/sendmail/oe_test_service_sm-client/2023-08-08-15_45_31.log) |
| slang | oe_test_slang_001 | success | [./oe-rv2309/mugen-riscv/logs/slang/oe_test_slang_001/2023-08-08-12_11_59.log](./oe-rv2309/mugen-riscv/logs/slang/oe_test_slang_001/2023-08-08-12_11_59.log) |
| smartmontools | oe_test_service_smartd | success | [./oe-rv2309/mugen-riscv/logs/smartmontools/oe_test_service_smartd/2023-08-13-04_44_15.log](./oe-rv2309/mugen-riscv/logs/smartmontools/oe_test_service_smartd/2023-08-13-04_44_15.log) |
| smoke-basic-os | oe_test_IPV6_traceroute6_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_02/2023-08-08-11_57_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_02/2023-08-08-11_57_47.log) |
|  | oe_test_arping | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_arping/2023-08-08-11_37_07.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_arping/2023-08-08-11_37_07.log) |
|  | oe_test_audit_fixed_memory_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_fixed_memory_02/2023-08-08-13_43_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_fixed_memory_02/2023-08-08-13_43_04.log) |
|  | oe_test_bbr_04 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bbr_04/2023-08-08-13_44_03.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bbr_04/2023-08-08-13_44_03.log) |
|  | oe_test_bonding_SCEN_05 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bonding_SCEN_05/2023-08-08-13_44_11.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bonding_SCEN_05/2023-08-08-13_44_11.log) |
|  | oe_test_gcc_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc_001/2023-08-08-14_01_15.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc_001/2023-08-08-14_01_15.log) |
|  | oe_test_ifconfig_ipv6_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ifconfig_ipv6_01/2023-08-08-11_52_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ifconfig_ipv6_01/2023-08-08-11_52_55.log) |
|  | oe_test_ip_ipv6_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_01/2023-08-08-11_53_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_01/2023-08-08-11_53_47.log) |
|  | oe_test_ip_ipv6_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_02/2023-08-08-11_53_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_02/2023-08-08-11_53_56.log) |
|  | oe_test_ip_ipv6_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_03/2023-08-08-11_54_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_03/2023-08-08-11_54_05.log) |
|  | oe_test_ip_ipv6_04 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_04/2023-08-08-11_54_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_ipv6_04/2023-08-08-11_54_13.log) |
|  | oe_test_ip_rule_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_rule_01/2023-08-08-11_54_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_rule_01/2023-08-08-11_54_48.log) |
|  | oe_test_ip_rule_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_rule_02/2023-08-08-11_54_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ip_rule_02/2023-08-08-11_54_55.log) |
|  | oe_test_iproute_ipv6_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iproute_ipv6_01/2023-08-08-11_54_29.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iproute_ipv6_01/2023-08-08-11_54_29.log) |
|  | oe_test_ipv6_VLAN_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_VLAN_01/2023-08-08-11_59_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_VLAN_01/2023-08-08-11_59_16.log) |
|  | oe_test_ipv6_VLAN_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_VLAN_02/2023-08-08-11_59_24.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_VLAN_02/2023-08-08-11_59_24.log) |
|  | oe_test_iscsi | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iscsi/2023-08-08-10_31_27.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iscsi/2023-08-08-10_31_27.log) |
|  | oe_test_iscsid | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iscsid/2023-08-08-12_00_20.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iscsid/2023-08-08-12_00_20.log) |
|  | oe_test_libcgroup_04 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_04/2023-08-08-12_06_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_04/2023-08-08-12_06_19.log) |
|  | oe_test_libxml2_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libxml2_001/2023-08-08-13_59_42.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libxml2_001/2023-08-08-13_59_42.log) |
|  | oe_test_libxml2_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libxml2_002/2023-08-08-14_00_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libxml2_002/2023-08-08-14_00_34.log) |
|  | oe_test_mtr | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mtr/2023-08-08-13_05_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mtr/2023-08-08-13_05_44.log) |
|  | oe_test_normal_tcpdump_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_02/2023-08-08-13_48_08.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_02/2023-08-08-13_48_08.log) |
|  | oe_test_normal_tcpdump_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_03/2023-08-08-12_14_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_03/2023-08-08-12_14_56.log) |
|  | oe_test_normal_tcpdump_04 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_04/2023-08-08-13_48_57.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_normal_tcpdump_04/2023-08-08-13_48_57.log) |
|  | oe_test_numactl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_numactl/2023-08-08-10_42_52.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_numactl/2023-08-08-10_42_52.log) |
|  | oe_test_rollback | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rollback/2023-08-08-10_57_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rollback/2023-08-08-10_57_05.log) |
|  | oe_test_route_ipv6 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_route_ipv6/2023-08-08-12_24_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_route_ipv6/2023-08-08-12_24_44.log) |
|  | oe_test_rule_ipv6 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rule_ipv6/2023-08-08-12_49_50.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rule_ipv6/2023-08-08-12_49_50.log) |
|  | oe_test_service | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_service/2023-08-08-12_49_57.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_service/2023-08-08-12_49_57.log) |
|  | oe_test_stat | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_stat/2023-08-08-12_51_14.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_stat/2023-08-08-12_51_14.log) |
|  | oe_test_systemd_journald | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_journald/2023-08-08-12_56_00.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_journald/2023-08-08-12_56_00.log) |
|  | oe_test_tftp_ipv6 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tftp_ipv6/2023-08-08-12_57_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tftp_ipv6/2023-08-08-12_57_04.log) |
|  | oe_test_user_debug_iotop_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_debug_iotop_03/2023-08-08-13_56_23.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_debug_iotop_03/2023-08-08-13_56_23.log) |
|  | oe_test_IPV6_traceroute6_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_01/2023-08-08-11_57_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_01/2023-08-08-11_57_05.log) |
|  | oe_test_IPV6_traceroute6_tracepath6 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_tracepath6/2023-08-08-11_58_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPV6_traceroute6_tracepath6/2023-08-08-11_58_34.log) |
|  | oe_test_IPVLAN_07 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPVLAN_07/2023-08-08-13_45_18.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_IPVLAN_07/2023-08-08-13_45_18.log) |
|  | oe_test_Netlink_ip_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_Netlink_ip_01/2023-08-08-13_47_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_Netlink_ip_01/2023-08-08-13_47_05.log) |
|  | oe_test_Numad_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_Numad_01/2023-08-08-13_49_38.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_Numad_01/2023-08-08-13_49_38.log) |
|  | oe_test_acl_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_acl_001/2023-08-08-09_49_53.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_acl_001/2023-08-08-09_49_53.log) |
|  | oe_test_acpid | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_acpid/2023-08-08-11_03_01.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_acpid/2023-08-08-11_03_01.log) |
|  | oe_test_aide_basic_info | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_basic_info/2023-08-08-13_28_31.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_basic_info/2023-08-08-13_28_31.log) |
|  | oe_test_aide_compare_database | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_compare_database/2023-08-08-11_03_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_compare_database/2023-08-08-11_03_47.log) |
|  | oe_test_aide_config_cfgfile | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_config_cfgfile/2023-08-08-13_08_40.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_config_cfgfile/2023-08-08-13_08_40.log) |
|  | oe_test_aide_config_check | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_config_check/2023-08-08-13_22_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_config_check/2023-08-08-13_22_04.log) |
|  | oe_test_aide_security | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_security/2023-08-08-13_35_39.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_security/2023-08-08-13_35_39.log) |
|  | oe_test_aide_update_database | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_update_database/2023-08-08-11_14_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_aide_update_database/2023-08-08-11_14_26.log) |
|  | oe_test_atd | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_atd/2023-08-08-11_37_21.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_atd/2023-08-08-11_37_21.log) |
|  | oe_test_attr_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_attr_001/2023-08-08-12_58_22.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_attr_001/2023-08-08-12_58_22.log) |
|  | oe_test_audit_abnormal_kill | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_abnormal_kill/2023-08-08-13_42_03.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_abnormal_kill/2023-08-08-13_42_03.log) |
|  | oe_test_audit_fixed_memory_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_fixed_memory_01/2023-08-08-13_42_51.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_audit_fixed_memory_01/2023-08-08-13_42_51.log) |
|  | oe_test_augeas_augtool | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_augeas_augtool/2023-08-08-11_39_10.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_augeas_augtool/2023-08-08-11_39_10.log) |
|  | oe_test_basic_ssh_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_basic_ssh_001/2023-08-08-09_50_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_basic_ssh_001/2023-08-08-09_50_05.log) |
|  | oe_test_bc | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bc/2023-08-08-11_40_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bc/2023-08-08-11_40_16.log) |
|  | oe_test_bridge-utils_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bridge-utils_01/2023-08-08-11_40_32.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_bridge-utils_01/2023-08-08-11_40_32.log) |
|  | oe_test_cd_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cd_002/2023-08-08-09_50_32.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cd_002/2023-08-08-09_50_32.log) |
|  | oe_test_chattr | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chattr/2023-08-08-09_50_43.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chattr/2023-08-08-09_50_43.log) |
|  | oe_test_chkconfig | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chkconfig/2023-08-08-11_41_15.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chkconfig/2023-08-08-11_41_15.log) |
|  | oe_test_chkconfig_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chkconfig_01/2023-08-08-13_58_51.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chkconfig_01/2023-08-08-13_58_51.log) |
|  | oe_test_chmod_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chmod_001/2023-08-08-09_50_54.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chmod_001/2023-08-08-09_50_54.log) |
|  | oe_test_chown_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chown_001/2023-08-08-09_51_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chown_001/2023-08-08-09_51_05.log) |
|  | oe_test_chroncy_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chroncy_001/2023-08-08-10_55_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chroncy_001/2023-08-08-10_55_25.log) |
|  | oe_test_chrt | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chrt/2023-08-08-11_41_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_chrt/2023-08-08-11_41_26.log) |
|  | oe_test_cifsclient | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cifsclient/2023-08-08-11_41_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cifsclient/2023-08-08-11_41_34.log) |
|  | oe_test_cp_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cp_001/2023-08-08-09_51_17.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_cp_001/2023-08-08-09_51_17.log) |
|  | oe_test_curl_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_01/2023-08-08-11_42_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_01/2023-08-08-11_42_47.log) |
|  | oe_test_curl_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_02/2023-08-08-11_43_40.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_02/2023-08-08-11_43_40.log) |
|  | oe_test_curl_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_03/2023-08-08-11_44_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_curl_03/2023-08-08-11_44_55.log) |
|  | oe_test_dateinfo_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dateinfo_001/2023-08-08-10_22_22.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dateinfo_001/2023-08-08-10_22_22.log) |
|  | oe_test_dbus | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus/2023-08-08-11_46_23.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus/2023-08-08-11_46_23.log) |
|  | oe_test_dbus-binging-tool | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-binging-tool/2023-08-08-11_45_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-binging-tool/2023-08-08-11_45_04.log) |
|  | oe_test_dbus-cleanup-sockets | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-cleanup-sockets/2023-08-08-11_45_49.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-cleanup-sockets/2023-08-08-11_45_49.log) |
|  | oe_test_dbus-daemon | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-daemon/2023-08-08-11_45_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-daemon/2023-08-08-11_45_56.log) |
|  | oe_test_dbus-monitor | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-monitor/2023-08-08-11_46_04.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-monitor/2023-08-08-11_46_04.log) |
|  | oe_test_dbus-send | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-send/2023-08-08-11_46_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-send/2023-08-08-11_46_16.log) |
|  | oe_test_dbus-uuidgen | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-uuidgen/2023-08-08-11_46_33.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dbus-uuidgen/2023-08-08-11_46_33.log) |
|  | oe_test_dd_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dd_001/2023-08-08-10_22_32.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dd_001/2023-08-08-10_22_32.log) |
|  | oe_test_df | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_df/2023-08-08-10_23_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_df/2023-08-08-10_23_05.log) |
|  | oe_test_dhclient_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dhclient_01/2023-08-08-11_46_41.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dhclient_01/2023-08-08-11_46_41.log) |
|  | oe_test_diffstat_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_diffstat_001/2023-08-08-10_56_22.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_diffstat_001/2023-08-08-10_56_22.log) |
|  | oe_test_dracut | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dracut/2023-08-08-11_46_59.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_dracut/2023-08-08-11_46_59.log) |
|  | oe_test_expat | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_expat/2023-08-08-12_58_06.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_expat/2023-08-08-12_58_06.log) |
|  | oe_test_file_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_file_001/2023-08-08-10_23_14.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_file_001/2023-08-08-10_23_14.log) |
|  | oe_test_find | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_find/2023-08-08-13_05_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_find/2023-08-08-13_05_13.log) |
|  | oe_test_gcc | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc/2023-08-08-10_59_17.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc/2023-08-08-10_59_17.log) |
|  | oe_test_gcc_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc_002/2023-08-08-14_02_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gcc_002/2023-08-08-14_02_16.log) |
|  | oe_test_glibc_getaddrinfo_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_glibc_getaddrinfo_02/2023-08-08-11_51_51.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_glibc_getaddrinfo_02/2023-08-08-11_51_51.log) |
|  | oe_test_golang | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_golang/2023-08-08-10_23_28.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_golang/2023-08-08-10_23_28.log) |
|  | oe_test_grep_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_grep_001/2023-08-08-10_27_42.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_grep_001/2023-08-08-10_27_42.log) |
|  | oe_test_group_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_group_001/2023-08-08-10_27_53.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_group_001/2023-08-08-10_27_53.log) |
|  | oe_test_group_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_group_002/2023-08-08-10_28_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_group_002/2023-08-08-10_28_05.log) |
|  | oe_test_gzip | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gzip/2023-08-08-11_52_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_gzip/2023-08-08-11_52_19.log) |
|  | oe_test_haproxy | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_haproxy/2023-08-08-10_28_18.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_haproxy/2023-08-08-10_28_18.log) |
|  | oe_test_httpd | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_httpd/2023-08-08-10_30_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_httpd/2023-08-08-10_30_19.log) |
|  | oe_test_hwdata | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_hwdata/2023-08-08-11_52_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_hwdata/2023-08-08-11_52_47.log) |
|  | oe_test_iproute | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iproute/2023-08-08-11_54_39.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_iproute/2023-08-08-11_54_39.log) |
|  | oe_test_ipv6_BRIDGE | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_BRIDGE/2023-08-08-11_55_42.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_BRIDGE/2023-08-08-11_55_42.log) |
|  | oe_test_ipv6_sysctl_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_sysctl_01/2023-08-08-13_45_02.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_sysctl_01/2023-08-08-13_45_02.log) |
|  | oe_test_ipv6_sysctl_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_sysctl_02/2023-08-08-13_45_10.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_sysctl_02/2023-08-08-13_45_10.log) |
|  | oe_test_journalctl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_journalctl/2023-08-08-10_33_36.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_journalctl/2023-08-08-10_33_36.log) |
|  | oe_test_krb5 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_krb5/2023-08-08-10_33_46.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_krb5/2023-08-08-10_33_46.log) |
|  | oe_test_less | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_less/2023-08-08-12_03_46.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_less/2023-08-08-12_03_46.log) |
|  | oe_test_less_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_less_001/2023-08-08-10_35_02.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_less_001/2023-08-08-10_35_02.log) |
|  | oe_test_let | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_let/2023-08-08-13_08_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_let/2023-08-08-13_08_25.log) |
|  | oe_test_libcgroup_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_01/2023-08-08-12_03_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_01/2023-08-08-12_03_55.log) |
|  | oe_test_libcgroup_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_02/2023-08-08-12_04_40.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_02/2023-08-08-12_04_40.log) |
|  | oe_test_libcgroup_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_03/2023-08-08-12_05_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libcgroup_03/2023-08-08-12_05_26.log) |
|  | oe_test_libuser_sm | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libuser_sm/2023-08-08-12_07_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_libuser_sm/2023-08-08-12_07_05.log) |
|  | oe_test_lldpad | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lldpad/2023-08-08-11_59_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lldpad/2023-08-08-11_59_34.log) |
|  | oe_test_ln_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ln_001/2023-08-08-10_34_50.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ln_001/2023-08-08-10_34_50.log) |
|  | oe_test_localectl_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_localectl_001/2023-08-08-10_35_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_localectl_001/2023-08-08-10_35_13.log) |
|  | oe_test_logrotate_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_001/2023-08-08-10_36_06.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_001/2023-08-08-10_36_06.log) |
|  | oe_test_logrotate_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_002/2023-08-08-10_35_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_002/2023-08-08-10_35_25.log) |
|  | oe_test_logrotate_003 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_003/2023-08-08-10_36_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_logrotate_003/2023-08-08-10_36_26.log) |
|  | oe_test_losetup | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_losetup/2023-08-08-12_07_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_losetup/2023-08-08-12_07_19.log) |
|  | oe_test_ls_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ls_001/2023-08-08-10_36_49.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ls_001/2023-08-08-10_36_49.log) |
|  | oe_test_lsof | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lsof/2023-08-08-12_07_31.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lsof/2023-08-08-12_07_31.log) |
|  | oe_test_lsscsi | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lsscsi/2023-08-08-10_37_01.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lsscsi/2023-08-08-10_37_01.log) |
|  | oe_test_lz4_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lz4_01/2023-08-08-12_08_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lz4_01/2023-08-08-12_08_19.log) |
|  | oe_test_lz4_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lz4_02/2023-08-08-12_08_33.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lz4_02/2023-08-08-12_08_33.log) |
|  | oe_test_lzo | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lzo/2023-08-08-10_37_53.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_lzo/2023-08-08-10_37_53.log) |
|  | oe_test_maildrop_delivery_agent | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_maildrop_delivery_agent/2023-08-08-12_08_42.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_maildrop_delivery_agent/2023-08-08-12_08_42.log) |
|  | oe_test_mkdir_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mkdir_001/2023-08-08-10_39_46.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mkdir_001/2023-08-08-10_39_46.log) |
|  | oe_test_mktemp | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mktemp/2023-08-08-12_09_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mktemp/2023-08-08-12_09_25.log) |
|  | oe_test_mpstat_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mpstat_01/2023-08-08-13_45_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mpstat_01/2023-08-08-13_45_26.log) |
|  | oe_test_mtr_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mtr_01/2023-08-08-13_46_12.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mtr_01/2023-08-08-13_46_12.log) |
|  | oe_test_mv_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mv_001/2023-08-08-10_39_59.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mv_001/2023-08-08-10_39_59.log) |
|  | oe_test_mv_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mv_002/2023-08-08-10_54_59.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_mv_002/2023-08-08-10_54_59.log) |
|  | oe_test_ndctl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ndctl/2023-08-08-10_40_11.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ndctl/2023-08-08-10_40_11.log) |
|  | oe_test_netstat_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_netstat_01/2023-08-08-13_47_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_netstat_01/2023-08-08-13_47_47.log) |
|  | oe_test_nfs | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs/2023-08-08-10_41_36.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs/2023-08-08-10_41_36.log) |
|  | oe_test_nfs_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_01/2023-08-08-12_09_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_01/2023-08-08-12_09_48.log) |
|  | oe_test_nfs_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_02/2023-08-08-12_10_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_02/2023-08-08-12_10_47.log) |
|  | oe_test_nfs_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_03/2023-08-08-12_11_53.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nfs_03/2023-08-08-12_11_53.log) |
|  | oe_test_nmap | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nmap/2023-08-08-12_13_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_nmap/2023-08-08-12_13_44.log) |
|  | oe_test_ntp_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ntp_01/2023-08-08-12_15_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ntp_01/2023-08-08-12_15_44.log) |
|  | oe_test_ntp_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ntp_02/2023-08-08-12_16_36.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_ntp_02/2023-08-08-12_16_36.log) |
|  | oe_test_od | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_od/2023-08-08-14_02_38.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_od/2023-08-08-14_02_38.log) |
|  | oe_test_openssl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_openssl/2023-08-08-10_43_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_openssl/2023-08-08-10_43_48.log) |
|  | oe_test_pciutils | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pciutils/2023-08-08-10_44_14.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pciutils/2023-08-08-10_44_14.log) |
|  | oe_test_pcre_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pcre_001/2023-08-08-12_12_58.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pcre_001/2023-08-08-12_12_58.log) |
|  | oe_test_perl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perl/2023-08-08-12_18_12.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_perl/2023-08-08-12_18_12.log) |
|  | oe_test_popt | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_popt/2023-08-08-13_58_10.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_popt/2023-08-08-13_58_10.log) |
|  | oe_test_procps-ng-sysctl | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_procps-ng-sysctl/2023-08-08-12_58_45.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_procps-ng-sysctl/2023-08-08-12_58_45.log) |
|  | oe_test_procps-ng-uptime | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_procps-ng-uptime/2023-08-08-12_58_38.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_procps-ng-uptime/2023-08-08-12_58_38.log) |
|  | oe_test_pstree | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pstree/2023-08-08-13_08_09.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pstree/2023-08-08-13_08_09.log) |
|  | oe_test_pwd_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pwd_002/2023-08-08-13_05_37.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_pwd_002/2023-08-08-13_05_37.log) |
|  | oe_test_python_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_01/2023-08-08-12_18_21.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_01/2023-08-08-12_18_21.log) |
|  | oe_test_python_pip_install | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_pip_install/2023-08-08-12_18_31.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_pip_install/2023-08-08-12_18_31.log) |
|  | oe_test_python_subprocess_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_01/2023-08-08-12_21_32.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_01/2023-08-08-12_21_32.log) |
|  | oe_test_python_subprocess_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_02/2023-08-08-12_21_40.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_02/2023-08-08-12_21_40.log) |
|  | oe_test_python_subprocess_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_03/2023-08-08-12_21_49.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_subprocess_03/2023-08-08-12_21_49.log) |
|  | oe_test_python_urllib3_urlopen_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_01/2023-08-08-12_22_43.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_01/2023-08-08-12_22_43.log) |
|  | oe_test_python_urllib3_urlopen_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_02/2023-08-08-12_23_43.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_02/2023-08-08-12_23_43.log) |
|  | oe_test_python_urllib3_urlopen_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_03/2023-08-08-12_24_14.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_python_urllib3_urlopen_03/2023-08-08-12_24_14.log) |
|  | oe_test_rm_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rm_001/2023-08-08-10_54_00.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rm_001/2023-08-08-10_54_00.log) |
|  | oe_test_rmdir_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rmdir_001/2023-08-08-10_46_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rmdir_001/2023-08-08-10_46_16.log) |
|  | oe_test_route_ipv4 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_route_ipv4/2023-08-08-12_24_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_route_ipv4/2023-08-08-12_24_25.log) |
|  | oe_test_rpcbind | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpcbind/2023-08-08-12_25_32.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpcbind/2023-08-08-12_25_32.log) |
|  | oe_test_rpmrebuild | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpmrebuild/2023-08-08-12_26_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rpmrebuild/2023-08-08-12_26_44.log) |
|  | oe_test_rsync | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsync/2023-08-08-10_46_26.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_rsync/2023-08-08-10_46_26.log) |
|  | oe_test_sadf_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sadf_001/2023-08-08-13_07_11.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sadf_001/2023-08-08-13_07_11.log) |
|  | oe_test_sar_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sar_01/2023-08-08-13_51_08.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sar_01/2023-08-08-13_51_08.log) |
|  | oe_test_sed_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sed_001/2023-08-08-13_57_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sed_001/2023-08-08-13_57_34.log) |
|  | oe_test_shadow | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_shadow/2023-08-08-12_50_52.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_shadow/2023-08-08-12_50_52.log) |
|  | oe_test_source | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_source/2023-08-08-13_05_25.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_source/2023-08-08-13_05_25.log) |
|  | oe_test_sshd | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sshd/2023-08-08-12_51_05.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sshd/2023-08-08-12_51_05.log) |
|  | oe_test_sudo | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo/2023-08-08-12_52_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo/2023-08-08-12_52_48.log) |
|  | oe_test_sudo_E | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_E/2023-08-08-12_51_21.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_E/2023-08-08-12_51_21.log) |
|  | oe_test_sudo_U | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_U/2023-08-08-12_54_53.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_U/2023-08-08-12_54_53.log) |
|  | oe_test_sudo_maxseq | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_maxseq/2023-08-08-12_51_31.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_sudo_maxseq/2023-08-08-12_51_31.log) |
|  | oe_test_syslog_dmesg_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_dmesg_001/2023-08-08-10_50_37.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_dmesg_001/2023-08-08-10_50_37.log) |
|  | oe_test_syslog_journalctl_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journalctl_001/2023-08-08-10_50_46.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journalctl_001/2023-08-08-10_50_46.log) |
|  | oe_test_syslog_journalctl_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journalctl_002/2023-08-08-10_50_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journalctl_002/2023-08-08-10_50_55.log) |
|  | oe_test_syslog_journald_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journald_001/2023-08-08-10_51_06.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_syslog_journald_001/2023-08-08-10_51_06.log) |
|  | oe_test_systemd-update-utmp | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd-update-utmp/2023-08-08-12_56_13.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd-update-utmp/2023-08-08-12_56_13.log) |
|  | oe_test_systemd_SCEN_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_01/2023-08-08-13_01_27.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_01/2023-08-08-13_01_27.log) |
|  | oe_test_systemd_SCEN_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_02/2023-08-08-13_01_39.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_02/2023-08-08-13_01_39.log) |
|  | oe_test_systemd_SCEN_03 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_03/2023-08-08-13_01_52.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_03/2023-08-08-13_01_52.log) |
|  | oe_test_systemd_SCEN_04 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_04/2023-08-08-13_02_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_04/2023-08-08-13_02_16.log) |
|  | oe_test_systemd_SCEN_05 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_05/2023-08-08-13_02_28.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_05/2023-08-08-13_02_28.log) |
|  | oe_test_systemd_SCEN_06 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_06/2023-08-08-13_02_39.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_06/2023-08-08-13_02_39.log) |
|  | oe_test_systemd_SCEN_07 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_07/2023-08-08-13_02_54.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_07/2023-08-08-13_02_54.log) |
|  | oe_test_systemd_SCEN_08 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_08/2023-08-08-13_03_11.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_08/2023-08-08-13_03_11.log) |
|  | oe_test_systemd_SCEN_09 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_09/2023-08-08-13_03_30.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_09/2023-08-08-13_03_30.log) |
|  | oe_test_systemd_SCEN_10 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_10/2023-08-08-13_03_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_10/2023-08-08-13_03_48.log) |
|  | oe_test_systemd_SCEN_11 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_11/2023-08-08-13_04_07.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_11/2023-08-08-13_04_07.log) |
|  | oe_test_systemd_SCEN_12 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_12/2023-08-08-13_04_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_12/2023-08-08-13_04_19.log) |
|  | oe_test_systemd_SCEN_13 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_13/2023-08-08-13_04_33.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_13/2023-08-08-13_04_33.log) |
|  | oe_test_systemd_SCEN_14 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_14/2023-08-08-13_04_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_14/2023-08-08-13_04_47.log) |
|  | oe_test_systemd_SCEN_15 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_15/2023-08-08-13_05_00.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_systemd_SCEN_15/2023-08-08-13_05_00.log) |
|  | oe_test_tar_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tar_001/2023-08-08-10_51_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tar_001/2023-08-08-10_51_34.log) |
|  | oe_test_tcpdump | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tcpdump/2023-08-08-12_56_20.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tcpdump/2023-08-08-12_56_20.log) |
|  | oe_test_time_01 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_time_01/2023-08-08-12_59_07.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_time_01/2023-08-08-12_59_07.log) |
|  | oe_test_time_02 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_time_02/2023-08-08-12_59_48.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_time_02/2023-08-08-12_59_48.log) |
|  | oe_test_touch_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_touch_001/2023-08-08-10_51_55.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_touch_001/2023-08-08-10_51_55.log) |
|  | oe_test_tr | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tr/2023-08-08-13_58_01.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_tr/2023-08-08-13_58_01.log) |
|  | oe_test_umask_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_umask_001/2023-08-08-10_53_08.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_umask_001/2023-08-08-10_53_08.log) |
|  | oe_test_umask_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_umask_002/2023-08-08-10_59_56.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_umask_002/2023-08-08-10_59_56.log) |
|  | oe_test_unalias_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_unalias_001/2023-08-08-12_58_52.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_unalias_001/2023-08-08-12_58_52.log) |
|  | oe_test_unset_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_unset_001/2023-08-08-13_08_33.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_unset_001/2023-08-08-13_08_33.log) |
|  | oe_test_user_001 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_001/2023-08-08-10_53_19.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_001/2023-08-08-10_53_19.log) |
|  | oe_test_user_002 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_002/2023-08-08-10_53_34.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_002/2023-08-08-10_53_34.log) |
|  | oe_test_user_003 | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_003/2023-08-08-10_53_47.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_user_003/2023-08-08-10_53_47.log) |
|  | oe_test_vmstat | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_vmstat/2023-08-08-13_08_18.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_vmstat/2023-08-08-13_08_18.log) |
|  | oe_test_wc | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_wc/2023-08-08-10_55_16.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_wc/2023-08-08-10_55_16.log) |
|  | oe_test_wget | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_wget/2023-08-08-13_00_27.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_wget/2023-08-08-13_00_27.log) |
|  | oe_test_which | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_which/2023-08-08-10_54_10.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_which/2023-08-08-10_54_10.log) |
|  | oe_test_xz | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_xz/2023-08-08-13_57_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_xz/2023-08-08-13_57_44.log) |
|  | oe_test_xz | success | [./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_xz/2023-08-08-13_57_44.log](./oe-rv2309/mugen-riscv/logs/smoke-basic-os/oe_test_xz/2023-08-08-13_57_44.log) |
| spice-vdagent | oe_test_service_spice-vdagentd | success | [./oe-rv2309/mugen-riscv/logs/spice-vdagent/oe_test_service_spice-vdagentd/2023-08-08-11_17_04.log](./oe-rv2309/mugen-riscv/logs/spice-vdagent/oe_test_service_spice-vdagentd/2023-08-08-11_17_04.log) |
|  | oe_test_socket_spice-vdagentd | success | [./oe-rv2309/mugen-riscv/logs/spice-vdagent/oe_test_socket_spice-vdagentd/2023-08-08-11_18_18.log](./oe-rv2309/mugen-riscv/logs/spice-vdagent/oe_test_socket_spice-vdagentd/2023-08-08-11_18_18.log) |
| sqlite | oe_test_sqlite_alter_01 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_alter_01/2023-08-12-19_30_22.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_alter_01/2023-08-12-19_30_22.log) |
|  | oe_test_sqlite_command_01 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_01/2023-08-12-19_30_46.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_01/2023-08-12-19_30_46.log) |
|  | oe_test_sqlite_command_02 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_02/2023-08-12-19_31_31.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_02/2023-08-12-19_31_31.log) |
|  | oe_test_sqlite_command_03 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_03/2023-08-12-19_32_03.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_03/2023-08-12-19_32_03.log) |
|  | oe_test_sqlite_command_04 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_04/2023-08-12-19_32_25.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_04/2023-08-12-19_32_25.log) |
|  | oe_test_sqlite_command_05 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_05/2023-08-12-19_32_47.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_05/2023-08-12-19_32_47.log) |
|  | oe_test_sqlite_command_06 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_06/2023-08-12-19_33_09.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_06/2023-08-12-19_33_09.log) |
|  | oe_test_sqlite_command_07 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_07/2023-08-12-19_33_42.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_07/2023-08-12-19_33_42.log) |
|  | oe_test_sqlite_command_08 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_08/2023-08-12-19_34_05.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_command_08/2023-08-12-19_34_05.log) |
|  | oe_test_sqlite_create_06 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_create_06/2023-08-12-19_37_08.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_create_06/2023-08-12-19_37_08.log) |
|  | oe_test_sqlite_delete_01 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_delete_01/2023-08-12-19_34_29.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_delete_01/2023-08-12-19_34_29.log) |
|  | oe_test_sqlite_function_01 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_01/2023-08-12-19_34_52.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_01/2023-08-12-19_34_52.log) |
|  | oe_test_sqlite_function_02 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_02/2023-08-12-19_35_15.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_02/2023-08-12-19_35_15.log) |
|  | oe_test_sqlite_function_03 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_03/2023-08-12-19_35_37.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_03/2023-08-12-19_35_37.log) |
|  | oe_test_sqlite_function_04 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_04/2023-08-12-19_36_02.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_function_04/2023-08-12-19_36_02.log) |
|  | oe_test_sqlite_select_05 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_select_05/2023-08-12-19_36_47.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_select_05/2023-08-12-19_36_47.log) |
|  | oe_test_sqlite_update_01 | success | [./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_update_01/2023-08-12-19_36_24.log](./oe-rv2309/mugen-riscv/logs/sqlite/oe_test_sqlite_update_01/2023-08-12-19_36_24.log) |
| squid | oe_test_service_squid | success | [./oe-rv2309/mugen-riscv/logs/squid/oe_test_service_squid/2023-08-09-05_12_14.log](./oe-rv2309/mugen-riscv/logs/squid/oe_test_service_squid/2023-08-09-05_12_14.log) |
| sssd | oe_test_service_sssd | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd/2023-08-08-11_22_23.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd/2023-08-08-11_22_23.log) |
|  | oe_test_service_sssd-autofs | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-autofs/2023-08-08-10_53_35.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-autofs/2023-08-08-10_53_35.log) |
|  | oe_test_service_sssd-ifp | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-ifp/2023-08-08-10_56_53.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-ifp/2023-08-08-10_56_53.log) |
|  | oe_test_service_sssd-kcm | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-kcm/2023-08-08-11_01_06.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-kcm/2023-08-08-11_01_06.log) |
|  | oe_test_service_sssd-nss | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-nss/2023-08-08-11_08_28.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-nss/2023-08-08-11_08_28.log) |
|  | oe_test_service_sssd-pac | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-pac/2023-08-08-11_15_02.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-pac/2023-08-08-11_15_02.log) |
|  | oe_test_service_sssd-pam | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-pam/2023-08-08-11_18_54.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-pam/2023-08-08-11_18_54.log) |
|  | oe_test_service_sssd-ssh | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-ssh/2023-08-08-11_27_52.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-ssh/2023-08-08-11_27_52.log) |
|  | oe_test_service_sssd-sudo | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-sudo/2023-08-08-11_34_29.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_service_sssd-sudo/2023-08-08-11_34_29.log) |
|  | oe_test_socket_sssd-autofs | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-autofs/2023-08-08-11_41_12.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-autofs/2023-08-08-11_41_12.log) |
|  | oe_test_socket_sssd-kcm | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-kcm/2023-08-08-11_49_14.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-kcm/2023-08-08-11_49_14.log) |
|  | oe_test_socket_sssd-nss | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-nss/2023-08-08-11_57_00.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-nss/2023-08-08-11_57_00.log) |
|  | oe_test_socket_sssd-pac | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pac/2023-08-08-12_03_40.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pac/2023-08-08-12_03_40.log) |
|  | oe_test_socket_sssd-pam | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pam/2023-08-08-12_18_45.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pam/2023-08-08-12_18_45.log) |
|  | oe_test_socket_sssd-pam-priv | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pam-priv/2023-08-08-12_11_54.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-pam-priv/2023-08-08-12_11_54.log) |
|  | oe_test_socket_sssd-ssh | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-ssh/2023-08-08-12_27_40.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-ssh/2023-08-08-12_27_40.log) |
|  | oe_test_socket_sssd-sudo | success | [./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-sudo/2023-08-08-12_36_33.log](./oe-rv2309/mugen-riscv/logs/sssd/oe_test_socket_sssd-sudo/2023-08-08-12_36_33.log) |
| strongswan | oe_test_service_strongswan_02 | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_02/2023-08-12-20_22_34.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_strongswan_02/2023-08-12-20_22_34.log) |
|  | oe_test_service_swanctl_01 | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_swanctl_01/2023-08-12-20_26_52.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_swanctl_01/2023-08-12-20_26_52.log) |
|  | oe_test_service_swanctl_02 | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_swanctl_02/2023-08-12-20_28_47.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_swanctl_02/2023-08-12-20_28_47.log) |
|  | oe_test_service_charon-cmd_01 | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-cmd_01/2023-08-12-20_30_45.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-cmd_01/2023-08-12-20_30_45.log) |
|  | oe_test_service_charon-cmd_02 | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-cmd_02/2023-08-12-20_32_25.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-cmd_02/2023-08-12-20_32_25.log) |
|  | oe_test_service_charon-systemd | success | [./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-systemd/2023-08-12-20_26_12.log](./oe-rv2309/mugen-riscv/logs/strongswan/oe_test_service_charon-systemd/2023-08-12-20_26_12.log) |
| sudo | oe_test_sudo_cvtsudoers | success | [./oe-rv2309/mugen-riscv/logs/sudo/oe_test_sudo_cvtsudoers/2023-08-08-12_00_58.log](./oe-rv2309/mugen-riscv/logs/sudo/oe_test_sudo_cvtsudoers/2023-08-08-12_00_58.log) |
| switcheroo-control | oe_test_service_switcheroo-control | success | [./oe-rv2309/mugen-riscv/logs/switcheroo-control/oe_test_service_switcheroo-control/2023-08-08-17_22_15.log](./oe-rv2309/mugen-riscv/logs/switcheroo-control/oe_test_service_switcheroo-control/2023-08-08-17_22_15.log) |
| sysprof | oe_test_service_sysprof2 | success | [./oe-rv2309/mugen-riscv/logs/sysprof/oe_test_service_sysprof2/2023-08-13-02_25_45.log](./oe-rv2309/mugen-riscv/logs/sysprof/oe_test_service_sysprof2/2023-08-13-02_25_45.log) |
|  | oe_test_service_sysprof3 | success | [./oe-rv2309/mugen-riscv/logs/sysprof/oe_test_service_sysprof3/2023-08-13-02_30_54.log](./oe-rv2309/mugen-riscv/logs/sysprof/oe_test_service_sysprof3/2023-08-13-02_30_54.log) |
| sysstat | oe_test_service_sysstat | success | [./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat/2023-08-08-14_42_33.log](./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat/2023-08-08-14_42_33.log) |
|  | oe_test_service_sysstat-collect | success | [./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat-collect/2023-08-08-14_35_47.log](./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat-collect/2023-08-08-14_35_47.log) |
|  | oe_test_service_sysstat-summary | success | [./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat-summary/2023-08-08-14_50_07.log](./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_service_sysstat-summary/2023-08-08-14_50_07.log) |
|  | oe_test_sysstat_iostat | success | [./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_sysstat_iostat/2023-08-08-14_57_15.log](./oe-rv2309/mugen-riscv/logs/sysstat/oe_test_sysstat_iostat/2023-08-08-14_57_15.log) |
| systemd | oe_test_service_initrd-switch-root | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-switch-root/2023-08-08-00_10_40.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-switch-root/2023-08-08-00_10_40.log) |
|  | oe_test_service_systemd-fsck-root | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-fsck-root/2023-08-08-00_17_14.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-fsck-root/2023-08-08-00_17_14.log) |
|  | oe_test_service_systemd-network-generator | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-network-generator/2023-08-08-00_28_08.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-network-generator/2023-08-08-00_28_08.log) |
|  | oe_test_target_initrd-switch-root | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-switch-root/2023-08-08-00_55_21.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd-switch-root/2023-08-08-00_55_21.log) |
|  | oe_test_service_dbus-org.freedesktop.hostname1 | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.hostname1/2023-08-08-00_04_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_dbus-org.freedesktop.hostname1/2023-08-08-00_04_34.log) |
|  | oe_test_service_emergency | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_emergency/2023-08-08-01_10_39.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_emergency/2023-08-08-01_10_39.log) |
|  | oe_test_service_initrd-cleanup | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-cleanup/2023-08-08-00_10_20.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-cleanup/2023-08-08-00_10_20.log) |
|  | oe_test_service_initrd-parse-etc | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-parse-etc/2023-08-08-00_10_30.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-parse-etc/2023-08-08-00_10_30.log) |
|  | oe_test_service_initrd-udevadm-cleanup-db | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-udevadm-cleanup-db/2023-08-08-00_10_48.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_initrd-udevadm-cleanup-db/2023-08-08-00_10_48.log) |
|  | oe_test_service_systemd-binfmt | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-binfmt/2023-08-08-00_15_25.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-binfmt/2023-08-08-00_15_25.log) |
|  | oe_test_service_systemd-bless-boot | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-bless-boot/2023-08-08-00_15_34.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-bless-boot/2023-08-08-00_15_34.log) |
|  | oe_test_service_systemd-boot-system-token | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-boot-system-token/2023-08-08-00_16_47.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-boot-system-token/2023-08-08-00_16_47.log) |
|  | oe_test_service_systemd-exit | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-exit/2023-08-08-00_16_56.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-exit/2023-08-08-00_16_56.log) |
|  | oe_test_service_systemd-firstboot | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-firstboot/2023-08-08-00_17_05.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-firstboot/2023-08-08-00_17_05.log) |
|  | oe_test_service_systemd-halt | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-halt/2023-08-08-00_17_23.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-halt/2023-08-08-00_17_23.log) |
|  | oe_test_service_systemd-hibernate | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hibernate/2023-08-08-00_17_32.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hibernate/2023-08-08-00_17_32.log) |
|  | oe_test_service_systemd-hybrid-sleep | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hybrid-sleep/2023-08-08-00_18_51.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-hybrid-sleep/2023-08-08-00_18_51.log) |
|  | oe_test_service_systemd-kexec | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-kexec/2023-08-08-00_24_44.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-kexec/2023-08-08-00_24_44.log) |
|  | oe_test_service_systemd-machine-id-commit | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-machine-id-commit/2023-08-08-00_26_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-machine-id-commit/2023-08-08-00_26_42.log) |
|  | oe_test_service_systemd-modules-load | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-modules-load/2023-08-08-00_26_51.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-modules-load/2023-08-08-00_26_51.log) |
|  | oe_test_service_systemd-poweroff | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-poweroff/2023-08-08-00_28_51.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-poweroff/2023-08-08-00_28_51.log) |
|  | oe_test_service_systemd-pstore | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-pstore/2023-08-08-00_29_00.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-pstore/2023-08-08-00_29_00.log) |
|  | oe_test_service_systemd-reboot | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-reboot/2023-08-08-00_30_16.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-reboot/2023-08-08-00_30_16.log) |
|  | oe_test_service_systemd-rfkill | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-rfkill/2023-08-08-00_31_35.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-rfkill/2023-08-08-00_31_35.log) |
|  | oe_test_service_systemd-suspend | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-suspend/2023-08-08-00_31_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-suspend/2023-08-08-00_31_50.log) |
|  | oe_test_service_systemd-suspend-then-hibernate | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-suspend-then-hibernate/2023-08-08-00_32_00.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-suspend-then-hibernate/2023-08-08-00_32_00.log) |
|  | oe_test_service_systemd-update-utmp-runlevel | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-utmp-runlevel/2023-08-08-00_37_51.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-update-utmp-runlevel/2023-08-08-00_37_51.log) |
|  | oe_test_service_systemd-volatile-root | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-volatile-root/2023-08-08-00_39_42.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_service_systemd-volatile-root/2023-08-08-00_39_42.log) |
|  | oe_test_target_ctrl-alt-del | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_ctrl-alt-del/2023-08-08-00_50_07.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_ctrl-alt-del/2023-08-08-00_50_07.log) |
|  | oe_test_target_emergency | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_emergency/2023-08-08-01_10_48.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_emergency/2023-08-08-01_10_48.log) |
|  | oe_test_target_exit | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_exit/2023-08-08-00_50_50.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_exit/2023-08-08-00_50_50.log) |
|  | oe_test_target_final | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_final/2023-08-08-00_51_02.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_final/2023-08-08-00_51_02.log) |
|  | oe_test_target_halt | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_halt/2023-08-08-00_52_55.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_halt/2023-08-08-00_52_55.log) |
|  | oe_test_target_hibernate | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_hibernate/2023-08-08-00_53_06.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_hibernate/2023-08-08-00_53_06.log) |
|  | oe_test_target_hybrid-sleep | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_hybrid-sleep/2023-08-08-00_53_16.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_hybrid-sleep/2023-08-08-00_53_16.log) |
|  | oe_test_target_initrd | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd/2023-08-08-00_55_12.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_initrd/2023-08-08-00_55_12.log) |
|  | oe_test_target_kexec | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_kexec/2023-08-08-00_55_30.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_kexec/2023-08-08-00_55_30.log) |
|  | oe_test_target_poweroff | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_poweroff/2023-08-08-00_59_33.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_poweroff/2023-08-08-00_59_33.log) |
|  | oe_test_target_reboot | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_reboot/2023-08-08-00_59_45.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_reboot/2023-08-08-00_59_45.log) |
|  | oe_test_target_runlevel0 | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel0/2023-08-08-01_02_02.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel0/2023-08-08-01_02_02.log) |
|  | oe_test_target_runlevel6 | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel6/2023-08-08-01_05_03.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_runlevel6/2023-08-08-01_05_03.log) |
|  | oe_test_target_shutdown | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_shutdown/2023-08-08-01_05_13.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_shutdown/2023-08-08-01_05_13.log) |
|  | oe_test_target_sleep | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sleep/2023-08-08-01_05_57.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_sleep/2023-08-08-01_05_57.log) |
|  | oe_test_target_suspend | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_suspend/2023-08-08-01_07_15.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_suspend/2023-08-08-01_07_15.log) |
|  | oe_test_target_suspend-then-hibernate | success | [./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_suspend-then-hibernate/2023-08-08-01_07_25.log](./oe-rv2309/mugen-riscv/logs/systemd/oe_test_target_suspend-then-hibernate/2023-08-08-01_07_25.log) |
| systemtap | oe_test_service_systemtap | fail | [./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_systemtap/2023-08-09-04_31_20.log](./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_systemtap/2023-08-09-04_31_20.log) |
|  | oe_test_service_stap-exporter | success | [./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_stap-exporter/2023-08-09-04_25_07.log](./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_stap-exporter/2023-08-09-04_25_07.log) |
|  | oe_test_service_stap-server | success | [./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_stap-server/2023-08-09-04_26_53.log](./oe-rv2309/mugen-riscv/logs/systemtap/oe_test_service_stap-server/2023-08-09-04_26_53.log) |
| tang | oe_test_service_tangd-keygen | success | [./oe-rv2309/mugen-riscv/logs/tang/oe_test_service_tangd-keygen/2023-08-08-11_08_44.log](./oe-rv2309/mugen-riscv/logs/tang/oe_test_service_tangd-keygen/2023-08-08-11_08_44.log) |
|  | oe_test_service_tangd-update | success | [./oe-rv2309/mugen-riscv/logs/tang/oe_test_service_tangd-update/2023-08-08-11_09_42.log](./oe-rv2309/mugen-riscv/logs/tang/oe_test_service_tangd-update/2023-08-08-11_09_42.log) |
|  | oe_test_socket_tangd | success | [./oe-rv2309/mugen-riscv/logs/tang/oe_test_socket_tangd/2023-08-08-11_10_39.log](./oe-rv2309/mugen-riscv/logs/tang/oe_test_socket_tangd/2023-08-08-11_10_39.log) |
| telnet | oe_test_socket_telnet | success | [./oe-rv2309/mugen-riscv/logs/telnet/oe_test_socket_telnet/2023-08-08-12_13_54.log](./oe-rv2309/mugen-riscv/logs/telnet/oe_test_socket_telnet/2023-08-08-12_13_54.log) |
| tftp | oe_test_service_tftp | success | [./oe-rv2309/mugen-riscv/logs/tftp/oe_test_service_tftp/2023-08-09-04_46_40.log](./oe-rv2309/mugen-riscv/logs/tftp/oe_test_service_tftp/2023-08-09-04_46_40.log) |
|  | oe_test_socket_tftp | success | [./oe-rv2309/mugen-riscv/logs/tftp/oe_test_socket_tftp/2023-08-09-04_48_03.log](./oe-rv2309/mugen-riscv/logs/tftp/oe_test_socket_tftp/2023-08-09-04_48_03.log) |
| tigervnc | oe_test_socket_xvnc | success | [./oe-rv2309/mugen-riscv/logs/tigervnc/oe_test_socket_xvnc/2023-08-13-04_50_26.log](./oe-rv2309/mugen-riscv/logs/tigervnc/oe_test_socket_xvnc/2023-08-13-04_50_26.log) |
| tree | oe_test_tree | success | [./oe-rv2309/mugen-riscv/logs/tree/oe_test_tree/2023-08-08-12_40_29.log](./oe-rv2309/mugen-riscv/logs/tree/oe_test_tree/2023-08-08-12_40_29.log) |
| udisks2 | oe_test_service_udisks2 | success | [./oe-rv2309/mugen-riscv/logs/udisks2/oe_test_service_udisks2/2023-08-08-12_16_25.log](./oe-rv2309/mugen-riscv/logs/udisks2/oe_test_service_udisks2/2023-08-08-12_16_25.log) |
| unbound | oe_test_service_unbound | success | [./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound/2023-08-08-15_39_31.log](./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound/2023-08-08-15_39_31.log) |
|  | oe_test_service_unbound-anchor | success | [./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound-anchor/2023-08-08-15_29_06.log](./oe-rv2309/mugen-riscv/logs/unbound/oe_test_service_unbound-anchor/2023-08-08-15_29_06.log) |
| upower | oe_test_service_upower | success | [./oe-rv2309/mugen-riscv/logs/upower/oe_test_service_upower/2023-08-13-04_59_05.log](./oe-rv2309/mugen-riscv/logs/upower/oe_test_service_upower/2023-08-13-04_59_05.log) |
| usbmuxd | oe_test_service_usbmuxd | success | [./oe-rv2309/mugen-riscv/logs/usbmuxd/oe_test_service_usbmuxd/2023-08-09-05_14_41.log](./oe-rv2309/mugen-riscv/logs/usbmuxd/oe_test_service_usbmuxd/2023-08-09-05_14_41.log) |
| util-linux | oe_test_service_fstrim | success | [./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_service_fstrim/2023-08-08-10_49_25.log](./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_service_fstrim/2023-08-08-10_49_25.log) |
|  | oe_test_service_uuidd | success | [./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_service_uuidd/2023-08-08-10_49_38.log](./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_service_uuidd/2023-08-08-10_49_38.log) |
|  | oe_test_socket_uuidd | success | [./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_socket_uuidd/2023-08-08-10_50_44.log](./oe-rv2309/mugen-riscv/logs/util-linux/oe_test_socket_uuidd/2023-08-08-10_50_44.log) |
| vsftpd | oe_test_service_vsftpd | success | [./oe-rv2309/mugen-riscv/logs/vsftpd/oe_test_service_vsftpd/2023-08-08-11_20_11.log](./oe-rv2309/mugen-riscv/logs/vsftpd/oe_test_service_vsftpd/2023-08-08-11_20_11.log) |
|  | oe_test_target_vsftpd | success | [./oe-rv2309/mugen-riscv/logs/vsftpd/oe_test_target_vsftpd/2023-08-08-11_21_35.log](./oe-rv2309/mugen-riscv/logs/vsftpd/oe_test_target_vsftpd/2023-08-08-11_21_35.log) |
| wpa_supplicant | oe_test_service_wpa_supplicant | success | [./oe-rv2309/mugen-riscv/logs/wpa_supplicant/oe_test_service_wpa_supplicant/2023-08-08-12_35_34.log](./oe-rv2309/mugen-riscv/logs/wpa_supplicant/oe_test_service_wpa_supplicant/2023-08-08-12_35_34.log) |
| xdelta | oe_test_xdelta_001 | success | [./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_001/2023-08-13-00_03_37.log](./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_001/2023-08-13-00_03_37.log) |
|  | oe_test_xdelta_002 | success | [./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_002/2023-08-13-00_29_17.log](./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_002/2023-08-13-00_29_17.log) |
|  | oe_test_xdelta_003 | success | [./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_003/2023-08-13-00_32_26.log](./oe-rv2309/mugen-riscv/logs/xdelta/oe_test_xdelta_003/2023-08-13-00_32_26.log) |
| xfsprogs | oe_test_service_xfs_scrub_all | success | [./oe-rv2309/mugen-riscv/logs/xfsprogs/oe_test_service_xfs_scrub_all/2023-08-13-05_05_36.log](./oe-rv2309/mugen-riscv/logs/xfsprogs/oe_test_service_xfs_scrub_all/2023-08-13-05_05_36.log) |
| xinetd | oe_test_service_xinetd | success | [./oe-rv2309/mugen-riscv/logs/xinetd/oe_test_service_xinetd/2023-08-08-11_20_53.log](./oe-rv2309/mugen-riscv/logs/xinetd/oe_test_service_xinetd/2023-08-08-11_20_53.log) |
|  | oe_test_service_xinetd_002 | success | [./oe-rv2309/mugen-riscv/logs/xinetd/oe_test_service_xinetd_002/2023-08-08-11_22_05.log](./oe-rv2309/mugen-riscv/logs/xinetd/oe_test_service_xinetd_002/2023-08-08-11_22_05.log) |
| xz | oe_test_xz_unxz_01 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_01/2023-08-09-03_35_51.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_01/2023-08-09-03_35_51.log) |
|  | oe_test_xz_unxz_02 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_02/2023-08-09-03_36_17.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_02/2023-08-09-03_36_17.log) |
|  | oe_test_xz_unxz_03 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_03/2023-08-09-03_36_41.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_03/2023-08-09-03_36_41.log) |
|  | oe_test_xz_unxz_04 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_04/2023-08-09-03_37_06.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_unxz_04/2023-08-09-03_37_06.log) |
|  | oe_test_xz_xz_01 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_01/2023-08-09-03_34_05.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_01/2023-08-09-03_34_05.log) |
|  | oe_test_xz_xz_02 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_02/2023-08-09-03_34_33.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_02/2023-08-09-03_34_33.log) |
|  | oe_test_xz_xz_03 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_03/2023-08-09-03_34_59.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_03/2023-08-09-03_34_59.log) |
|  | oe_test_xz_xz_04 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_04/2023-08-09-03_35_25.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xz_04/2023-08-09-03_35_25.log) |
|  | oe_test_xz_xzcat_01 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_01/2023-08-09-03_37_31.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_01/2023-08-09-03_37_31.log) |
|  | oe_test_xz_xzcat_02 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_02/2023-08-09-03_37_56.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_02/2023-08-09-03_37_56.log) |
|  | oe_test_xz_xzcat_03 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_03/2023-08-09-03_38_22.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcat_03/2023-08-09-03_38_22.log) |
|  | oe_test_xz_xzcmp | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcmp/2023-08-09-03_38_49.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzcmp/2023-08-09-03_38_49.log) |
|  | oe_test_xz_xzdec | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdec/2023-08-09-03_39_15.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdec/2023-08-09-03_39_15.log) |
|  | oe_test_xz_xzdiff_01 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdiff_01/2023-08-09-03_39_41.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdiff_01/2023-08-09-03_39_41.log) |
|  | oe_test_xz_xzdiff_02 | success | [./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdiff_02/2023-08-09-03_40_34.log](./oe-rv2309/mugen-riscv/logs/xz/oe_test_xz_xzdiff_02/2023-08-09-03_40_34.log) |
| yajl | oe_test_yajl | fail | [./oe-rv2309/mugen-riscv/logs/yajl/oe_test_yajl/2023-08-09-05_16_44.log](./oe-rv2309/mugen-riscv/logs/yajl/oe_test_yajl/2023-08-09-05_16_44.log) |
| ypbind | oe_test_service_ypbind | success | [./oe-rv2309/mugen-riscv/logs/ypbind/oe_test_service_ypbind/2023-08-08-12_07_53.log](./oe-rv2309/mugen-riscv/logs/ypbind/oe_test_service_ypbind/2023-08-08-12_07_53.log) |
| ypserv | oe_test_service_yppasswdd | success | [./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_yppasswdd/2023-08-09-04_28_34.log](./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_yppasswdd/2023-08-09-04_28_34.log) |
|  | oe_test_service_ypserv | success | [./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_ypserv/2023-08-09-04_30_33.log](./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_ypserv/2023-08-09-04_30_33.log) |
|  | oe_test_service_ypxfrd | success | [./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_ypxfrd/2023-08-09-04_32_09.log](./oe-rv2309/mugen-riscv/logs/ypserv/oe_test_service_ypxfrd/2023-08-09-04_32_09.log) |
|



## 23,09 fail 失败原因排查汇总

我们将测试状态为 x86 fail 的用例大致分为三类：

1. 23.09 和 23.03 失败原因完全一样 [base_OS_same.csv](./base_OS_same.csv)
2. 23.09 和 23.03 失败原因不同 [base_OS_diff.csv](./base_OS_diff.csv)
3. 23.09 下失败，但在 23.03 下成功 [base_OS_09fail.csv](./base_OS_09fail.csv)

又根据失败的具体原因以及缺陷的重要程度，在下文给出分类和每种类型的测试用例举例。

### 目前在23.03与23.09下失败原因相同

#### 测试套 suite2cases 问题

+ [rsyslog/oe_test_rsyslog_abnormal_template.sh](https://gitee.com/openeuler/mugen/blob/master/testcases/cli-test/rsyslog/oe_test_rsyslog_abnormal_template/oe_test_rsyslog_abnormal_template.sh) 这一测试用例需要 NODE2 配置，即需要 2 个节点进行测试，但是 [rsyslog.json](https://gitee.com/openeuler/mugen/blob/master/suite2cases/rsyslog.json) 中没有 "machine num" 配置。这一问题在该测试套共影响 10 个测试用例

#### 测试套脚本问题

+ [os-basic/oe_test_system_log_recorded](./mugen-riscv/logs/os-basic/oe_test_system_log_recorded/2023-08-08-12_23_29.log) ``folder=$(ls /run/log/journal/)`` 若 ``ls`` 命令输出零个（riscv）或多个（x86 两个）就会出现问题，但是并没有进行处理
+ [kernel/oe_test_cifs.sh](https://gitee.com/openeuler/mugen/blob/master/testcases/cli-test/kernel/oe_test_cifs.sh) 测试脚本测试的是 cifs 模块，但是错误信息是 vport_geneve 模块；且最后一个测试也是测试的 vport_geneve 模块，和测试套目的不相符合
   ```
   function run_test() {
       modinfo cifs |grep cifs
       CHECK_RESULT $? 0 0 "Description Module information failed to be displayed"
       lsmod | grep  cifs
       CHECK_RESULT $? 0 1 "Default installation"
       modprobe cifs
       CHECK_RESULT $? 0 0 "Module loading failure"
       lsmod | grep  cifs
       CHECK_RESULT $? 0 0 "vport_geneve not found"
       rmmod  cifs
       CHECK_RESULT $? 0 0 "vport-geneve remove failure"
       lsmod | grep  cifs
       CHECK_RESULT $? 0 1 "vport_geneve exist"
       dmesg | grep "vport_geneve" | grep -Ei 'error|fail'
       CHECK_RESULT $? 1 0 "error message was reported"
   }
   ```
+ [kmod/oe_test_depmod](./mugen-riscv/logs/kmod/oe_test_depmod/2023-08-09-03_48_11.log) ``symversPath=$(find / -name Module.symvers)`` 和 ``mapPath=$(find / -name System.map)`` 两个命令，若 ``ls`` 命令输出多个（riscv 两个）就会出现问题，但是并没有进行处理
+ [initscripts/oe_test_service_network.sh](https://gitee.com/openeuler/mugen/blob/master/testcases/cli-test/initscripts/oe_test_service_network.sh#L37) 测试行命令拼写错误 ``journalct --since`` ，由于该测试命令返回非 0 为成功，这行测试将永远成功（测试成功但是脚本有问题）

#### 命令输出与 grep 预期不符

+ [os-basic/oe_test_dmraid](./mugen-riscv/logs/os-basic/oe_test_dmraid/2023-08-08-11_13_04.log) ``dmraid -s`` 输出为 ``no block devices found`` ，而测试脚本预期结果为 ``no raid disks`` ，导致没有判定出 raid 不存在
+ [os-basic/oe_test_whereis](./mugen-riscv/logs/os-basic/oe_test_whereis/2023-08-08-11_04_34.log) ``whereis --help | grep "Usage: whereis"`` 匹配失败， ``whereis --help`` 的实际输出为
   ```
   Usage:
    whereis [options] [-BMS <dir>... -f] <name>
   ```
+ [openssl/oe_test_openssl_DSA_algorithm](./mugen-riscv/logs/openssl/oe_test_openssl_DSA_algorithm/2023-08-12-15_05_50.log) ``grep 'BEGIN DSA PRIVATE KEY'`` 失败，实际应为 ``BEGIN PRIVATE KEY`` 。同样的错误影响了 11 个测试用例
+ [openssl/oe_test_openssl_speed](./mugen-riscv/logs/openssl/oe_test_openssl_speed/2023-08-12-15_39_54.log) ``grep "aes-128 cbc"`` 和 ``grep "sm4-cbc"`` 失败，实际应为 ``aes-128-cbc`` 和 ``SM4-CBC``
+ [iptables/oe_test_iptables-restore_01](./mugen-riscv/logs/iptables/oe_test_ip6tables-restore_01/2023-08-08-10_24_37.log) 和 [iptables/oe_test_iptables-restore](./mugen-riscv/logs/iptables/oe_test_iptables-restore/2023-08-08-10_23_06.log) ``grep "DROP       icmp"`` 失败， ``ip6tables -nvL`` 输出没有出现该字段
+ [iptables/oe_test_ip6tables-save](./mugen-riscv/logs/iptables/oe_test_ip6tables-save/2023-08-08-10_23_52.log) ``grep -A 200 nat | grep -A 100 mangle | grep -A 80 raw | grep -A 60 security | grep filter`` ， ``ip6tables-save`` 命令实际输出没有出现这些字段
+ [mc/oe_test_mc_base_01](./mugen-riscv/logs/mc/oe_test_mc_base_01/2023-08-08-10_33_01.log) ``grep 'Home路径'`` 尝试 grep 中文字串，实际输出为 ``Home directory``

#### 内核模块名称和预期不同

+ [kmod/oe_test_insmod-lsmod](./mugen-riscv/logs/kmod/oe_test_insmod-lsmod/2023-08-09-03_50_37.log) 在 23.09 riscv 的内核上， raid0 模块文件名为 ``raid0.ko.xz`` 、 faulty 模块文件名为 ``faulty.ko.xz`` ，而测试套预期文件名为 ``raid0.ko`` 和 ``faulty.ko`` ，导致测试失败
+ [kmod/oe_test_modprobe](./mugen-riscv/logs/kmod/oe_test_modprobe/2023-08-09-03_51_36.log) 在 23.09 riscv 的内核上 dm_log 模块的文件名为 ``dm-log.ko.xz`` ，而测试套预期文件名为 ``dm-log.ko`` ，导致测试失败

#### 依赖的命令没有安装也没有预装

+ [os-basic/oe_test_auditctl](./mugen-riscv/logs/os-basic/oe_test_auditctl/2023-08-08-11_30_21.log) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [os-basic/oe_test_server_openssh_verifykey](./mugen-riscv/logs/os-basic/oe_test_server_openssh_verifykey/2023-08-08-13_50_12.log) 依赖 policycoreutils 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [os-basic/oe_test_count_gperftools_function](./mugen-riscv/logs/os-basic/oe_test_count_gperftools_function/2023-08-08-23_46_34.log) 依赖 gperftools-devel 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [os-basic/oe_test_disk_graphics_card](./mugen-riscv/logs/os-basic/oe_test_disk_graphics_card/2023-08-08-22_17_55.log) 依赖 pciutils 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ 大部分 **audit** 测试用例都在 x86 和 riscv 失败了，共计 29 个，均是由于测试所需的包没有安装，这里只列举 3 个
+ [audit/oe_test_audit_ausearch](./mugen-riscv/logs/audit/oe_test_audit_ausearch/2025-08-09-03_30_50.log) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [audit/oe_test_audit_available_disk_space](./mugen-riscv/logs/audit/oe_test_audit_available_disk_space/2023-08-09-03_21_32.log) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装；依赖 service 命令，但是它属于早期系统的服务管理系统，在 23.03 和 23.09 riscv上均未配置
+ [audit/oe_test_audit_user_build_connection](./mugen-riscv/logs/audit/oe_test_audit_user_build_connection/2023-08-09-03_02_38.log) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装；依赖 service 命令，但是它属于早期系统的服务管理系统，在 23.03 和 23.09 riscv上均未配置
+ [chrony/oe_test_service_chrony-wait](./mugen-riscv/logs/chrony/oe_test_service_chrony-wait/2023-08-13-00_46_21.log) 依赖 chrony 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [ebtables/oe_test_service_ebtables](./mugen-riscv/logs/ebtables/oe_test_service_ebtables/2023-08-13-03_04_20.log) 依赖 ebtables 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [ipmitool/oe_test_service_bmc-snmp-proxy](./mugen-riscv/logs/ipmitool/oe_test_service_bmc-snmp-proxy/2023-08-08-10_55_56.log) 依赖 net-snmp 和 bmc-snmp-proxy 软件包，但是它们在 23.03 和 23.09 riscv 上均没有预装
+ [ipmitool/oe_test_service_exchange-bmc-os-info](./mugen-riscv/logs/ipmitool/oe_test_service_exchange-bmc-os-info/2023-08-08-10_57_16.log) 依赖 exchange-bmc-os-info 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [iprutils/oe_test_service_iprdump](./mugen-riscv/logs/iprutils/oe_test_service_iprdump/2023-08-09-04_11_11.log) 依赖 iprutils 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装，该问题导致测试套 4 个用例全部失败
+ [iptables/oe_test_service_ip6tables](./mugen-riscv/logs/iptables/oe_test_service_ip6tables/2023-08-08-10_21_44.log) 和 [iptables/oe_test_service_iptables](./mugen-riscv/logs/iptables/oe_test_service_iptables/2023-08-08-10_22_24.log) 依赖 iptables 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [keyutils/oe_test_keyutils-api](./mugen-riscv/logs/keyutils/oe_test_keyutils-api/2023-08-08-10_51_47.log) 依赖 keyutils-libs-devel 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [mtx/oe_test_mtx_loaderinfo](./mugen-riscv/logs/mtx/oe_test_mtx_loaderinfo/2023-08-08-10_33_35.log) 依赖 kernel-devel 软件包，但是它在 23.03 和 23.09 riscv 均没有预装。该问题导致测试套 5 个测试全部失败


#### 依赖的目录和文件没有预先建立

+ [kernel/oe_test_swap_compress](./mugen-riscv/logs/kernel/oe_test_swap_compress/2023-08-08-11_15_57.log) 测试使用的 swap 设备 ``/dev/dm-1`` 没有提前建立直接使用

#### 其他问题

+ [dnf/oe_test_dnf_enabled_enablerepo](./mugen-riscv/logs/dnf/oe_test_dnf_enabled_enablerepo/2023-08-08-11_33_50.log) 测试套编写的预期返回值不正确
+ [dnf/oe_test_dnf_list_mark](./mugen-riscv/logs/dnf/oe_test_dnf_list_mark/2023-08-08-11_59_07.log) 测试套需要软件源中的包高于预装的包，否则将失败

#### 没有考虑 qemu 环境

+ [kernel/oe_test_io_sched](./mugen-riscv/logs/kernel/oe_test_io_sched/2023-08-08-11_06_23.log) 测试脚本使用了 ``sda`` 作为测试用块设备，而在 qemu 中通常为 ``vda`` 。

#### 没有考虑 riscv

+ [os-basic/oe_test_uname](./mugen-riscv/logs/os-basic/oe_test_uname/2023-08-08-11_44_44.log) ``uname -m | grep -E "aarch64|x86_64"`` 显然在 riscv 上无法通过

#### 软件包问题

+ [iputils/oe_test_service_ninfod](./mugen-riscv/logs/iputils/oe_test_service_ninfod/2023-08-08-20_25_13.log) 软件包 iputils-ninfod 不存在，无法安装
+ [iputils/oe_test_service_rdisc](./mugen-riscv/logs/iputils/oe_test_service_rdisc/2023-08-08-20_26_21.log) 没有软件包提供 rdisc.service
+ [irqbalance/oe_test_service_irqbalance](./mugen-riscv/logs/irqbalance/oe_test_service_irqbalance/2023-08-09-09_59_33.log) 依赖 irqbalance 软件包，但是 23.09 riscv 源中不存在该包
```
[root@openeuler-riscv64 ~]# dnf install irpbalance
Last metadata expiration check: 1:18:29 ago on Sun Aug 13 00:01:56 2023.
No match for argument: irpbalance
Error: Unable to find a match: irpbalance
```
+ [kexec-tools/oe_test_service_kdump](./mugen-riscv/logs/kexec-tools/oe_test_service_kdump/2023-08-13-03_23_04.log) 依赖 kexec-tools 软件包，但是它在 23.09 riscv 源中不存在该包
```
[root@openeuler-riscv64 ~]# dnf install kexec-tools
Last metadata expiration check: 1:19:17 ago on Sun Aug 13 00:01:56 2023.
No match for argument: kexec-tools
Error: Unable to find a match: kexec-tools
```

+ [freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay](./mugen-riscv/logs/freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay/2023-08-12-19_15_16.log) 依赖 mysql5 和 mysql5-server 软件包，但是它们在 23.03 和 23.09 riscv 上均无法安装

#### 内核问题

+ [kernel/oe_test_check_huge_task](./mugen-riscv/logs/kernel/oe_test_check_huge_task/2023-08-08-11_17_26.log) CONFIG_BOOTPARAM_HUNG_TASK_PANIC_VALUE 配置在 23.09 riscv 的配置文件中没有找到
+ [kernel/oe_test_hinic](./mugen-riscv/logs/kernel/oe_test_hinic/2023-08-08-11_10_10.log) 23.09 riscv 内核缺失 hinic 模块
+ [lm_sensors/oe_test_service_fancontrol](./mugen-riscv/logs/lm_sensors/oe_test_service_fancontrol/2023-08-12-23_20_08.log) 23.09 riscv 缺失 cpuid 模块

#### qemu 问题

经常出现 reboot 以后测试节点失联，这部分用例通常需要重测，但是往往重测后问题稳定复现。

```
+ SSH_CMD ls 10.198.114.4 'openEuler12#$' root
+ cmd=ls
+ remoteip=10.198.114.4
+ remotepasswd='openEuler12#$'
+ remoteuser=root
+ timeout=300
+ connport=22
+ bash /root/mugen/libs/locallibs/sshcmd.sh -c ls -i 10.198.114.4 -u root -p 'openEuler12#$' -t 300 -o 22
1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms
Fri Apr 28 05:55:53 2023 - ERROR - connection to 10.198.114.4 failed.
```

### 对比23.03,23.09 riscv出现的新问题（23.09 fail ）

#### 重新测试通过

+ ``libstoragemgmt/oe_test_service_libstoragemgmt``
+ ``netcf/oe_test_service_netcf-transaction``
+ ``smoke-basic-os/oe_test_cmp``
+ ``dbus/oe_test_socket_dbus``
+ ``libosinfo/oe_test_osinfo-install-script``
+ ``quota/oe_test_service_rpc-rquotad``
+ ``quota/oe_test_service_quota_nld``
+ ``fio/oe_test_fio_003``
+ ``os-basic/oe_test_disk_schedule_specific``
+ ``os-basic/oe_test_IOaccess_1Gfile``
+ ``os-basic/oe_test_IOaccess_500Mfile``
+ ``os-basic/oe_test_server_mysql``

#### 可确定为缺陷

+ [smoke-basic-os/oe_test_llvm](./mugen-riscv/logs/smoke-basic-os/oe_test_llvm/2023-08-08-11_00_18.log) clang调用ld报错，此lib文件由gcc软件包提供，gcc已经安装，但对应path中无对应lib
  ```bash
  clang /tmp/test_llvm/llvm_test.c -o /tmp/test_llvm/test
  /usr/bin/ld: cannot find crtbeginS.o: No such file or directory
  /usr/bin/ld: cannot find -lgcc
  /usr/bin/ld: cannot find -lgcc_s
  clang-15: error: linker command failed with exit code 1 (use -v to see invocation)

  [root@openeuler-riscv64 ~]# yum whatprovides */crtbeginS.o
  Last metadata expiration check: 4 days, 2:14:06 ago on Sun Aug 13 21:07:31 2023.
  gcc-12.3.1-4.oe2309.riscv64 : Various compilers (C, C++, Objective-C, ...)
  Repo        : @System
  Matched from:
  Filename    : /usr/lib/gcc/riscv64-openEuler-linux/12/crtbeginS.o

  gcc-12.3.1-4.oe2309.riscv64 : Various compilers (C, C++, Objective-C, ...)
  Repo        : base
  Matched from:
  Filename    : /usr/lib/gcc/riscv64-openEuler-linux/12/crtbeginS.o

  rust-std-static-1.70.0-3.oe2309.riscv64 : Standard library for Rust
  Repo        : base
  Matched from:
  Filename    : /usr/lib/rustlib/riscv64gc-unknown-linux-musl/lib/self-contained/crtbeginS.o

  [root@openeuler-riscv64 ~]# whereis crtbeginS.o
  crtbeginS.o:
  [root@openeuler-riscv64 ~]# yum install gcc
  base                                            3.1 kB/s | 3.0 kB     00:00    
  extra                                           5.5 kB/s | 3.0 kB     00:00    
  Package gcc-12.3.1-4.oe2309.riscv64 is already installed.
  Dependencies resolved.
  Nothing to do.
  Complete!
  ```
+ [smoke-basic-os/oe_test_rpm_cmd](./mugen-riscv/logs/smoke-basic-os/oe_test_rpm_cmd/2023-08-08-12_26_28.log)中运行命令``yum install``报错
+ [smoke-basic-os/oe_test_user_debug_iotop_SCEN_01](./mugen-riscv/logs/smoke-basic-os/oe_test_user_debug_iotop_SCEN_01/2023-08-08-13_57_16.log)中``iotop -b -n 1 -d 10``命令未能正确显示PRIO（显示为?sys）


#### 软件包依赖出错或源中无对应软件

+ [oe_test_rpmlint](./mugen-riscv/logs/rpmlint/oe_test_rpmlint/2023-08-09-04_47_09.log) 和 [oe_test_rpmdiff](./mugen-riscv/logs/rpmlint/oe_test_rpmdiff/2023-08-09-04_46_08.log) 两个测试例安装软件rpmlint时发生依赖错误
```
Error: 
 Problem: conflicting requests
  - nothing provides python3.11dist(zstandard) needed by rpmlint-2.4.0-1.oe2309.noarch
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

+ [oe_test_gnome-shell](./mugen-riscv/logs/gnome-shell/oe_test_gnome-shell/2023-08-09-05_00_14.log) 安装软件gnome-shell时发生依赖错误
```
Error: 
 Problem: conflicting requests
  - nothing provides gcr4(riscv-64) needed by gnome-shell-43.2-2.oe2309.riscv64
  - nothing provides libgcr-4.so.0.0.0()(64bit) needed by gnome-shell-43.2-2.oe2309.riscv64
  - nothing provides pipewire-gstreamer(riscv-64) needed by gnome-shell-43.2-2.oe2309.riscv64
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

+ **bind**测试套中三个测试例由于软件bind不在源中，导致失败
```
Last metadata expiration check: 2:25:17 ago on Wed Aug  9 01:48:20 2023.
No match for argument: bind
Error: Unable to find a match: bind'
```

+ [clevis/oe_test_tang_nbde](./mugen-riscv/logs/clevis/oe_test_tang_nbde/2023-08-08-13_49_19.log) clevs 安装出错 nothing provides libcrypto.so.1.1()(64bit) libcrypto.so.1.1(OPENSSL_1_1_0)(64bit)
+ [os-basic/oe_test_virt-what](./mugen-riscv/logs/os-basic/oe_test_virt-what/2023-08-08-23_59_00.log) virt-what 安装失败 nothing provides dmidecode needed by virt-what-1.25-1.oe2309.riscv64
+ [perl-libwww-perl/*](./mugen-riscv/logs/perl-libwww-perl/) 软件源中没有 perl 6 ， perl-libwww-perl 安装失败 package perl-libwww-perl-6.67-1.oe2309.noarch requires perl >= 6, but none of the providers can be installed
+ [iSulad/oe_test_iSulad_container](./mugen-riscv/logs/iSulad/oe_test_iSulad_container/2023-08-09-04_19_54.log) [lxcfs/oe_test_service_lxcfs](./mugen-riscv/logs/lxcfs/oe_test_service_lxcfs/2023-08-08-16_50_22.log) lxcfs-tools 安装出错 nothing provides libabsl_synchronization.so.2206.0.0()(64bit) needed by iSulad-2.1.2-4.oe2309.riscv64
+ [os-basic/oe_test_server_mariadb_compatibilty](./mugen-riscv/logs/os-basic/oe_test_server_mariadb_compatibilty/2023-08-08-15_03_30.log) 软件包 mysql-server 与软件包 mariadb-server 冲突 package mysql-server-8.0.30-4.oe2309.riscv64 conflicts with mariadb-server provided by mariadb-server-4:10.5.16-3.oe2309.riscv64
+ [smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01](./mugen-riscv/logs/smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01/2023-08-08-13_44_23.log)中缺少libcrypto.so.1.1()(64bit)、libcrypto.so.1.1(OPENSSL_1_1_0)(64bit)
+ [openscap/oe_test_ensure_security_compliance](./mugen-riscv/logs/openscap/oe_test_ensure_security_compliance/2023-08-08-21_04_08.log)中缺少openscap-scanner >= 1.2.5并找不到openscap软件包
+ [smoke-basic-os/oe_test_ipv6_dnsmasq](./mugen-riscv/logs/smoke-basic-os/oe_test_ipv6_dnsmasq/2023-08-08-11_56_27.log) 2309 中找不到 bind-utils 软件包
+ [smoke-basic-os/oe_test_skopeo](./mugen-riscv/logs/smoke-basic-os/oe_test_skopeo/2023-08-08-10_47_43.log)中找不到skopeo软件包
+ [valgrind/oe_test_valgrind](./mugen-riscv/logs/valgrind/oe_test_valgrind/2023-08-08-12_05_24.log)中找不到valgrind软件包和glibc-debuginfo软件包
+ [tog-pegasus/oe_test_service_tog-pegasus](./mugen-riscv/logs/tog-pegasus/oe_test_service_tog-pegasus/2023-08-08-12_02_40.log)中找不到tog-pegasus软件包
+ [lxc/oe_test_lxc_ls_monitor](./mugen-riscv/logs/lxc/oe_test_lxc_ls_monitor/2023-08-08-03_26_01.log) 软件包 lxcfs 依赖关系不满足 package lxcfs-tools-0.3-30.oe2309.riscv64 requires iSulad, but none of the providers can be installed

#### 软件包版本升级而mugen脚本未能及时做出适配

+ [os-basic/oe_test_gcc_01](./mugen-riscv/logs/os-basic/oe_test_gcc_01/2023-08-09-00_01_09.log) 测试套预期为 ``warning: ‘i’ is used uninitialized in this function`` 但是实际输出
  ```bash
  # gcc -Wall main.c -o main
  main.c: In function 'main':
  main.c:6:4: warning: 'i' is used uninitialized [-Wuninitialized]
      6 |    printf("\n The Geek Stuff [%d]\n", i);
        |    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  main.c:5:8: note: 'i' was declared here
      5 |    int i;
        |
  ```

+ [gsl/oe_test_gsl_histogram_01](./mugen-riscv/logs/gsl/oe_test_gsl_histogram_01/2023-08-09-04_04_19.log): 23.09中gsl对应版本为2.7.1，软件命令行使用格式有所改变，但mugen脚本没有更新

+ [os-basic/oe_test_glibc](./mugen-riscv/logs/os-basic/oe_test_glibc/2023-08-08-23_50_28.log) glibc 升级导致的问题

+ [smoke-basic-os/oe_test_aide_init_database](./mugen-riscv/logs/smoke-basic-os/oe_test_aide_init_database/2023-08-08-13_29_13.log)中```aide --init```预期输出应该包括"AIDE initialized database at /var/lib/aide/aide.db.new.gz"但是实际输出为：

```bash
Start timestamp: 2023-08-13 19:32:26 +0800 (AIDE 0.18.5)
AIDE successfully initialized database.
New AIDE database written to /var/lib/aide/aide.db.new.gz

Number of entries:	51176

---------------------------------------------------
The attributes of the (uncompressed) database(s):
---------------------------------------------------

/var/lib/aide/aide.db.new.gz
.......
```

+ [smoke-basic-os/oe_test_rsyslog_10](./mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_10/2023-08-08-12_34_36.log)中date命令的输出按空格分割第六个字符按预期为应为时区，例如```Sun Aug 13 07:34:01 PM CST 2023```但是实际输出为```Sun Aug 13 07:34:01 CST 2023```，输出按空格分割第六个字符变成了年份。

+ [smoke-basic-os/oe_test_sort](./mugen-riscv/logs/smoke-basic-os/oe_test_sort/2023-08-08-10_47_30.log)中新版sort命令默认不忽略空行，导致测试失败

#### 镜像预装软件问题

+ [ModemManager/oe_test_service_ModemManager](./mugen-riscv/logs/ModemManager/oe_test_service_ModemManager/2023-08-08-16_16_45.log) 镜像没有预装 polkit 软件包

+ [os-basic/oe_test_vim](./mugen-riscv/logs/os-basic/oe_test_vim/2023-08-08-22_46_29.log) 没有预装 vim 软件包

+ [os-basic/oe_test_lastb](./mugen-riscv/logs/os-basic/oe_test_lastb/2023-08-08-23_09_02.log) secret-tool 在 03 预装了但是在 09 没有预装
+ [smoke-basic-os/oe_test_rsyslog_04](./mugen-riscv/logs/smoke-basic-os/oe_test_rsyslog_04/2023-08-08-12_28_17.log)没有预装rsyslog
+ [smoke-basic-os/oe_test_iptables](./mugen-riscv/logs/smoke-basic-os/oe_test_iptables/2023-08-08-11_55_20.log)没有预装iptables
+ [smoke-basic-os/oe_test_os-prober_01](./mugen-riscv/logs/smoke-basic-os/oe_test_os-prober_01/2023-08-08-12_17_56.log)没有预装os-prober
+ [smoke-basic-os/oe_test_glibc_getaddrinfo_01](./mugen-riscv/logs/smoke-basic-os/oe_test_glibc_getaddrinfo_01/2023-08-08-11_50_54.log)缺少host命令

## 内核问题

+ [kernel/oe_test_bnx2fc](./mugen-riscv/logs/kernel/oe_test_bnx2fc/2023-08-08-11_25_04.log) 内核模块 scsi/hpsa.ko 不存在
+ [os-basic/oe_test_python3-kmod](./mugen-riscv/logs/os-basic/oe_test_python3-kmod/2023-08-09-00_17_54.log) snd_seq 内核模块不存在

#### 其他问题

+ [FS_File/oe_test_FSIO_check_alias](./mugen-riscv/logs/FS_File/oe_test_FSIO_check_alias/2023-08-09-02_17_51.log) 中mugen欲在~/.bashrc处修改配置，但对应文件不存在，mugen没有考虑此情况，没有手动创建，但另一方面也可判断为镜像中bash配置文件缺失
```
+ grep -q 'alias rm='\''rm -i'\''' /root/.bashrc
grep: /root/.bashrc: No such file or directory
+ CHECK_RESULT 2 0 0 'The alias on system are changed.'
```

+ [dnf/oe_test_dnf_check_diffenert-packages](./mugen-riscv/logs/dnf/oe_test_dnf_check_diffenert-packages/2023-08-08-11_15_02.log) dnf check-update 异常推出，返回值 100 ，没有打印报错信息
+ [fio/oe_test_fio_002](./mugen-riscv/logs/fio/oe_test_fio_002/2023-08-08-14_28_48.log) 测试多个 fio-dedupe 命令测试失败，原因未知
+ [openssh/oe_test_openssh_ssh-copy-id](./mugen-riscv/logs/openssh/oe_test_openssh_ssh-copy-id/2023-08-11-00_35_09.log) ssh-keygen 没有生成 pubkey 导致 /usr/bin/ssh-copy-id: ERROR: failed to open ID file '/root/.ssh/id_rsa.pub': No such file or directory
+ [samba/oe_test_service_ctdb](./mugen-riscv/logs/samba/oe_test_service_ctdb/2023-08-08-13_45_48.log) 未知原因 ctdb.service: Failed to parse PID from file /run/ctdb/ctdbd.pid: Invalid argument
+ [os-basic/oe_test_server_openssh_secure](./mugen-riscv/logs/os-basic/oe_test_server_openssh_secure/2023-08-08-13_52_50.log) 测试失败
  ```bash
  firewall-cmd --add-port 22/tcp
  ERROR:dbus.proxies:Introspect error on :1.60:/org/fedoraproject/FirewallD1: dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NoReply: Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.
  Error: Did not receive a reply. Possible causes include: the remote application did not send a reply, the message bus security policy blocked the reply, the reply timeout expired, or the network connection was broken.
  ```
+ [libosinfo/oe_test_osinfo-db-import](./mugen-riscv/logs/libosinfo/oe_test_osinfo-db-import/2023-08-08-10_53_45.log) 中导出文件名按照当前时间命名，测试脚本中寻找的导出文件名是写死的
+ [PackageKit/oe_test_packagekit_pkcon](./mugen-riscv/logs/PackageKit/oe_test_packagekit_pkcon/2023-08-08-10_57_28.log) ``pkcon install git -y`` 报 Fatal error: Failed to check for authentication: GDBus.Error:org.freedesktop.DBus.Error.ServiceUnknown: The name org.freedesktop.PolicyKit1 was not provided by any .service files

+ [os-basic/oe_test_xmlsec](./mugen-riscv/logs/os-basic/oe_test_xmlsec/2023-08-08-23_00_32.log) 测试程序链接失败
  ```bash
  gcc -I/usr/include/libxml2 -lxml2 -lz -llzma -lm -I/usr/include/libxml2 -lxslt -lxml2 -lm -lxmlsec1 -g -D__XMLSEC_FUNCTION__=__FUNCTION__ -DXMLSEC_NO_XKMS=1 -DXMLSEC_CRYPTO_OPENSSL -DXMLSEC_CRYPTO_DYNAMIC_LOADING=1 -DUNIX_SOCKETS -DXMLSEC_NO_SIZE_T sign.c -o sign1
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlIOFTPOpen@LIBXML2_2.4.30'
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlIOFTPRead@LIBXML2_2.4.30'
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlIOFTPClose@LIBXML2_2.4.30'
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlNanoFTPInit@LIBXML2_2.4.30'
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlNanoFTPCleanup@LIBXML2_2.4.30'
  /usr/bin/ld: /lib64/lp64d/libxmlsec1.so: undefined reference to `xmlIOFTPMatch@LIBXML2_2.4.30'
  collect2: error: ld returned 1 exit status
  ```


### 与23.03测试同样报错但错误原因不同

#### 软件包安装时发生依赖错误

+ [gdm/oe_test_service_gdm](./mugen-riscv/logs/gdm/oe_test_service_gdm/2023-08-08-20_22_28.log)2309上没有gnome-shell的依赖
```
Error: 
 Problem: package gdm-1:43.0-1.oe2309.riscv64 requires gnome-shell, but none of the providers can be installed
  - conflicting requests
  - nothing provides gcr4(riscv-64) needed by gnome-shell-43.2-2.oe2309.riscv64
  - nothing provides libgcr-4.so.0.0.0()(64bit) needed by gnome-shell-43.2-2.oe2309.riscv64
  - nothing provides pipewire-gstreamer(riscv-64) needed by gnome-shell-43.2-2.oe2309.riscv64
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

+ [clevis/oe_test_install_clevis](./mugen-riscv/logs/clevis/oe_test_install_clevis/2023-08-08-13_33_02.log)软件包 clevis 依赖问题
```
Error: 
 Problem: conflicting requests
  - nothing provides libcrypto.so.1.1()(64bit) needed by clevis-18-2.oe2309.riscv64
  - nothing provides libcrypto.so.1.1(OPENSSL_1_1_0)(64bit) needed by clevis-18-2.oe2309.riscv64
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

+ [smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01](./mugen-riscv/logs/smoke-basic-os/oe_test_ima_v2_manual_gen_digest_01/2023-08-08-13_44_23.log)2309上没有digest-list-tools 的依赖
```
Error: 
 Problem: conflicting requests
  - nothing provides libcrypto.so.1.1()(64bit) needed by digest-list-tools-0.3.95-13.oe2309.riscv64
  - nothing provides libcrypto.so.1.1(OPENSSL_1_1_0)(64bit) needed by digest-list-tools-0.3.95-13.oe2309.riscv64
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

#### 测试后发生错误与oe23.03 x86上发生错误一致

+ [kmod/oe_test_depmod](./mugen-riscv/logs/kmod/oe_test_depmod/2023-08-09-03_48_11.log)与oe2303x86的情况相同，find Module.symvers与System.map均为空
```
++ find / -name Module.symvers
+ symversPath=
+ depmod -e -E
depmod: option requires an argument -- 'E'
...
++ find / -name System.map
+ mapPath=
+ depmod -e -F
depmod: option requires an argument -- 'F'
```

+ [kmod/oe_test_modprobe](./mugen-riscv/logs/kmod/oe_test_modprobe/2023-08-09-03_51_36.log) 与oe2303x86的情况相同，dm_cache模块存在，但modprobe -f dm_cache提示modprobe: ERROR: could not insert 'dm_cache': Key was rejected by service

