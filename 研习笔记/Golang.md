只有简洁的设计，才能让软件持久的进化。
Go是编译型语言，PHP是解释型语言。
main函数是整个程序的入口。os.Args[0]是可执行文件的完整路径。

Go语言将数据类型分为四类：基础类型、复合类型、引用类型和接口类型。
基础类型： 数字、字符串和布尔型
复合数据类型：数组、结构体
引用类型：指针、切片、字典、函数、通道
接口类型：interface
内建常量: true false iota nil
内建类型: int int8 int16 int32 int64 uint uint8 uint16 uint32 uint64 uintptr float32 float64 complex128 complex64 bool byte rune string error
内建函数: make len cap new append copy close delete complex real imag panic recover
类型断言 `number,ok :=x.(int)`

类型别名转换
```
type X int
type Y = int

func main() {
	var m X = 100
	var n Y = 200
	a := int(m)
	a = Y
	fmt.Println(a)
}
```

sizeof总是在编译期求值。
unsafe.Sizeof(string) 永远都是 16
unsafe.Sizeof(slice)   永远都是 24
unsafe.Sizeof(map)   永远都是 8
unsafe.Sizeof(chan)   永远都是 8
unsafe.Sizeof(pointer)   永远都是 8
unsafe.Sizeof(interface)   永远都是 10
arr := [...]int{1,2,3,4,5}
fmt.Println(unsafe.Sizeof(arr)) //40，由数组长度决定

多变量赋值，先计算出全部右值，再依次赋值。

类型开关，断言与switch的配合：
```
switch v.(type) {
	case string:
}
```


**数组** 是由相同类型元素的集合组成的数据结构，数组初始化分配一块连续且固定的内存来保存其中的元素，默认为元素类型的零值。
数组2种初始化方式：
```
arr1 := [3]int{1, 2, 3}
arr2 := [...]int{1, 2, 3}
```

**切片** 用形如s[m:n]的形式来获取到一个slice的子集，遵循左开右闭，包括m元素，但不包括n元素，包含n-m个元素。
切片在运行时的结构：
```
type SliceHeader struct {
	Data uintptr
	Len  int
	Cap  int
}
```
Data 是指向数组的指针。
Len 是当前切片的长度。
Cap 是当前切片的容量，即 Data 数组的大小。
可以将切片理解成一片连续的内存空间加上长度与容量的标识。
当切片底层的数组长度不足时就会触发扩容，切片指向的数组可能会发生变化，不过在上层看来切片是没有变化的。
切片3种初始化方式：
```
arr := [3]int{1, 2, 3}
slice := arr[0:1]
slice := []int{1, 2, 3} // []int{}
slice := make([]int, 10) // make([]int, 0)
```
append自我赋值不用担心性能问题，Go已经做了优化。
切片扩容策略：
如果期望容量（append之后的容量）大于当前容量的两倍就会使用期望容量；
如果当前切片的长度小于 1024 就会将容量翻倍；
如果当前切片的长度大于 1024 就会每次增加 25% 的容量，直到新容量大于期望容量；
拷贝切片：`copy(a, b)`
在遇到大切片扩容或者拷贝时可能会发生大规模的内存拷贝，从而影响性能。
切片可以实现栈、队列、环(取模)。

**哈希** 字典 映射 MAP 键值对
map两种初始化方式：
m := map[int]string{}
m := make([int]string, 0)
读写性能好O(1)，哈希函数的选择在很大程度上能够决定哈希表的读写性能。
Go语言的map中所有的key都有相同的类型，所有的value也有着相同的类型。
哈希冲突：
开放地址法：数组
拉链法：链表
哈希在运行时的数据结构：
```
type hmap struct {
	count     int
	flags     uint8
	B         uint8
	noverflow uint16
	hash0     uint32

	buckets    unsafe.Pointer
	oldbuckets unsafe.Pointer
	nevacuate  uintptr

	extra *mapextra
}
```
对map进行range循环时，其迭代顺序是不确定的，因为map在扩容迁移后键值对的位置就发生了变化。
slice、map性能对比：
* 查找 Slice 中的一个元素 vs 查找 Map 中的一个元素：map快(键值都是元素值，空间换时间)
* 给定索引，查找元素：slice快
* 遍历 Slice vs 遍历 Map：slice快

使用内置的delete函数删除map元素，即使这些元素不在map中也不会报错：`delete(ages, "alice")`
禁止对map元素取址的原因是map可能随着元素数量的增长而重新分配更大的内存空间，从而可能导致之前的地址无效。
slice、map、chan、指针作为引用类型的零值是nil（因为是引用类型）。slice、map、chan、指针作为引用类型不能进行相等比较。

**字符串** 是一片连续的内存空间，是一个只读的字节切片类型，编码UTF-8，无\0结尾。
string可与[]rune或[]byte相互转换。
字符串运行时数据结构：
```
type StringHeader struct {
	Data uintptr
	Len  int
}
```

字符串与[]byte转换：
字符串和 []byte 中的内容虽然一样，但是字符串的内容是只读的，我们不能通过下标或者其他形式改变其中的数据，而 []byte 中的内容是可以读写的。
go格式化输出动词：%v %+v(打印结构体时，会添加字段名) %#v(相应值的类型的Go语法表示) %t %d %x %o %b %f %c(输出整数对应的Unicode字符) %s %q %T
**rune byte** Unicode字符rune类型是和int32等价的类型，通常用于表示一个Unicode码点。byte也是uint8类型的等价类型，byte类型一般用于强调数值是一个原始的数据而不是一个小的整数。
Go浮点计算存在精度丢失。

