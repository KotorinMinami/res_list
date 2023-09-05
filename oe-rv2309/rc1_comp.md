# same
## acl
### oe_test_access_providepam
- 原因相同，pam_systemd.so不存在
## cmake
### oe_test_ccmake
- 原因相同，spawn原因，脚本失败
### oe_test_ccmake3
- 原因相同，spawn原因，脚本失败
## crontabs
### oe_test_crontabs
- 原因相同，Cannot obtain SELinux process context
## initscripts
### oe_test_service_netconsole
- 原因相同，systemd启动失败
## iputils
### oe_test_service_ninfod
- 原因相同，源内无软件包iputils-ninfod
### oe_test_service_rdisc
- 原因相同，Unit rdisc.service not found.
## iprutils
- 原因相同，测试脚本没有显式安装iprutils，镜像也没有预装
## mysql
### oe_test_service_mysql
- 原因相同，安装mysql软件包后，无法找到mysql.service
## patchutils
### oe_test_patchutils_combinediff_01
- 原因相同，``Check combinediff -U 10 -i -w -b -B -q test1.patch test2.patch  failed``与 ``Check combinediff --unified=10 --ignore-case --ignore-all-space --ignore-space-change --ignore-blank-lines --quiet test1.patch test2.patch  failed``
### oe_test_patchutils_flipdiff_01
- 原因相同，同上一样指令选项报错
### oe_test_patchutils_interdiff_01
- 原因相同，同上一样指令选项报错
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
## samba
### oe_test_service_samba
- 原因相同，samba.service 停止失败，
## smoke-basic-os
### oe_test_dumpe2fs
- 原因相同，将硬盘格式化时出现问题，无法在上面创建文件inode
### oe_test_ethtool_01
- 原因不同，x86为使用127.0.0.1配置时无NODE1_NIC,riscv为软件预装逻辑错误
### oe_test_mke2fs
- 原因相同，将硬盘格式化时出现问题，无法在上面创建文件inode
### oe_test_mkdosfs
- 原因相同，将硬盘格式化时出现问题，无法在上面创建文件inode
### oe_test_ncurses
- 原因相同，failed to execute infotocap
### oe_test_network_001
- 原因不同，x86为使用127.0.0.1配置时无NODE1_NIC,riscv为软件未预装
### oe_test_rsyslog_03
- 原因不同，x86为test命令报错，riscv为软件未预装
### oe_test_sevice_001
- 原因相同，systemctl -t service 输出不符合预期

# diff
## iSulad
### oe_test_iSulad_container
- 原因不同，x86为镜像启动失败，riscv为软件包安装时发生依赖错误 nothing provides libabsl_synchronization.so.2206.0.0()(64bit) needed by iSulad-2.1.2-4.oe2309.riscv64
### oe_test_iSulad_resource_mapping
- 原因不同，x86未预装软件hostname,riscv出现预装问题
### oe_test_iSulad_resource_restriction_cgroup
- 原因不同，x86为镜像启动失败，riscv为软件包安装时发生依赖错误 nothing provides libabsl_synchronization.so.2206.0.0()(64bit) needed by iSulad-2.1.2-4.oe2309.riscv64
### oe_test_service_isulad
- 原因不同，x86文件缺失且enable服务失败;riscv 出现安装依赖错误
## initscripts
### oe_test_service_import-state
- 原因相同，import-state.service启动命令返回值为零但启动失败
## smoke-basic-os
### oe_test_ima_v2_manual_gen_digest_01
- 原因不同，x86``Cannot initialize libselinux``;riscv安装时发生依赖错误
### oe_test_ip6tables_01
- 原因相同，使用127.0.0.1进行mugen配置时NODE1_NIC为空

# 09fail
## polkit
### oe_test_service_polkit
- 原因相同，软件polkit未安装
## smoke-basic-os
### oe_test_aide_init_database
- 原因相同，新版aide命令输出发生变化
## timedatex
- 原因相同，timedatex对应软件包未安装
## valgrind
- 原因不同，x86为软件gcc未预装，riscv为源中无对应软件