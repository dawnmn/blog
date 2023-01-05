**io**
```
Reader Writer Closer ReadWriteCloser
io.copy()
```
io.EOF变量的定义：var EOF = errors.New("EOF")，是 error 类型。
```
# Read 从基本数据流中读取 len(p) 个字节到 p 中，当 Read 在成功读取 n > 0 个字节后遇到一个错误或 EOF后返回
type Reader interface {
    Read(p []byte) (n int, err error)
}
# Write 将 len(p) 个字节从 p 中写入到基本数据流中
type Writer interface {
    Write(p []byte) (n int, err error)
}
type ReaderAt interface {
    ReadAt(p []byte, off int64) (n int, err error)
}
type WriterAt interface {
    WriteAt(p []byte, off int64) (n int, err error)
}
# ReadFrom 从 r 中读取数据，直到 EOF 或发生错误。
type ReaderFrom interface {
    ReadFrom(r Reader) (n int64, err error)
}
# WriteTo 将数据写入 w 中
type WriterTo interface {
    WriteTo(w Writer) (n int64, err error)
}
type Closer interface {
    Close() error
}
type ByteReader interface {
    ReadByte() (c byte, err error)
}
type ByteWriter interface {
    WriteByte(c byte) error
}
type StringWriter interface {
	WriteString(s string) (n int, err error)
}

func Copy(dst Writer, src Reader) (written int64, err error)
# CopyN 将 n 个字节(或到一个error)从 src 复制到 dst。
func CopyN(dst Writer, src Reader, n int64) (written int64, err error)
# WriteString 将s的内容写入w中
func WriteString(w Writer, s string) (n int, err error)
# MultiReader 只是逻辑上将n个 Reader 组合起来，需要调用n次 Read 方法获取所有 Reader 的内容。在所有的 Reader 内容都被读完后，Read 会返回 EOF。
func MultiReader(readers ...Reader) Reader
func MultiWriter(writers ...Writer) Writer
func ReadAll(r Reader) ([]byte, error)

io.Discard变量，实现了io.Writer接口，空写入
```
MultiReader示例
```
func main() {
	readers := []io.Reader{
		strings.NewReader("from strings reader"),
		bytes.NewBufferString("from bytes buffer"),
	}
	reader := io.MultiReader(readers...)
	data := make([]byte, 0, 128)
	buf := make([]byte, 10)

	for n, err := reader.Read(buf); err != io.EOF; n, err = reader.Read(buf) {
		if err != nil {
			panic(err)
		}
		data = append(data, buf[:n]...)
	}
	fmt.Printf("%s\n", data)
}
```
MultiWriter示例
```
func main() {
	file, err := os.Create("tmp.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()
	writers := []io.Writer{
		file,
		os.Stdout,
	}
	writer := io.MultiWriter(writers...)
	writer.Write([]byte("Go语言中文网"))
}
```

```
os.File 同时实现了io.Reader和io.Writer
在Linux中，有3种标准流 (standard stream)，分别为stdin,stdout, stderr。
在go语言中，Stdin、Stdout、Stderr都是os.File的实例，每个进程都各有一个Stdin、Stdout、Stderr文件描述符。Stdout、Stderr基本相同。

```

**os**
打开方式：
```
func OpenFile(name string, flag int, perm FileMode) (*File, error)
const (
    O_RDONLY int = syscall.O_RDONLY // 只读模式打开文件
    O_WRONLY int = syscall.O_WRONLY // 只写模式打开文件
    O_RDWR   int = syscall.O_RDWR   // 读写模式打开文件
    O_APPEND int = syscall.O_APPEND // 写操作时将数据附加到文件尾部
    O_CREATE int = syscall.O_CREAT  // 如果不存在将创建一个新文件
    O_EXCL   int = syscall.O_EXCL   // 和 O_CREATE 配合使用，文件必须不存在
    O_SYNC   int = syscall.O_SYNC   // 打开文件用于同步 I/O
    O_TRUNC  int = syscall.O_TRUNC  // 如果可能，打开时清空文件
)
func Open(name string) (*File, error) {
    return OpenFile(name, O_RDONLY, 0)
}
func Create(name string) (*File, error) {
    return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}

f.Read(slice) f.Write([]byte("abc")) f.WriteString("def") 
# 删除目录或文件，如果目录不为空则失败
os.Reomove("a.txt")
# 修改一个文件的名字或移动一个文件，如果 newpath 已经存在，则替换它
func Rename(oldpath, newpath string) error

# 读取整个文件
func ReadFile(name string) ([]byte, error)
# 将data写入filename文件中，当文件不存在时会根据perm指定的权限进行创建一个,文件存在时会先清空文件内容。
func WriteFile(name string, data []byte, perm FileMode)
# 删除 path 指定的文件和所有下级文件
func RemoveAll(path string) error
# 创建 path 指定的目录和所有下级目录，如果path存在不会报错
func MkdirAll(path string, perm FileMode) error
```
```
func main() {
	var err error
	f, _ := os.Open("a.txt")
	defer f.Close()
	s := make([]byte, 3)
	for {
		if _, err = f.Read(s); err != nil {
			break
		}
		fmt.Print(string(s))
	}

	f2, _ := os.OpenFile("a.txt", os.O_RDWR, 0)
	defer f.Close()
	f2.Write([]byte("abc"))
	f2.WriteString("def")
}
```