文本字符串通常被解释为采用UTF8编码的Unicode码点（rune）序列。字符串可以用==和<进行比较；比较通过逐个字节比较完成的，因此比较的结果是字符串自然编码的顺序。不变性意味如果两个字符串共享相同的底层数据的话也是安全的。

```
s := "Hello, 世界" 
fmt.Println(len(s)) // "13" 
fmt.Println(utf8.RuneCountInString(s)) // "9"
```
**bool** 布尔值并不会隐式转换为数字值0或1，反之亦然。

**接口** 的本质是引入一个新的中间层，解除上下游的耦合。
Go 语言中接口的实现都是隐式的。
我们使用结构体时并不关心它实现了哪些接口，Go 语言只会在传递参数、返回参数以及变量赋值时才会对某个类型是否实现接口进行检查。
接口也是 Go 语言中的一种类型，它能够出现在变量的定义、函数的入参和返回值中并对它们进行约束。
编译器检查：
|     | 结构体实现接口    | 结构体指针实现接口   |
| --- | --- | --- |
| 结构体初始化接口变量    |   通过  |   不通过  |
|结构体指针初始化接口变量|通过	|通过|


当我们使用指针实现接口时，只有指针类型的变量才会实现该接口；当我们使用结构体实现接口时，指针类型和结构体类型都会实现该接口。
Go 语言使用 runtime.iface 表示第一种接口，使用 runtime.eface 表示第二种不包含任何方法的接口（空接口） interface{}。interface{} 类型不是任意类型，赋值时其它类型会转化成interface{}类型。
空接口runtime.eface数据结构：
```
type eface struct { // 16 字节
	_type *_type
	data  unsafe.Pointer
}
```
接口runtime.iface数据类型：
```
type iface struct { // 16 字节
	tab  *itab
	data unsafe.Pointer
}
```

动态派发：
```
func main() {
	var c Duck = &Cat{Name: "draven"}
	c.Quack()
	c.(*Cat).Quack()
}
```
赋值给接口时，会复制目标对象。


**结构体** 是一种聚合的数据类型，可以对成员取地址，然后通过指针访问。
struct{}为可忽略值类型，不占内存，可用于map、chan、array，值无关紧要的场景。
Go结构体的标签，用于反射。
Go的结构体用组合的方式实现继承的效果。
Go语言的封装：通过属性和方法首字母大小写实现。
```
var employeeOfTheMonth *Employee = &dilbert 
employeeOfTheMonth.Position += " (proactive team player)"
// 相当于下面语句
(*employeeOfTheMonth).Position += " (proactive team player)"

值传递与引用传递的区别：调用函数返回的是值，并不是一个可取地址的变量
func EmployeeByID1(id int) *Employee { /* ... */ }
func EmployeeByID2(id int) Employee { /* ... */ }
EmployeeByID1(id).Salary = 0 // 正确
EmployeeByID2(id).Salary = 0 // 错误
```
一个命名为S的结构体类型将不能再包含S类型的成员：因为一个聚合的值不能包含它自身。但是S类型的结构体可以包含*S指针类型的成员。
你不能企图在外部包中用第一种顺序赋值的技巧来偷偷地初始化结构体中未导出的成员。
外层的结构体不仅仅是获得了匿名成员类型的所有成员，而且也获得了该类型导出的全部的方法。
结构体字面值必须遵循形状类型声明时的结构：
```
w = Wheel{Circle{Point{8, 8}, 5}, 20}
```
不管你的method的receiver是指针类型还是非指针类型，都是可以通过指针/非指针类型进行调用的，编译器会帮你做类型转换。

除接口、指针，其它的数据结构都能定义方法。

**指针** 指针是保存变量内存地址的整型变量。不同类型的指针不能互相转化，指针变量不能进行运算。支持二级指针，也就是C中指向指针的指针。
```
var a int = 100
var b int = 200
var p *int = &a
var pp **int = &p
*pp = &b

fmt.Printf("%#v\n", *p) // 200
fmt.Printf("%#v\n", **pp) // 200
```
内存地址是内存中每个字节单元的唯一编号，而指针是变量，需要分配内存空间。
make：初始化切片、哈希表和 Channel
new：根据传入的类型分配一片内存空间并返回指向这片内存空间的指针。表达式new(T)将创建一个T类型的匿名变量，其值为零值。
```
p := new(int) // p, *int 类型, 指向匿名的 int 变量
fmt.Println(*p) // "0"
*p = 2 // 设置 int 匿名变量的值为 2
fmt.Println(*p) // "2"
```
在项目中当遇到需要使用大量的带指针的数据结构并需要长时间保持引用的时候，需要注意可能会引发 GC 问题。

**函数**



