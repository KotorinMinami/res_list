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

在运行该脚本之前，需要先使用 mugen 完成测试，即 mugen 的目录下要有 `logs` 和 `logs_failed` 两个文件夹。然后再将 mugen 的路径作为脚本的第一个参数：

```shell-session
$ python3 result_parser.py <mugen directory>
``` 

运行之后，结果文件将在 mugen 的目录下生成。
