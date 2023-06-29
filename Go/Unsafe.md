可以绕过 Go 语言的类型系统，直接操作内存。
```
func Alignof(x ArbitraryType) uintptr // 返回变量对齐字节数量
func Offsetof(x ArbitraryType) uintptr // 返回变量指定属性的偏移量
func Sizeof(x ArbitraryType) uintptr // 返回变量在内存中占用的字节数
type ArbitraryType int
type Pointer *ArbitraryType
```
`unsafe.Pointer`：通用指针类型，用于转换不同类型的指针，不能进行指针运算，不能读取内存存储的值（必须转换到某一类型的普通指针）。GC不会回收unsafe.Pointer的内存。
`uintptr`：用于指针运算，GC不把 uintptr 当指针，uintptr 无法持有对象。uintptr 类型的目标会被回收。
**byte数组与struct转换**
```
type a struct {
	seq    int64
	status int8
}

const size = unsafe.Sizeof(a{})

func main() {
	v := a{
		seq:    1024,
		status: 2,
	}

	var data *[size]byte
	data = (*[size]byte)(unsafe.Pointer(&v))
	fmt.Println(data) // 0 4 0 0 0 0 0 0 2 0 0 0 0 0 0 0

	var p *a
	p = (*a)(unsafe.Pointer(data))
	fmt.Println(*p) // 1024 2

	pp := (*int8)(unsafe.Pointer(uintptr(unsafe.Pointer(&v)) + uintptr(8)))
	fmt.Println(*pp) // 2
}
```
**超大byte数组**
```
var b = (*[0xFFFFFFFFFFFF]byte)(unsafe.Pointer(&[10]byte{}))
fmt.Println((*b)[0])   // 0，在声明的数组范围内
fmt.Println((*b)[100]) // 102，超过声明的数组范围，值不确定
```