赋值语句是显式的赋值形式，但是程序中还有很多地方会发生隐式的赋值行为。
Go 语言函数参数选择了传值的方式，无论是传递基本类型、结构体还是指针，都会对传递的参数进行拷贝。如果实参包括引用类型，如指针，slice(切片)、map、function、channel等类型，实参可能会由于函数的简介引用被修改。
在Go中，函数被看作第一类值：函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回。
如果两个函数形式参数列表和返回值列表中的变量类型一一对应，那么这两个函数被认为有相同的类型和标识符。形参和返回值的变量名不影响函数标识符。
匿名函数捕获迭代变量的陷阱：函数值中记录z的是循环变量的内存地址，而不是循环变量某一时刻的值。
```
var rmdirs []func()
for _, d := range tempDirs() {
	dir := d // 引入局部变量，作为循环变量的副本
	os.MkdirAll(dir, 0755)
	rmdirs = append(rmdirs, func() { os.RemoveAll(dir) })
}
```
可变参数，定义与使用示例：
```
func sum(vals...int) int
sum()
sum(1)
sum(1,2,3,4)
values := []int{1,2,3,4,5}
sum(values...) // 有机会改变values切片值
```
可变参数函数和以切片作为参数的函数形式上略有不同。

匿名函数可实现静态局部变量，不好用：
```
func test() func() []int {
	s := make([]int, 0)
	return func() []int {
		if len(s) == 0 {
			s = []int{1, 2, 3}
			fmt.Println("---")
		}
		return s
	}
}

func main() {
	f := test()
	f()
	f()
}
```

error接口：type error interface { Error() string }
errors.New("") 创建错误对象。
fmt.Errorf("xxx:%w", err) 格式化生成新的错误对象，构建错误链。
函数返回error为nil时，应当直接返回nil。因为接口类型和值都为nil，即(nil, nil)时才等于nil（var test interface{} = nil）。

**panic** 能够改变程序的控制流，调用 panic 后会立刻停止执行当前函数的剩余代码，并在当前 Goroutine 中递归执行调用方的 defer。
panic 只会触发当前 Goroutine 的 defer。
recover 可以中止 panic 造成的程序崩溃。它是一个只能在 defer 中发挥作用的函数，在其他作用域中调用不会发挥作用。
在runtime中包含一些无法捕获的异常，如内存溢出、map并发读写、栈内存耗尽等。

直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行，不论包含defer语句的函数是通过return正常结束，还是由于panic导致的异常结束。你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反（FILO）。
defer 被延迟执行的匿名函数甚至可以修改函数返回给调用者的返回值，前提是命名返回值。
导致panic异常的函数栈（包扩recover的函数）不会继续运行，但能正常返回零值。
遇到panic时，遍历本协程的defer链表，并执行defer。在执行defer过程中:遇到recover则停止向上层函数传递panic。如果没有遇到recover，遍历完本协程的defer链表后，向stderr抛出panic信息。

runtime.Goexit()：退出当前 goroutine，defer语句会照常执行。
os.Exit() 终止进程，不会执行defer语句。
程序多次调用 panic （defer中产生panic）也不会影响 defer 函数代码的运行，只有最后一个panic可以被revover捕获：
```
func main() {
	defer fmt.Println("in main")
	defer func() {
		if err := recover(); err != nil {
			fmt.Println(err)
		} else {
			fmt.Println("fatal")
		}
	}()
	defer func() {
		defer func() {
			panic("panic again and again")
		}()
		panic("panic again")
	}()
	defer func() {
		defer func() {
			panic("panic again and again2")
		}()
		panic("panic again2")
	}()

	panic("panic once")
}
```

在压栈function1的时候，预计算参数，需要连同函数地址、函数形参一同进栈：
```
func function(index int, value int) int {

    fmt.Println(index)

    return index
}
func main() {
    defer function(1, function(3, 0))
    defer function(2, function(4, 0))
}
// 输出 3 4 2 1
```




**for** 在for range遍历切片时增加、删除元素不会改变循环的执行次数。对于所有的 range 循环，Go 语言都会在编译期将原切片或者数组赋值给一个新变量 ha，又通过 len 关键字预先获取了切片的长度。
在for range循环中获取返回变量的地址都完全相同：
```
func main() {
	arr := []int{1, 2, 3}
	newArr := []*int{}
	for _, v := range arr {
		newArr = append(newArr, &v)
	}
	for _, v := range newArr {
		fmt.Println(*v)
	}
}
```
支持遍历的数据结构：数组、切片、string、map、chan

**select** 基于select的多路复用：每一个case代表一个通信操作(在某个channel上进行发送或者接收)，当条件满足时，select才会去通信并执行case之后的语句。
```
select {
case <-ch2:
case x := <-ch2:
case ch3 <- 100:
case <-time.After(3 * time.Second):
default:
}

ch := make(chan int, 1)
for i := 0; i < 10; i++ {
	select {
	case x := <-ch:
		fmt.Println(x)  // 0,2,4,6,8
	case ch <- i:
	}
}
```
select{} 永远阻塞
如果多个case同时就绪时，select会随机地选择一个执行，这样来保证每一个channel都有平等的被 select的机会。