**bytes.Buffer**
bytes.Buffer针对内存到内存的缓存。

3种方式初始化：
```
a := bytes.NewBufferString("Hello World")
b := bytes.NewBuffer([]byte("Hello World"))
c := bytes.Buffer{}
```

写入：
```
b.Write(d []byte) (n int, err error)               //将切片d写入Buffer尾部
b.WriteString(s string) (n int, err error)         //将字符串s写入Buffer尾部
b.WriteByte(c byte) error                          //将字符c写入Buffer尾部
b.WriteRune(r rune) (n int, err error)            //将一个rune写入Buffer尾部
```

读取：
```
//从off偏移量开始读取len(p)长度的字节到p中
b.Read(p []byte) (n int, err error)
//读取下一个byte并返回，如果没有字节了，然后错误io.EOF
b.ReadByte() (byte, error)
//读取下一个 UTF8 编码的字符
b.ReadRune() (r rune, size int, err error)
//读取缓冲区第一个分隔符delim内容，并且返回从off到分隔符delim的内容
b.ReadBytes(delim byte) (line []byte, err error)
//读取缓冲区第一个分隔符delim内容，并且返回从off到分隔符delim的内容
b.ReadString(delim byte) (line string, err error)

读入到缓存区/从缓存区写入：
//从实现了io.Reader接口的可读取对象写入Buffer尾部
b.ReadFrom(r io.Reader) (n int64, err error)
//将 Buffer 中的内容输出到实现了 io.Writer 接口的可写入对象中，成功返回写入的字节数，失败返回错误
b.WriteTo(w io.Writer) (n int64, err error)
```
**path**
```
path.Dir("/a/b.txt")
path.Base("/a/b.txt")
path.Ext("/a/b.txt")
// 遍历目录，包括文件
filepath.Walk("/a/b", func(path string, info fs.FileInfo, err error) error {
   fmt.Println(path)
   return err
})
```


**ioutil**
```
func ReadDir(dirname string) ([]fs.FileInfo, error)
```

遍历目录：
```
func listAll(path string) {
	fileInfos, _ := ioutil.ReadDir(path)
	for _, info := range fileInfos {
		fmt.Println(info.Name())
		if info.IsDir() {
			listAll(path + "/" + info.Name())
		}
	}
}
```

**bufio**
针对的是文件到内存的缓存。

reader类型实例化：
```
func NewReaderSize(rd io.Reader, size int) *Reader
func NewReader(rd io.Reader) *Reader {
		// 默认缓存大小：defaultBufSize=4096
		return NewReaderSize(rd, defaultBufSize)
}

# 返回的 []byte 是指向 Reader 中的 buffer，而不是 copy 一份返回，也正因为如此，通常我们会使用 ReadBytes 或 ReadString。
func (b *Reader) ReadSlice(delim byte) (line []byte, err error)
func (b *Reader) ReadBytes(delim byte) (line []byte, err error)
func (b *Reader) ReadString(delim byte) (line string, err error)
func (b *Reader) WriteTo(w io.Writer) (n int64, err error)

func (b *Reader) Read(p []byte) (n int, err error)
func (b *Reader) ReadByte() (c byte, err error)
func (b *Reader) ReadRune() (r rune, size int, err error)

scanner类型，简单的读取一行，有每行最大缓存65535的限制：
scanner := bufio.NewScanner(os.Stdin)
for scanner.Scan() {
		fmt.Println(scanner.Text())
}
```

writer类型实例化：
```
func NewWriterSize(wr io.Writer, size int) *Writer
func NewWriter(wr io.Writer) *Writer {
		// 默认缓存大小：defaultBufSize=4096
		return NewWriterSize(wr, defaultBufSize)
}

func (b *Writer) ReadFrom(r io.Reader) (n int64, err error)
func (b *Writer) Write(p []byte) (nn int, err error)
func (b *Writer) WriteString(s string) (int, error)
func (b *Writer) WriteByte(c byte) error
func (b *Writer) WriteRune(r rune) (size int, err error)
```