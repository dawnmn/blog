示例：
```
var name string
var age int
flag.StringVar(&name, "name", "", "名字")
flag.IntVar(&age, "age", 0, "年龄")
work := flag.Bool("work", false, "是否就业")

flag.Parse()
fmt.Printf("名字：%s 年龄：%d 是否就业：%t, %#v, %d, %d ", name, age, *work, flag.Args(), flag.NArg(), flag.NFlag())
// go run main.go -name=小明 -age=12 -work=false money=0
```