# mugen-riscv log 结果处理

- `mugen-riscv` 文件夹存放 mugen 在 RISC-V 下运行的结果，包括 `logs` 与 `logs_failed` 文件夹。
- `mugen-x86` 文件夹存放 mugen 在 x86 下运行结果，内容同上。

运行时运行 `res_list.py` 文件。

```shell-session
$ python3 res_list.py
```

## 自动生成结果文件

在 mugen 测试结束之后，`result_parser.py` 可以解析测试结果，生成 `result.csv`、`results.json` 两个测试结果文件；并分析测试失败的原因，生成 `failedCause.csv` 文件。

### 用法

在运行之前，需要将 mugen 目录下的 `logs` 和 `logs_failed` 文件夹复制到脚本目录下的 `mugen-riscv` 文件夹。然后，使用运行脚本：

```shell-session
$ python3 result_parser.py
```

运行之后，结果文件将在 `mugen-riscv` 目录下生成。