**包**
Go程序初始化顺序：
导入包，当一个包被导入时，如果它还导入了其它的包，则先将其它的包包含进来，然后创建和初始化这个包的常量和变量。然后就是调用包里的init函数，如果一个包有多个init函数的话，实现可能是以文件名的顺序调用，同一个文件内的多个init则是以出现的顺序依次调用（init不是普通函数，可以定义有多个，所以不能被其它函数调用）。最终，在main包的所有包常量、包变量被创建和初始化，并且init函数被执行后，才会进入main.main函数，程序开始正常执行。
导入包的四种方式：
```
import (
	"math" // 默认：math.Sin()
	m "math" // 别名：m.Sin()
	. "math" // 简便：Sin()
	_ "math" // 初始化：无法引用，触发包的init()函数
)
```
内部包：导入路径包含internal关键字的包，只允许internal的父级目录及父级目录的子包导入，其它包无法导入。
这里main.go不能访问cpu.go、mem.go:
```
|-- resources
|   |-- internal
|   |   |-- cpu
|   |   |   |-- cpu.go
|   |   |-- mem
|   |       |-- mem.go
|   |-- input
|   |   |-- input.go
|   |-- board.go
|-- main.go
```

**Channel** 是协程之间的通信机制，先入先出FIFO的有锁管道。
```
type hchan struct {
   qcount   uint           // 循环队列元素个数
   dataqsiz uint           // 循环队列的大小
   buf      unsafe.Pointer // 循环队列的指针
   elemsize uint16         // chan中元素的大小
   closed   uint32         // 是否已close
   elemtype *_type         // chan中元素类型
   sendx    uint           // send在buffer中的索引
   recvx    uint           // recv在buffer中的索引
   recvq    waitq          // receiver的等待队列
   sendq    waitq          // sender的等待队列

   lock mutex              // 互拆锁，不是sync.Mutex
}
```

不要通过共享内存来通信，而应通过通信来共享内存。

类型 chan<- int 表示一个只发送int的channel，类型 <-chan int 表示一个只接收int的channel
Channel还支持close操作，用于关闭channel，随后对基于该channel的任何发送操作都将导致panic异常。对一个已经被close过的channel之行接收操作依然可以接受到之前已经成功发送的数据；如果channel中已经没有数据的话讲产生一个零值的数据（每次获取都是零值）。
`x, ok := <-ch // 通过ok判断管道是否关闭`
不管一个channel是否被关闭，当它没有被引用时将会被Go语言的垃圾自动回收器回收。关闭一个channels还会触发一个广播机制，广播发送零值。
一个基于无缓存Channels的发送操作将导致发送者goroutine阻塞，直到另一个goroutine在相同的Channels上执行接收操作。反之，如果接收操作先发生，那么接收者goroutine也将阻塞，直到有另一个goroutine在相同的Channels上执行发送操作。
同步Channels：基于无缓存Channels的发送和接收操作将导致两个goroutine做一次同步操作。
串联Channels（管道pipeline）：Channels也可以用于将多个goroutine串联在一起，一个Channels的输出作为下一个Channels的输入。

```
x := <-ch // ch关闭时，x获取零值
for x := range ch { } // ch关闭时，不会进入循环体内部，这与switch case不同
```
可以通过chan数组+协程数组实现任务按次序运行。
如果发送一直快于接收，或者接收一直快于发送，那么额外的缓存并没有任何好处。
只能通道赋值来初始化单向通道：
```
ch := make(chan int)
var send chan<-int = ch
var recv <-chan int = ch
```
通道并非用来取代锁。


**协程** 可以看成用户态线程。
协程池：创建固定数目的协程（或者根据并发量逐渐增加协程数目直到上限），消费channel里的数据。
我们使用了一个buffered channel作为一个计数信号量，来保证最多只有20个goroutine会同时执行HTTP请求。同理，我们可以用一个容量只有1的channel来保证最多只有一个goroutine在同一时刻访问一个共享变量。
当父协程是main协程时，父协程退出，父协程下的所有子协程也会跟着退出。当父协程不是main协程时，父协程退出，父协程下的所有子协程并不会跟着退出。
goroutines泄漏：channel没有接收者，无缓存的channel容易造成goroutines泄漏，泄漏的goroutines并不会被自动回收。
Go并发协程退出方式（核心：关闭channel会发送广播）：
1 使用for-range
通过关闭channel的方式，range能够感知channel的关闭，适用于协程只从1个channel读取数据时。
2 使用for-select !ok
2.1 如果某个通道关闭后，需要退出协程，直接return即可。
2.2 如果某个通道关闭了，还继续处理其他case，将该通道置为nil。select不会在nil的通道上进行等待。
3 使用for-select 退出通道
使用一个专门的通道，显式关闭通道可以处理主动通知协程退出。
4 使用for-select context
为每个子协程传递相同的上下文ctx即可，调用cancel()函数后该Context控制的所有子协程都会退出。
通过协程和管道可以实现生产者/消费者、发布/订阅。

