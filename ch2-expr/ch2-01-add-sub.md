# 2.1. 加减法表达式

在前一节我们通过最小编译器将一个整数编译为可以返回相同状态码的程序。现在我们尝试将加法和减法的表达式编译为同样的程序。

比如有 `1+3-2` 表达式，手工编写对应的LLVM汇编程序如下：

```ll
define i32 @main() {
	; 1 + 3 - 2
	%t0 = add i32 0, 1   ; t0 = 1
	%t1 = add i32 %t0, 3 ; t1 = t0 + 3
	%t2 = sub i32 %t1, 2 ; t2 = t1 - 2
	ret i32 %t2
}
```

如果将输入的`1+3-2`转化为`[]string{"1", "+", "3", "-", "2"}` 形式，我们则可以通过以下代码输出对应的汇编程序：

```wa

func genAsm(tokens: []string) => string {
	sb: strings.Builder
	sb.WriteString("define i32 @main() {\n")

	idx: int
	for i, tok := range tokens {
		if i == 0 {
			t0 := "%t" + strconv.Itoa(idx)
			sb.WriteString("\t" + t0 + " = add i32 0, " + tokens[i] + "\n")
			continue
		}
		switch tok {
		case "+":
			idx++
			t0 := "%t" + strconv.Itoa(idx)
			t1 := "%t" + strconv.Itoa(idx-1)
			sb.WriteString("\t" + t0 + " = add i32 " + t1 + ", " + tokens[i+1] + "\n")
		case "-":
			idx++
			t0 := "%t" + strconv.Itoa(idx)
			t1 := "%t" + strconv.Itoa(idx-1)
			sb.WriteString("\t" + t0 + " = sub i32 " + t1 + ", " + tokens[i+1] + "\n")
		}
	}
	sb.WriteString("\tret i32 %t" + strconv.Itoa(idx) + "\n")
	sb.WriteString(`}`)

	return sb.String()
}
```

而如何将输入的字符串拆分为记号数组本质上属于词法分析的问题。我们先以最简单的方式实现：

```wa
func parseTokens(code: string) => (tokens: []string) {
	for code != "" {
		if idx := strings.IndexAny(code, "+-"); idx >= 0 {
			if idx > 0 {
				tokens = append(tokens, strings.TrimSpace(code[:idx]))
			}
			tokens = append(tokens, code[idx:][:1])
			code = code[idx+1:]
			continue
		}

		tokens = append(tokens, strings.TrimSpace(code))
		return
	}
	return
}
```

基本思路是通过 `strings.IndexAny(code, "+-")` 函数调用根据 `+-` 符号拆分，最终返回拆分后的词法列表。

然后对上个版本的compile函数稍加改造以支持加法和减法的运算表达式编译：

```wa
func compile(code: string) {
	tokens := parseTokens(code)
	output := genAsm(tokens)
	println(output)
}
```


更新main函数：

```wa
func main {
	compile(`1+2+3`)
}
```

通过以下命令执行：

```
$ wa run main.wa > _main.ll
$ clang -Wno-override-module -o a.out _main.ll
$ ./a.out || echo $?
6
```
