# mugen测试用例整理工作说明
## 测试要求
- 从rest*中自选一个进行测试，推荐根据此[仓库](https://github.com/brsf11/Tarsier-Internship/tree/main/Testing/mugenTestcase)中选择部分对应进行测试
- 在riscv镜像上运行测试后，将未通过的测试在X86镜像上再进行一次测试。
- 将结果整理形成表格，格式参考[用例结果整理issue](https://gitee.com/openeuler/RISC-V/issues/I77F5M?from=project-issue)中附件csv
- riscv上测试通过的状态记为success/原因记为None，riscv和X86上均未通过状态和原因记为x86 fail，其余（riscv未通过，x86通过）状态记为fail，具体未通过原因需要逐个进行分析（可选）
## 划分说明
- 根据[needTest.csv](./needtest.csv)进行平均划分，其中有些已经测试但未测试完全的测试套，可参考表格进行测试
- 一个测试套可能出现有一些测试例中安装软件为出现在riscv的dnf仓库列表中，可参考[nopkg.csv](./nopkg.csv) 
## 测试环境
- RISCV镜像（不需要xfce，统一使用base）：https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/testing/20230331_openEuler-23.03-V1-riscv64/QEMU/  
- 2303 x86 的镜像https://mirror.iscas.ac.cn/openeuler/openEuler-23.03/virtual_machine_img/x86_64/
- kernel和initrd对应文件https://mirror.iscas.ac.cn/openeuler/openEuler-23.03/OS/x86_64/images/pxeboot/
- host上mugen使用https://github.com/brsf11/mugen-riscv
- guest上mugen使用[上游仓库]([https://gitee.com/src-oerv/mugen](https://gitee.com/openeuler/mugen))版本(revision [9577098](https://gitee.com/openeuler/mugen/commit/95770982e86665a2beffa90c07b031da937333ac))
- qemu_test.py运行推荐用-F加配置文件的方法，在测试配置文件中开启addDisk/multiMachine/addNic
