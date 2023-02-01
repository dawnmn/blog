可以绕过 Go 语言的类型系统，直接操作内存。
```
func Alignof(x ArbitraryType) uintptr // 返回变量对齐字节数量
func Offsetof(x ArbitraryType) uintptr // 返回变量指定属性的偏移量
func Sizeof(x ArbitraryType) uintptr // 返回变量在内存中占用的字节数
type ArbitraryType int
type Pointer *ArbitraryType
```