**上下文** context.Context 是 Go 语言在 1.7 版本中引入标准库的接口1，该接口定义了四个需要实现的方法，其中包括：
Deadline — 返回 context.Context 被取消的时间，也就是完成工作的截止日期；
Done — 返回一个 Channel，这个 Channel 会在当前工作完成或者上下文被取消后关闭，多次调用 Done 方法会返回同一个 Channel；
Err — 返回 context.Context 结束的原因，它只会在 Done 方法对应的 Channel 关闭时返回非空的值；
如果 context.Context 被取消，会返回 Canceled 错误；
如果 context.Context 超时，会返回 DeadlineExceeded 错误；
Value — 从 context.Context 中获取键对应的值，对于同一个上下文来说，多次调用 Value 并传入相同的 Key 会返回相同的结果，该方法可以用来传递请求特定的数据；
Go Server 服务的每一个请求都是通过单独的 Goroutine 处理的，HTTP/RPC 请求的处理器会启动新的 Goroutine 访问数据库和其他服务。
当最上层的 Goroutine 因为某些原因执行失败时，正确地使用 context.Context，就可以在下层及时停掉无用的工作以减少额外资源的消耗。
```
func main() {
	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	defer cancel()
	go func() {
		go func() {
			select {
			case <-ctx.Done():
				fmt.Println("aa/aa")
			}
		}()
		select {
		case <-ctx.Done():
			fmt.Println("aa")
		}
	}()
	go func() {
		select {
		case <-ctx.Done():
			fmt.Println("bb")
		}
	}()
	go func() {
		select {
		case <-ctx.Done():
			fmt.Println("cc")
		}
	}()

	select {
	case <-time.After(time.Second * 2):
		fmt.Println("dd")
	}
}
```
// 全部都会打印，底层采用chan关闭时的广播通知。
ctx.Done()返回一个只读的channel，没有地方向这个 channel 里面写数据，在ctx.Cancel()时会关闭这个channel，select会立即读出零值。
context.Background() 和 context.TODO() 也只是互为别名，没有太大的差别，只是在使用和语义上稍有不同，每次调用都会返回新的context，永远不会被cancel。
父子之间传递cancel也是通过select done，真正的cancel函数包含锁、channel
Context有两个主要的功能：
1 通知子协程退出（正常退出，超时退出等）。context.WithCancel context.WithTimeout context.WithDeadline。当一个Context对象被取消时，继承自它的所有Context都会被取消。
2 传递必要的参数。context.WithValue
以Context作为参数的函数方法，应该把Context作为第一个参数，放在第一位。Context是协程安全的。
Context 不应该被存储在结构体类型中，而是传参给需要它的方法。

goroutine的创建是树状结构的，context也是一个树状结构。

```
type Options struct{ Interval time.Duration }
func reqTask(ctx context.Context, name string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("stop", name)
			return
		default:
			fmt.Println(name, "send request")
			op := ctx.Value("options").(*Options)
			time.Sleep(op.Interval * time.Second)
		}
	}
}
func main() {
	ctx, cancel := context.WithCancel(context.Background())
	vCtx := context.WithValue(ctx, "options", &Options{1})

	go reqTask(vCtx, "worker1")
	go reqTask(vCtx, "worker2")

	time.Sleep(3 * time.Second)
	cancel()
	time.Sleep(3 * time.Second)
}
```

**sync.Mutex**
内存同步：如果是多个goroutine都需要访问的变量，使用互斥条件来访问。

```
type Mutex struct {
	state int32
	sema  uint32
}
```
![](../images/mutex的state结构.png)


state 表示当前互斥锁的状态，而 sema 是用于控制锁状态的信号量。
公平锁
锁有两种模式：正常模式和饥饿模式。
在正常模式下，所有的等待锁的goroutine都会存在一个先进先出的队列中（轮流被唤醒）。但是一个被唤醒的goroutine并不是直接获得锁，而是仍然需要和那些新请求锁的（new arrivial）的goroutine竞争，而这其实是不公平的，因为新请求锁的goroutine有一个优势——它们正在CPU上运行，并且数量可能会很多。所以一个被唤醒的goroutine拿到锁的概率是很小的。在这种情况下，这个被唤醒的goroutine会加入到队列的头部。
如果一个等待的goroutine有超过1ms（写死在代码中）都没获取到锁，那么就会把锁转变为饥饿模式。在饥饿模式中，锁的所有权会直接从释放锁(unlock)的goroutine转交给队列头的goroutine，新请求锁的goroutine就算锁是空闲状态也不会去获取锁，并且也不会尝试自旋。它们只是排到队列的尾部。

如果一个goroutine获取到了锁之后，它会判断以下两种情况：
1. 它是队列中最后一个goroutine；
2. 它拿到锁所花的时间小于1ms；


以上只要有一个成立，它就会把锁转变回正常模式。
正常模式会有比较好的性能。饥饿模式对于防止尾部延迟来说非常的重要。
sync.Mutex.Lock()函数内部是个for循环不断轮询获取锁。
自旋是一种多线程同步机制，当前的线程在进入自旋的过程中会一直保持 CPU 的占用。


**sync.Cond**
**条件变量** 可以让一组的 Goroutine 都在满足特定条件时被唤醒。
使用方法：c.L.Lock() 、c.L.Unlock()、c.Wait()、c.Broadcast()、c.Signal()
原理：调用 Wait 会自动释放锁 c.L，并挂起调用者所在的 goroutine（释放CPU，提高性能），因此当前协程会阻塞在 Wait 方法调用的地方。如果其他协程调用了 Signal 或 Broadcast 唤醒了该协程，那么 Wait 方法在结束阻塞时，会重新给 c.L 加锁，并且继续执行 Wait 后面的代码。使用了`for !condition()`而非`if`，是因为当前协程被唤醒时，条件不一定符合，需要再次 Wait 等待下次被唤醒。条件判断的作用是避免先唤醒，再等待的情况（协程永远等待下去）。

