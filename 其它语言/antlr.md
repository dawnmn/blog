**编译原理**
`DSL`：Domain Specific Language，领域特定语言。
`编译器`：把源语言代码翻译成一个等价的目标机器语言代码。
`解释器`：执行用户提供的代码。

编译器的前端一般承担着词法分析、语法分析、类型检查和中间代码生成几部分工作，而编译器后端主要负责目标代码的生成和优化，也就是将中间代码翻译成目标机器能够运行的二进制机器码。
Go的编译器在逻辑上可以被分成四个阶段：词法与语法分析、类型检查和AST转换、通用SSA生成和最后的机器代码。

`词法分析`：将文件中的字符串序列（代码）转换成`Token`序列。
`词法分析器`：`lexer`
`语法分析`：把Token序列转换成有意义的结构体，通常是一个`抽象语法树`。
`语法分析器`：`parser`
`AST` 抽象语法树：用树状的方式表示编程语言的语法结构，剔除了空格、分号、括号等。
每个 Go 源代码文件最终都会被解析成一个独立的抽象语法树。
`上下文无关文法`(Context-Free Grammar，CFG)，产生式规则左侧仅有一个非终结符号。

`+ - * / `左结合，`= != ==` 右结合

**antlr4 windows 安装**
```
pip
pip install antlr4-tools
antlr4
```
`antlr4-4.13.1-complete.jar`所在目录：
`C:\Users\xxx\.m2\repository\org\antlr\antlr4\4.13.1\antlr4-4.13.1-complete.jar`
`antlr4.exe`、`antlr4-parse.exe`所在目录：
`C:\Users\xxx\AppData\Local\Programs\Python\Python311\Scripts`
操作系统环境变量`CLASSPATH`，增加`C:\Users\xxx\.m2\repository\org\antlr\antlr4\4.13.1\antlr4-4.13.1-complete.jar`
**Hello.g4**
```
grammar Hello;
r : 'hello' ID ;
ID: [a-z]+ ;
WS: [ \t\r\n]+ -> skip ;
```
```
# 语法解析，生成parser目录
antlr4 -o parserJava Hello.g4
cd parserJava
javac *.java

grun Hello r -gui
# 输入以下内容，回车，ctrl+z，回车
hello parrt

grun Hello r -tree
grun Hello r -gui
```
**CSV.g4**
```
grammar CSV;
csvFile: hdr row+ ;
hdr : row ;
row : field (',' field)* '\r'? '\n' ;
field
    : TEXT
    | STRING
    |
    ;
TEXT   : ~[,\n\r"]+ ;
STRING : '"' ('""'|~'"')* '"' ; // quote-quote is an escaped quote
```
```
antlr4 -o parserCsv CSV.g4
cd parserCsv
javac *.java
```
a.csv
```
编号,内容,分数
20,hello,9.6
30,你好,19.6
```
```
grun CSV csvFile a.csv -gui

cd ..

# 编译成go
antlr4 -Dlanguage=Go -o parser CSV.g4
go get github.com/antlr4-go/antlr/v4
```
run.go，运行Run函数：
```
package parser

import (
	"fmt"
	"github.com/antlr4-go/antlr/v4"
	"strings"
)

type CSVMapListener struct {
	*BaseCSVListener
	headers []string
	cm      []map[string]string
	fields  []string // a slice of fields in current row
}

func (cl *CSVMapListener) lastHeader(header string) bool {
	return header == cl.headers[len(cl.headers)-1]
}

func (cl *CSVMapListener) String() string {
	var s strings.Builder
	s.WriteString("[")

	for i, m := range cl.cm {
		s.WriteString("{")
		for _, h := range cl.headers {
			s.WriteString(fmt.Sprintf("%s=%v", h, m[h]))
			if !cl.lastHeader(h) {
				s.WriteString(", ")
			}
		}
		s.WriteString("}")
		if i != len(cl.cm)-1 {
			s.WriteString(",\n")
			continue
		}
	}
	s.WriteString("]")
	return s.String()
}

/* for debug
func (this *CSVMapListener) EnterEveryRule(ctx antlr.ParserRuleContext) {
	fmt.Println(ctx.GetText())
}
*/

func (cl *CSVMapListener) ExitHdr(c *HdrContext) {
	cl.headers = cl.fields
}

func (cl *CSVMapListener) ExitField(c *FieldContext) {
	cl.fields = append(cl.fields, c.GetText())
}

func (cl *CSVMapListener) EnterRow(c *RowContext) {
	cl.fields = []string{} // create a new field slice
}

func (cl *CSVMapListener) ExitRow(c *RowContext) {
	// get the rule index of parent context
	if i, ok := c.GetParent().(antlr.RuleContext); ok {
		if i.GetRuleIndex() == CSVParserRULE_hdr {
			// ignore this row
			return
		}
	}

	// it is a data row
	m := map[string]string{}

	for i, h := range cl.headers {
		m[h] = cl.fields[i]
	}
	cl.cm = append(cl.cm, m)
}

func Run(csvFile string) {
	is, err := antlr.NewFileStream(csvFile)
	if err != nil {
		fmt.Printf("new file stream error: %s\n", err)
		return
	}

	// Create the Lexer
	lexer := NewCSVLexer(is)
	stream := antlr.NewCommonTokenStream(lexer, antlr.TokenDefaultChannel)

	// Create the Parser
	p := NewCSVParser(stream)

	// Finally parse the expression
	l := &CSVMapListener{}
	antlr.ParseTreeWalkerDefault.Walk(l, p.CsvFile())
	fmt.Printf("%s\n", l.String())
}
```
**Expr.g4**
```
grammar Expr;
prog: stat+;
stat: expr NEWLINE  # printExpr
    | ID '=' expr NEWLINE # assign
    | NEWLINE # blank
    ;
expr: expr ('*'|'/') expr # MulDiv
    | expr ('+'|'-') expr # AddSub
    | INT # int
    | ID # id
    | '(' expr ')' # parents
    ;
ID: [a-zA-Z]+;
INT: [0-9]+;
NEWLINE: '\r'? '\n';
WS: [ \t]+ -> skip ;
MUL:'*';
DIV:'/';
ADD:'+';
SUB:'-';
```
```
antlr4 -Dlanguage=Go -o parser -no-listener -visitor Expr.g4
```




