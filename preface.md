# 前言

## Why: 挖坑的起因

- 因为坑就在那里
- 挖坑的工具差不多齐全了
- 为了启动 [凹语言](https://github.com/wa-lang/wa) 的热身项目
- 凹语言项目已过5年, 完成了当初不做玩具车的目标, 是时候向凹语言迁移了
- ？

## What: µGo 例子

```go
import "libc"
import "libc.math" => m

const Pi = 3.14
const Pi_2 = Pi * 2

type MyInt :int

global x = println(1 + 2*(3+4) + -10 + double(50))

func println() => int

func main => int {}
```

## Output: 输出的目标格式

为了跨平台和方便测试，输出LLVM汇编代码，如果以后可能会增加WASM文件。