sync.Cond.Signal 方法会唤醒队列最前面、等待最久的 Goroutine；
sync.Cond.Broadcast 方法会唤醒队列中全部的 Goroutine；

```
var status int64

func main() {
	c := sync.NewCond(&sync.Mutex{})
	for i := 0; i < 10; i++ {
		go listen(c)
	}
	time.Sleep(1 * time.Second)
	go broadcast(c)

	ch := make(chan os.Signal, 1)
	signal.Notify(ch, os.Interrupt)
	<-ch
}

func broadcast(c *sync.Cond) {
	c.L.Lock()
	atomic.StoreInt64(&status, 1)
	c.Broadcast()
	c.L.Unlock()
}

func listen(c *sync.Cond) {
	c.L.Lock()
	for atomic.LoadInt64(&status) != 1 {
		c.Wait()
	}
	fmt.Println("listen")
	c.L.Unlock()
}
```

**sync.atomic**
原子操作是 CPU 的能力，与操作系统无关。原子操作不会被打断。
atomic原子操作主要用于并发环境下，无须加锁对整数进行安全的加减、比较、读取操作。

```

func StoreT(addr *T, val T) // 原子性的存储
func AddT(addr *T, delta T)(new T) // 原子性的相加，并返回新值
func LoadT(addr *T) (val T) // 原子性的获取

func SwapT(addr *T, new T) (old T) //原子性的存储并返回旧值
func CompareAndSwapT(addr *T, old, new T) (swapped bool) //原子性的比较*addr和old，如果相同则将new赋值给*addr并返回true
```
**sync.RWMutex**、 **sync.Map**、 **sync.WaitGroup**
```
var mu2 sync.RWMutex
mu2.RLock()
mu2.RUnlock()
mu2.WLock()
mu2.WUnlock()
// sync.Map直接使用方法，不用实例化对象
sync.Map. Store() Load() Delete() Range() // 遍历不能用for range
sync.WaitGroup Add() Done() Wait()
```



**sync.Once** 初始化，一个once的Do(func)只会执行一次（与协程无关），即使func变化。可以实现单例模式。
```
func main() {
   o := &sync.Once{}
   o.Do(do)

   go func() {
      o.Do(do)
   }()
   time.Sleep(time.Second)
}

func do() {
   fmt.Println("only once")
}
```


**singleflight.Group** 防缓存穿透利器，Do(key,func())同一时间只能执行一个函数。
```
var g singleflight.Group
func load(key string) (string, error) {
   data, err := loadFromCache(key)
   if err != nil && err == ErrCacheMiss {
      // 利用 singleflight 来归并请求
      v, err, _ := g.Do(key, func() (interface{}, error) {
         data, err := loadFromDB(key)
         if err != nil {
            return nil, err
         }
         setCache(key, data)
         return data, nil
      })
      if err != nil {
         log.Println(err)
         return "", err
      }
      data = v.(string)
   }
   return data, nil
}
```

**sync.Pool** 可以作为保存临时取还对象的一个“池子”，可以复用对象，无需重新分配，减轻 GC 的压力。New、Get()、Put()
Get是随机的，Put归还对象时，将对象“清空”。只进行 Get 操作的话，就相当于一直在生成新的对象。使用时，先put合适数量的对象。
使用场景：临时对象池、协程池、连接池等。

```
var pool *sync.Pool

type Person struct {
   Name string
}

func init() {
   pool = &sync.Pool{
      New: func() interface{} {
         fmt.Println("creating a new person")
         return new(Person)
      },
   }
}
func main() {
   person := pool.Get().(*Person)
   fmt.Println("Get Pool Object：", person)
   person.Name = "first"
   pool.Put(person)
   fmt.Println("Get Pool Object：", pool.Get().(*Person))
   fmt.Println("Get Pool Object：", pool.Get().(*Person))
}
```
**定时器**
完全二叉树：若设二叉树的深度为h，除第 h 层外，其它各层 (1～h-1) 的结点数都达到最大个数，第 h 层所有的结点都连续集中在最左边。
最小堆：是一种完全二叉树，任一节点均小于等于它的左右子节点，位于堆顶节点的值最小。
计时器以最小堆的形式存储P中，runtime.runtimer里面会启动一个for循环，不停的检查P的timer列表的第一个元素的状态。
```
type timer struct {
    // 对应处理器P的指针
    pp puintptr 
    // 定时器被唤醒的时间
    when   int64
    // 唤醒的间隔时间
    period int64
    // 唤醒时被调用的函数
    f      func(interface{}, uintptr)
    // 被调用的函数的参数
    arg    interface{} 
    seq    uintptr
    // 处于timerModifiedXX状态时用于设置when字段 
    nextwhen int64 
    // 定时器的状态
    status uint32
}
```


**反射**：在运行时更新变量和检查它们的值、调用它们的方法和它们支持的内在操作，但是在编译时并不知道这些变量的具体类型。
反射主要作用：减少代码，在很多框架中用到，缺点：代码不好理解、性能差一两个数量级。
反射包两个重要的类型：reflect.Type 和 reflect.Value
反射包两个重要的函数：reflect.TypeOf(i interface{}) 获取reflect.Type、reflect.ValueOf(i interface{})获取reflect.Value
反射三大法则：
从 interface{} 变量可以反射出反射对象。reflect.ValueOf(i interface{})方法。
从反射对象可以获取 interface{} 变量。reflect.Value.Interface()方法。
要修改反射对象，其值必须可设置。
从反射对象到接口值的过程是从接口值到反射对象的镜面过程，两个过程都需要经历两次转换：
从接口值到反射对象：
	从基本类型到接口类型的类型转换；
	从接口类型到反射对象的转换；
