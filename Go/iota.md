**iota只能在const的表达式中**
```
fmt.Println(iota)  
//编译错误： undefined: iota
```
**每次const，iota都会置零**
```
const a = iota // 0
const (
	b = iota // 0
	c        // 1
)
```
