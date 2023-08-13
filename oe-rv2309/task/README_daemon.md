# openEuler 23.09 RISC-V Mugen测试结果

本次测试基于 [mugen](https://gitee.com/openeuler/mugen) [2023年8月1日](https://gitee.com/openeuler/mugen/commit/d1fb5af5572de344090fb979bdc45694564b0620)测试套仓库中包含的所有base OS测试套及测试用例。

测试方法详见 [Mugen 测试方法](./Mugen.md)

共测试了 **？** 个测试套， **？** 个测试用例，其中

- **？** 个测试用例在23.09上成功
- **？** 个测试用例在23.03和23.09上均失败，失败原因相同
- **？** 个测试用例在23.03和23.09上均失败，失败原因不同
- **？** 个测试用例在23.09上失败，在23.03上成功

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

此表内的测试套及测试用例均为在23.03上成功，在 23.09 上失败的 BaseOS 测试用例。共涉及 ? 个测试套 ? 个测试用例。




## BaseOS 在 23.09 和 23.03 版本下均失败用例

此表内的测试套和测试用例均为在 23.03 上和 23.09 上均失败的 BaseOS 测试用例




## BaseOS 在 23.09 上测试通过用例

此表中的测试用例均为在 RISC-V 上测试通过的测试用例



## 23,09 fail 失败原因排查汇总

我们将测试状态为 x86 fail 的用例大致分为三类：

1. 23.09 和 23.03 失败原因完全一样 [base_OS_same.csv]()
2. 23.09 和 23.03 失败原因不同 [base_OS_diff.csv]()
3. 23.09 下失败，但在 23.03 下成功 [base_OS_09fail.csv]()

又根据失败的具体原因，在下文给出一个详细的分类和每种类型的测试用例举例。

### 目前在23.03与23.09下失败原因相同

#### 测试套 suite2cases 问题

+ [rsyslog/oe_test_rsyslog_abnormal_template.sh](https://gitee.com/openeuler/mugen/blob/master/testcases/cli-test/rsyslog/oe_test_rsyslog_abnormal_template/oe_test_rsyslog_abnormal_template.sh) 这一测试用例需要 NODE2 配置，即需要 2 个节点进行测试，但是 [rsyslog.json](https://gitee.com/openeuler/mugen/blob/master/suite2cases/rsyslog.json) 中没有 "machine num" 配置。这一问题在该测试套共影响 10 个测试用例

#### 测试套脚本问题

+ [os-basic/oe_test_system_log_recorded](./x86_fail_md/os-basic/oe_test_system_log_recorded.md) ``folder=$(ls /run/log/journal/)`` 若 ``ls`` 命令输出零个（riscv）或多个（x86 两个）就会出现问题，但是并没有进行处理
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
+ [kmod/oe_test_depmod](./x86_fail_md/kmod/oe_test_depmod.md) ``symversPath=$(find / -name Module.symvers)`` 和 ``mapPath=$(find / -name System.map)`` 两个命令，若 ``ls`` 命令输出多个（riscv 两个）就会出现问题，但是并没有进行处理
+ [initscripts/oe_test_service_network.sh](https://gitee.com/openeuler/mugen/blob/master/testcases/cli-test/initscripts/oe_test_service_network.sh#L37) 测试行命令拼写错误 ``journalct --since`` ，由于该测试命令返回非 0 为成功，这行测试将永远成功（测试成功但是脚本有问题）

#### 命令输出与 grep 预期不符

+ [os-basic/oe_test_dmraid](./x86_fail_md/os-basic/oe_test_dmraid.md) ``dmraid -s`` 输出为 ``no block devices found`` ，而测试脚本预期结果为 ``no raid disks`` ，导致没有判定出 raid 不存在
+ [os-basic/oe_test_whereis](./x86_fail_md/os-basic/oe_test_whereis.md) ``whereis --help | grep "Usage: whereis"`` 匹配失败， ``whereis --help`` 的实际输出为
   ```
   Usage:
    whereis [options] [-BMS <dir>... -f] <name>
   ```
+ [openssl/oe_test_openssl_DSA_algorithm](./x86_fail_md/openssl/oe_test_openssl_DSA_algorithm.md) ``grep 'BEGIN DSA PRIVATE KEY'`` 失败，实际应为 ``BEGIN PRIVATE KEY`` 。同样的错误影响了 11 个测试用例
+ [openssl/oe_test_openssl_speed](./x86_fail_md/openssl/oe_test_openssl_speed.md) ``grep "aes-128 cbc"`` 和 ``grep "sm4-cbc"`` 失败，实际应为 ``aes-128-cbc`` 和 ``SM4-CBC``
+ [iptables/oe_test_iptables-restore_01](./x86_fail_md/iptables/oe_test_iptables-restore_01.md) 和 [iptables/oe_test_iptables-restore](./x86_fail_md/iptables/oe_test_iptables-restore.md) ``grep "DROP       icmp"`` 失败， ``ip6tables -nvL`` 输出没有出现该字段
+ [iptables/oe_test_ip6tables-save](./x86_fail_md/iptables/oe_test_ip6tables-save.md) ``grep -A 200 nat | grep -A 100 mangle | grep -A 80 raw | grep -A 60 security | grep filter`` ， ``ip6tables-save`` 命令实际输出没有出现这些字段
+ [klibappstream-glibeyutils/oe_test_libappstream-glib_appstream-util_03](./x86_fail_md/klibappstream-glibeyutils/oe_test_libappstream-glib_appstream-util_03.md) ``grep http://www.ezix.org/project/wiki/HardwareLiSter`` 失败
+ [mc/oe_test_mc_base_01](./x86_fail_md/mc/oe_test_mc_base_01.md) ``grep 'Home路径'`` 尝试 grep 中文字串，实际输出为 ``Home directory``

#### 内核模块名称和预期不同

+ [kmod/oe_test_insmod-lsmod](./x86_fail_md/kernel/oe_test_insmod-lsmod.md) 在 23.09 riscv 的内核上， raid0 模块文件名为 ``raid0.ko.xz`` 、 faulty 模块文件名为 ``faulty.ko.xz`` ，而测试套预期文件名为 ``raid0.ko`` 和 ``faulty.ko`` ，导致测试失败
+ [kmod/oe_test_modprobe](./x86_fail_md/kernel/oe_test_modprobe.md) 在 23.09 riscv 的内核上 dm_log 模块的文件名为 ``dm-log.ko.xz`` ，而测试套预期文件名为 ``dm-log.ko`` ，导致测试失败

#### 依赖的命令没有安装也没有预装

+ os-basic/oe_test_auditctl 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [os-basic/oe_test_server_openssh_verifykey](./x86_fail_md/os-basic/oe_test_server_openssh_verifykey.md) 依赖 policycoreutils 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [os-basic/oe_test_count_gperftools_function](./x86_fail_md/os-basic/oe_test_count_gperftools_function.md) 依赖 gperftools-devel 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [os-basic/oe_test_disk_graphics_card](./x86_fail_md/os-basic/oe_test_disk_graphics_card.md) 依赖 pciutils 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ 大部分 **audit** 测试用例都在 x86 和 riscv 失败了，共计 29 个，均是由于测试所需的包没有安装，这里只列举 3 个
+ [audit/oe_test_audit_ausearch](./x86_fail_md/os-basic/oe_test_audit_ausearch.md) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [audit/oe_test_audit_available_disk_space](./x86_fail_md/os-basic/oe_test_audit_available_disk_space.md) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装；依赖 service 命令，但是它属于早期系统的服务管理系统，在 23.03 和 23.09 riscv上均未配置
+ [audit/oe_test_audit_user_build_connection](./x86_fail_md/os-basic/oe_test_audit_user_build_connection.md) 依赖 audit 软件包，但是它在 23.03 和 23.09 riscv 均没有预装；依赖 service 命令，但是它属于早期系统的服务管理系统，在 23.03 和 23.09 riscv上均未配置
+ [chrony/oe_test_service_chrony-wait](./x86_fail_md/chrony/oe_test_service_chrony-wait.md) 依赖 chrony 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [ebtables/oe_test_service_ebtables](./x86_fail_md/ebtables/oe_test_service_ebtables.md) 依赖 ebtables 软件包，但是它在 23.03 和 23.09 riscv 均没有预装
+ [ipmitool/oe_test_service_bmc-snmp-proxy](./x86_fail_md/ipmitool/oe_test_service_bmc-snmp-proxy.md) 依赖 net-snmp 和 bmc-snmp-proxy 软件包，但是它们在 23.03 和 23.09 riscv 上均没有预装
+ [ipmitool/oe_test_service_exchange-bmc-os-info](./x86_fail_md/ipmitool/oe_test_service_exchange-bmc-os-info.md) 依赖 exchange-bmc-os-info 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [iprutils/oe_test_service_iprdump](./x86_fail_md/iprutils/oe_test_service_iprdump.md) 依赖 iprutils 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装，该问题导致测试套 4 个用例全部失败
+ [ipset/oe_test_service_ipset](./x86_fail_md/ipset/oe_test_service_ipset.md) 依赖 ipset 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装，该问题导致测试套 4 个用例全部失败
+ [iptables/oe_test_service_ip6tables](./x86_fail_md/iptables/oe_test_service_ip6tables.md) 和 [iptables/oe_test_service_iptables](./x86_fail_md/iptables/oe_test_s ervice_iptables.md) 依赖 iptables 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [keyutils/oe_test_keyutils-api](./x86_fail_md/keyutils/oe_test_keyutils-api.md) 依赖 keyutils-libs-devel 软件包，但是它在 23.03 和 23.09 riscv 上均没有预装
+ [mtx/oe_test_mtx_loaderinfo](./x86_fail_md/mtx/oe_test_mtx_loaderinfo.md) 依赖 kernel-devel 软件包，但是它在 23.03 和 23.09 riscv 均没有预装。该问题导致测试套 5 个测试全部失败
# + [freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay](./x86_fail_md/freeradius/oe_test_freeradius_freeradius-utils_radsqlrelay.md) 依赖 mysql5 和 mysql5-server 软件包，但是它们在 x86 和 riscv 上均无法安装

#### 依赖的目录和文件没有预先建立

+ [kernel/oe_test_swap_compress](./x86_fail_md/kernel/oe_test_swap_compress.md) 测试使用的 swap 设备 ``/dev/dm-1`` 没有提前建立直接使用

#### 其他问题

+ [dnf/oe_test_dnf_enabled_enablerepo](./x86_fail_md/dnf/oe_test_dnf_enabled_enablerepo.md) 测试套编写的预期返回值不正确
+ [dnf/oe_test_dnf_list_mark](./x86_fail_md/dnf/oe_test_dnf_list_mark.md) 测试套需要软件源中的包高于预装的包，否则将失败

#### 没有考虑 qemu 环境

+ [kernel/oe_test_io_sched](./x86_fail_md/kernel/oe_test_io_sched.md) 测试脚本使用了 ``sda`` 作为测试用块设备，而在 qemu 中通常为 ``vda`` 。

#### 没有考虑 riscv

+ [os-basic/oe_test_uname](./x86_fail_md/os-basic/oe_test_uname.md) ``uname -m | grep -E "aarch64|x86_64"`` 显然在 riscv 上无法通过

#### 软件包问题

+ [iputils/oe_test_service_ninfod](./x86_fail_md/iputils/oe_test_service_ninfod.md) 软件包 iputils-ninfod 不存在，无法安装
+ [iputils/oe_test_service_rdisc](./x86_fail_md/iputils/oe_test_service_rdisc.md) 没有软件包提供 rdisc.service
+ [irqbalance/oe_test_service_irqbalance](./x86_fail_md/irqbalance/oe_test_service_irqbalance.md) 依赖 irqbalance 软件包，但是 23.09 riscv 源中不存在该包
```
[root@openeuler-riscv64 ~]# dnf install irpbalance
Last metadata expiration check: 1:18:29 ago on Sun Aug 13 00:01:56 2023.
No match for argument: irpbalance
Error: Unable to find a match: irpbalance
```
+ [kexec-tools/oe_test_service_kdump](./x86_fail_md/kexec-tools/oe_test_service_kdump.md) 依赖 kexec-tools 软件包，但是它在 23.09 riscv 源中不存在该包
```
[root@openeuler-riscv64 ~]# dnf install kexec-tools
Last metadata expiration check: 1:19:17 ago on Sun Aug 13 00:01:56 2023.
No match for argument: kexec-tools
Error: Unable to find a match: kexec-tools
```

#### 内核问题

+ [kernel/oe_test_check_huge_task](./x86_fail_md/kernel/oe_test_check_huge_task.md) CONFIG_BOOTPARAM_HUNG_TASK_PANIC_VALUE 配置在 23.09 riscv 的配置文件中没有找到
+ [kernel/oe_test_hinic](./x86_fail_md/kernel/oe_test_hinic.md) 23.09 riscv 内核缺失 hinic 模块
+ [lm_sensors/oe_test_service_fancontrol](./x86_fail_md/lm_sensors/oe_test_service_fancontrol.md) 23.09 riscv 缺失 cpuid 模块

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

#### 软件包依赖出错或源中无安装软件

+ [oe_test_rpmlint]() 和 [oe_test_rpmdiff]() 两个测试例安装软件rpmlint时发生依赖错误
```
Error: 
 Problem: conflicting requests
  - nothing provides python3.11dist(zstandard) needed by rpmlint-2.4.0-1.oe2309.noarch
(try to add '\''--skip-broken'\'' to skip uninstallable packages or '\''--nobest'\'' to use not only best candidate packages)'
```

+ [oe_test_gnome-shell]() 安装软件gnome-shell时发生依赖错误
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

#### mugen 中测试的软件与最新版本软件操作不同

+ [oe_test_gsl_histogram_01](): 23.09中gsl对应版本为2.7.1，软件命令行使用格式有所改变，但mugen脚本没有更新


#### 其他问题

+ [oe_test_FSIO_check_alias]() 中mugen欲在~/.bashrc处修改配置，但对应文件不存在，mugen没有考虑此情况，没有手动创建，但另一方面也可判断为镜像中bash配置文件缺失
```
+ grep -q 'alias rm='\''rm -i'\''' /root/.bashrc
grep: /root/.bashrc: No such file or directory
+ CHECK_RESULT 2 0 0 'The alias on system are changed.'
```