从反射对象到接口值：
	反射对象转换成接口类型；
	通过显式类型转换变成原始类型；
由于 Go 语言的函数调用都是值传递的，所以要先获取指针对应的 reflect.Value，再通过 reflect.Value.Elem 方法得到可以被设置的变量：
```
func main() {
	i := 1
	v := reflect.ValueOf(&i)
	v.Elem().SetInt(10)
	fmt.Println(i)
}
```
调用 reflect.ValueOf 获取变量指针；
调用 reflect.Value.Elem 获取指针指向的变量；
调用 reflect.Value.SetInt 更新变量的值；
获取反射类型：reflect.Value.Kind()
调用函数：reflect.Value.Call() 
调用方法：
reflect.Value.NumMethod()、reflect.Value.MethodByName(reflect.Type.Method(i).Name).Call()
获取结构体字段：
reflect.Value.NumField()、reflect.Type.Field(i).Name

线程由 CPU 调度抢占式，协程是用户态协作式，一个 goroutine 最多占用 CPU 10ms。
Go的调度器通过使用与 CPU 数量相等的线程减少线程频繁切换的内存开销和锁竞争，在每一个线程上执行内存消耗更低、上下文切换耗时更低的 Goroutine 来提升性能。
**GMP**
Go 调度本质是把大量的 goroutine 分配到少量线程上去执行，并利用多核并行，实现更强大的并发。

* G — 表示 Goroutine，可以把一个 G 当做一个任务，当 Goroutine 被调离 CPU 时，调度器代码负责把 CPU 寄存器的值保存在 G 对象的成员变量之中，当 Goroutine 被调度起来运行时，调度器代码又负责把 G 对象的成员变量所保存的寄存器的值恢复到 CPU 的寄存器。
* M — 表示内核线程，M 结构体对象除了记录着工作线程的诸如栈的起止位置、当前正在执行的Goroutine 以及是否空闲等等状态信息之外，还通过指针维持着与 P 结构体的实例对象之间的绑定关系。
* P — 代表一个虚拟的 Processor 处理器。线程想运行任务就得获取 P，从 P 的本地队列获取 G，P 队列为空时，M 也会尝试从全局队列拿一批 G 放到 P 的本地队列，或从其他 P 的本地队列偷一半放到自己 P 的本地队列。通过处理器 P 的调度，每一个内核线程都能够执行多个 Goroutine，它能在 Goroutine 进行一些 I/O 操作时及时让出计算资源，提高线程的利用率。

Goroutine 在 Go 语言运行时使用runtime.g结构体 runtime.g 表示，这个结构体非常复杂。


每一个线程都对应一个运行时中的 runtime.m 结构体：
```
type m struct {
	g0   *g
	curg *g
	...
}
```

调度器最多可以创建 10000 个线程，但是其中大多数的线程都不会执行用户代码（可能陷入系统调用），最多只会有 GOMAXPROCS 个活跃线程能够正常运行。
在默认情况下，运行时会将 GOMAXPROCS 设置成当前机器的核数，也就是会创建GOMAXPROCS个调度器P。
runtime.p 是处理器的运行时表示。
```
type p struct {
	m           muintptr

	runqhead uint32
	runqtail uint32
	runq     [256]guintptr // 任务队列
	runnext guintptr // 下一个任务
	...
}
```

![](../images/Ugu3C2WSpM.jpeg)


P 何时创建：在确定了 P 的最大数量 n 后，运行时系统会根据这个数量创建 n 个 P。
M 何时创建：没有足够的 M 来关联 P 并运行其中的可运行的 G。比如所有的 M 此时都阻塞住了，而 P 中还有很多就绪任务，就会去寻找空闲的 M，而没有空闲的，就会去创建新的 M。

`逻辑CPU数=CPU个数*核数*超线程数`



并发：不确定执行的顺序。
并行：多核时并行才有可能。

**内存管理**
程序和操作系统实际操作的都是`虚拟内存`，最终由CPU通过内存管理单元`MMU`(Memory Manage Unit)把虚拟内存的地址转化为实际的物理内存地址。
栈，是一种连续储存的数据结构，具有先进后出的性质，栈内存空间由操作系统自动分配和释放，有大小限制。
堆，是一种不连续的数据结构，由用户手动申请和释放，几乎没有大小限制。

Go通过 escape analysis 来分配内存，通过 garbage collection（gc）来回收内存。程序有两种内存，一种是栈(stack)，一种是堆（heap），所有的堆内数据都被 GC 管理。栈内存连续线性分配，进栈出栈方式分配和释放内存，简单高效。越多的值在栈，程序运行越快。
分配堆的情况：
* 1 goloang只会把函数中确定不在函数结束后使用的变量放到栈，否则就会放到堆。
* 2 编译器确定值太大而无法放入栈。
* 3 编译器在编译的时候无法得知这个值的具体大小。


