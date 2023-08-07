# openEuler 23.09 baseOS Mugen 测试说明
## 测试要求
- 从./task/oe2309test*中自选一个进行测试
- 对失败样例进行分析，对比[2303 baseOS 结果](https://gitee.com/yunxiangluo/oerv-2303-test/tree/master/BasicTest/function/mugen),重点分析2303版本的缺陷是否已经修复，是否出现新的缺陷,结果格式参考上面报告。
- 最后结果表格提交至此仓库oe-rv2309文件夹下
## 测试环境
- RISCV镜像（不需要xfce，统一使用base，后续mirror.iscas.ac.cn上同步更新后也可使用那里的）：https://repo.tarsier-infra.com/openEuler-RISC-V/testing/20230728/QEMU/
- host上mugen使用：https://github.com/brsf11/mugen-riscv
- guest上mugen使用[上游仓库]([https://gitee.com/src-oerv/mugen](https://gitee.com/openeuler/mugen))版本(revision [d1fb5af5](https://gitee.com/openeuler/mugen/commit/d1fb5af5572de344090fb979bdc45694564b0620))
- qemu_test.py运行推荐用-F加配置文件的方法，在测试配置文件中开启addDisk/multiMachine/addNic