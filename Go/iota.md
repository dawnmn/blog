**规则**
iota在每个const中从0值开始，每一行自增1。
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
**允许跳过值**
```
type AudioOutput int

const (
	OutMute   AudioOutput = iota // 0
	OutMono                      // 1
	OutStereo                    // 2
	_
	_
	OutSurround // 5
)
```
**位掩码**
```
// 二进制位运算
type AudioOutput int

const (
	mutexLocked      = 1 << iota // 00000001 1
	mutexWoken                   // 00000010 2
	mutexStarving                // 00000100 4
	mutexWaiterShift = iota      // 3
)
```
**定义数量级**
```
type ByteSize int64

const (
	_           = iota             // 忽略0值
	KB ByteSize = 1 << (10 * iota) // 1 << (10*1)
	MB                             // 1 << (10*2)
	GB                             // 1 << (10*3)
	TB                             // 1 << (10*4)
	PB                             // 1 << (10*5)
	EB                             // 1 << (10*6)
)
```
**中间插队**
```
const (
	i = iota // 0
	j = 3.14 // 3.14
	k = iota // 2
	l        // 3
)
```

