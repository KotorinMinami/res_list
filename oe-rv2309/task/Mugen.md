# 摘要

openEuler 是一款开源操作系统。当前 openEuler 内核源于 Linux ，支持多种处理器架构，是由全球开源贡献者构建的高效、稳定、安全的开源操作系统。 

本文主要描述 openEuler RISC-V 23.09 版本自动化mugen测试方法

**关键词**：RISC-V，自动化测试，mugen

## 测试配置

#### 测试框架和测试方法

测试框架：mugen-riscv

测试方式：测试环境自动复原，测试套间隔离以及自动分配硬盘外设资源的自动化测试

测试使用脚本：

1. qemu_test.py

   实现了自动化测试环境复原和多线程测试的功能

   使用qemu_test.py，利用qemu qcow2快照实现在测试每个测试套时单独建立qemu虚拟机进行测试，保证了测试套间不会相互干扰。测试程序运行时会根据测试套文件中"add disk"字段的信息，自动创建硬盘资源并分配给对应的虚拟机。

2. mugen_riscv.py

   mugen_riscv.py主要用于解决以下问题

   - openEuler的mugen项目目前用于openEuler x86/AArch64的测试，并不方便直接用于当前openEuler RISC-V的测试
   - 测试项目（测试套）并不能很好地匹配
   - 目前的openEuler RISC-V测试依靠QEMU虚拟机
   - mugen中一个测试套对应一个软件或服务，为了更方便地执行测试，可以再抽象一层

   因此mugen_riscv.py用于辅助测试脚本匹配测试列表和mugen中的测试套，并反馈缺失的测试和执行可用测试，因此有以下功能

   - 指定运行mugen原生测试套
     - 由于mugen只原生支持X86和AArch64，用于RISC-V时需要进行适配（我们现在正在进行这项工作），本mugen的riscv分支使用测试套文件区分已适配的测试和为适配的测试。即已适配的测试套在suite2cases/目录下会有一个对应带"-riscv"后缀的测试套描述文件
     - mugen_riscv.py在运行测试时默认优先匹配适配后的测试套
     - 需要运行mugen原生测试套里的测试，需要在执行时加上-m参数
   - 使用mugen_riscv.py的适配缺失分析功能
     - mugen_riscv.py的适配缺失分析功能用于帮助列出适配后测试套相对于mugen原生测试套缺失的测试例（即为测试套中为做移植适配的测试例）

   - 让mugen_riscv.py执行测试后生成可用测试的测试套描述文件
     - 移植mugen的测试需要筛选出测试套中哪些测试可以在RISC—V环境下正确运行，并将这些可用的测试整合形成一个对应的带"-riscv"后缀的测试套描述文件
     - 判断测试是否可用的一个简单依据是测试运行结果，如果测试通过，那么一般情况下测试和被测对象本身都是正确的
     - 因此可以让mugen_riscv.py在执行测试后将通过的测试整合，并生成对应的测试套描述文件，从而方便mugen测试移植乃至开发工作

3. result_parser.py

   result_parser.py为测试结果分析脚本,用于自动解析qemu_test.py测试结果日志文件，生成统计结果和初步的错因分析

   - 运行目录下存放logs logs_failed suite2cases catalog.json四个文件和文件夹，logs和logs_failed为测试结果，suite2cases为测试所用mugen的suite2cases文件夹，catalog.json为错因归类数据，在本仓库中有参考文件
   - 运行此脚本生成result.json result.csv failureCause.csv三个文件，result.json为统计结果的原始数据，result.csv为各测试套通过/未通过的统计结果，failureCause.csv中为错因分析

测试用例代码位置：https://gitee.com/src-oerv/mugen/tree/riscv/testcases/cli-test/对应测试套名/对应测试用例名.sh

#### 测试环境

- RISCV镜像：https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/testing/20230728/QEMU/

- host上mugen使用https://github.com/brsf11/mugen-riscv

- guest上mugen使用上游仓库版本(revision [d1fb5af5](https://gitee.com/openeuler/mugen/commit/d1fb5af5572de344090fb979bdc45694564b0620))

- 软件源：默认

- CPU核数：4

- 内存大小：4G

- qemu启动参数：


```
qemu-system-riscv64 \
-nographic -machine virt  \
-smp 4 -m 4G \
-audiodev pa,id=snd0 \
-bios fw_payload_oe_uboot_2304.bin \
-drive file=mugen_ready.qcow2,format=qcow2,id=hd0 \
-object rng-random,filename=/dev/urandom,id=rng0 \
-device virtio-rng-device,rng=rng0 \
-device virtio-blk-device,drive=hd0 \
-device virtio-net-device,netdev=usernet \
-netdev user,id=usernet,hostfwd=tcp::"ssh_port"-:22 \
-device qemu-xhci -usb -device usb-kbd -device usb-tablet -device usb-audio,audiodev=snd0 \ 
```

- 附加硬盘qemu参数：


```
-drive file=disk"self.id-i".qcow2,format=qcow2,id=hd"i" -device virtio-blk-pci,drive=hd"i"
```

- self.id：测试时为对应虚拟机的id

- i：测试时为某一虚拟机的硬盘序号

- mugen_ready.qcow2处理
  mugen_ready.qcow2由原始镜像openEuler-23.03-V1-base-qemu-preview.qcow2安装git和mugen依赖而来

## 分级测试策略

在进行测试的时候需要进行对比测试，会将测试结果与23.03下mugen的base OS测试结果进行比较，分级测试阶段如下

测试阶段1：

1. 在riscv上将需要进行测试的测试套进行mugen测试


测试阶段2：

1. 对在23.03和23.09上均失败的测试套进行测试失败原因分析，判断造成两者失败的缺陷原因是否一致，之前在23.03上发现的缺陷是否修复，是否出现新的缺陷


测试阶段3：

1. 对在23.03上成功 23.09上失败的测试套进行测试失败原因分析

在测试结束后，将获得四个测试结果：

- 在23.09上成功的测试用例
- 在23.03上和23.09上均失败的测试用例，其中失败原因一致
- 在23.03上和23.09上均失败的测试用例，其中失败原因不一致
- 在23.03上成功，但是在23.09上失败的用例