`TCMalloc`全称Thread Cache Memory alloc线程缓存内存分配器。
Go的内存管理器是基于Google自身开源的TCMalloc内存分配器为理念设计和实现的。

逻辑上为：
* mcache：线程缓存
* mcentral：中央缓存
* mheap：堆内存
![](../images/6401.png)


操作系统是按`page`管理内存的，同样Go语言也是也是按page管理内存的，1page为8KB，保证了和操作系统一致。Go内存管理单元`mspan`通常由N个且连续的page组成。相同page数目的`mspan`之间还可以构成mspan链表。
![](../images/6402.png)

mspan会被拆解成粒度更小的object，object和object之间构成一个FreeList链表。object大小由sizeclass决定的，它是个尺寸数组，一共包含 67 种跨度（0~32kb）。在mspan中，用spanclass表示sizeclass，它是个uint8。Go内存管理单元mspan被分为了两类：
第一类：需要垃圾回收扫描的mspan，简称scan，sizeclass最后一位为0。
第二类：不需要垃圾回收扫描的mspan，简称noscan，sizeclass最后一位为1。

![](../images/6403.png)
微对象和小对象（<=32kb）通过`mcache`分配，大对象(>32kb)直接从`mheap`中分配。如果对应的大小规格在 mcache 中没有可用的块，则向 mcentral 申请，如果 mcentral 中没有可用的块，则向 mheap 申请，如果 mheap 中没有可用 span，则向操作系统申请一系列新的页arena。mcache和mcentral持有mspan，mheap持有mcentral和arena（由page组成）。访问中心缓存需要使用互斥锁。




![](../images/2020-02-29-15829868066479-go-memory-layout.png)




**垃圾回收**（Garbage Collection，缩写为GC），追踪法
根对象一般包括全局变量和栈变量。
标记清除，go1.3：
标记，遍历整个堆，从根对象出发将可达的变量做上标记。
清除，遍历整个堆，将未作标记的变量清除。
执行标记清除时，会暂停整个程序（Stop the world，STW）。

三色标记，go1.5:
通过三个阶段的标记来确定清除的变量都有哪些。
1 新创建的变量为白色。
2 GC开始，遍历根变量，将根变量置为灰色。
3 遍历灰色集合，将灰色变量的下一层变量置为灰色，当前变量由灰色置为黑色。
4 递归重复第3个步骤，直到灰色中无任何变量，此时只有黑色和白色变量。
5 回收白色变量。
三色标记是标记删除的升级版，可以异步执行，波次推进。
三色标记解决了STW，但是会出现悬挂指针，误删除变量的问题。
强三色不变式：黑色下面不能有白色。
弱三色不变式：黑色下面有白色，但是这个白色同时被灰色保护（父辈有灰色）。
插入屏障（强三色不变式）：在 A 对象引用 B 对象的时候，B 对象如果是白色，将会被被标记为灰色。结束时需要STW来重新扫描栈。

混合写屏障机制（弱三色不变式），go1.8:
1、GC开始将栈上的对象全部扫描并标记为黑色(之后不再进行第二次重复扫描，无需STW)
2、GC期间，任何在栈上创建的新对象，均为黑色。
3、堆上被删除的对象标记为灰色。
4、堆上被添加的对象标记为灰色。

**经验与理论**

竞态条件：多线程同时读写共享资源。
临界区：读写共享资源的代码片段。
互斥锁：同一时刻，只有一个线程能进入临界区。
读写锁：写独占临界区，读共享。
信号量：允许指定数目的线程进入临界区。
悲观锁：互斥锁：写锁
乐观锁：无锁，会有失败的情况：select num as num_1 from user where id =1;update user set name='zhangsan',num=(num+1) where id=1 and num=num_1;






写代码时永远不要把性能作为第一优先级，因为你也不想猜测性能。写正确的代码才是最高优先级，这意味着我们首要关注完整性、可读性和简洁。当你有了可执行的程序，再来确定程序是否足够快。如果确实不够快，用语言提供的工具来找到并修复性能问题。

最难搞的 bug 是你的思维就有问题，所以根本看不到问题。








在考虑并发的时候，有两种很重要的工作负载：
CPU 密集型：Goroutine 从不处于等待状态，就是不停地在计算。算 π 就是。需要并行的并发。
IO 密集型：会使 Goroutine 自然地进入等待状态，或是通过网络访问资源、或是进行系统调用、或是等待事件。并不需要并行的并发。
数组求和适合并行的并发。并不是所有 CPU 密集型的工作负载都适合并发的，比如排序，主要是分工或将所有结果组合起来的成本很高的情况下。
C和C++没有垃圾回收机制，由工程师自行处理。Go 以及 Java 等编程语言会由工程师和GC共同管理。

>寄存器
>1级缓存
>2级缓存
>3级缓存
>内存
>固态硬盘
>机械硬盘
>网络/网盘

静态库和动态库的优缺点也比较明显；只依赖静态库并且通过静态链接生成的二进制文件因为包含了全部的依赖，所以能够独立执行，但是编译的结果也比较大；而动态库可以在多个可执行文件之间共享，可以减少内存的占用，其链接的过程往往也都是在装载或者运行期间触发的，所以可以包含一些可以热插拔的模块并降低内存的占用。
守护进程是在后台运行的计算机程序，不由用户直接操作。