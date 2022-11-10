```
func main()  {
   var s,seq string
   for i:=0; i<len(os.Args); i++{
      s += seq + os.Args[i]
      seq = " "
   }
   fmt.Println(s)
}
```

```
func main()  {
   fmt.Println(os.Args[1:])
}
```

```
func main()  {
   s,seq := "",""
   for _,arg := range os.Args[1:]{
      s += seq + arg
      seq = " "
   }
   fmt.Println(s)
}
```

```
func main()  {
   fmt.Println(strings.Join(os.Args[1:], " "))
}
```

```
func main()  {
   counts := make(map[string]int)
   input := bufio.NewScanner(os.Stdin)
   for input.Scan() {
      if input.Text()=="die"{
         break
      }
      counts[input.Text()]++
   }

   for line,num := range counts {
      if num > 1{
         fmt.Printf("%d\t%s\n", num, line)
      }
   }
}
```

```
func main()  {
   counts := make(map[string]int)

   files := os.Args[1:]
   if len(files) == 0 {
      scanLine(os.Stdin, counts)
   }else{
      for _,fileName := range files {
         f,err := os.Open(fileName)
         if err!=nil{
            fmt.Fprintf(os.Stderr, "%v\n", err) // 错误输出
            continue
         }
         scanLine(f, counts)
         f.Close()
      }
   }
   fmt.Println("重复的行：")
   for line,num:= range counts{
      if num > 1{
         fmt.Printf("%d次:\t%s\n", num, line)
      }
   }
}
func scanLine(f *os.File, counts map[string]int)  { // map slice 传值传的是引用
   input := bufio.NewScanner(f) // 文件流的形式
   for input.Scan() {
      counts[input.Text()]++
   }
}
```

```
func main()  {
   counts := make(map[string] int)

   files := os.Args[1:]
   for _,filename:=range files{
      content,err := ioutil.ReadFile(filename) // 将整个文件读取到内存中
      if err!=nil{
         fmt.Fprintf(os.Stderr, "%v\t", err)
         continue
      }
      for _,line:= range strings.Split(string(content), "\n"){
         counts[strings.Trim(line,"\r")]++
      }
   }

   for line,num := range counts{
      if num>1{
         fmt.Printf("%d\t%s\n", num, line)
      }
   }
}
```

```
func main()  {
   response, err := http.Get(os.Args[1])
   if err!=nil{
      fmt.Fprintf(os.Stderr, "%v\n", err)
      os.Exit(1)
   }

   body,err := ioutil.ReadAll(response.Body) // resp的Body字段包括一 个可读的服务器响应流。
   response.Body.Close()
   if err!=nil{
      fmt.Fprintf(os.Stderr, "%v\n", err)
      os.Exit(1)
   }
   fmt.Printf("%s", body)
}
```

```
func main()  {
   ch := make(chan string) // 创建管道
   timeBegin := time.Now()
   for _,url:= range os.Args[1:]{
      go request(url, ch) // 创建协程
   }
   for range os.Args[1:]{ // 由于协程函数只往管道写一次，所以循环协程数目次数
      fmt.Println(<-ch) // 获取协程返回数据
   }

   fmt.Printf("%.2f\n", time.Since(timeBegin).Seconds())
}
func request(url string, ch chan<- string)  { // 协程函数，只往管道写一次
   timeBegin := time.Now()
   response,err := http.Get(url)
   if err!=nil{
      ch <- fmt.Sprint(err)
      return
   }

   length,err := io.Copy(ioutil.Discard, response.Body) // ioutil.Discard输出流，可以向里面写一些不需要的数据
   response.Body.Close()
   if err!=nil{
      ch <- fmt.Sprint(err)
      return
   }

   ch <- fmt.Sprintf("%.2f %d %s", time.Since(timeBegin).Seconds(), length, url)
}
```

```
func main() {
   http.HandleFunc("/", handler)
   log.Fatal(http.ListenAndServe("localhost:8000", nil))
}
func handler(w http.ResponseWriter, r *http.Request) {
   fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}
```

```
func main()  {
   x := 1
   p := &x
   fmt.Println(p)
   fmt.Println(*p)
   *p = 2
   fmt.Println(x)
}
```

```
func main()  {
   x,y := 100, 200
   x,y = y,x //在赋值之前，赋值语句右边的所有 表达式将会先进行求值，然后再统一更新左边对应变量的值。
   fmt.Printf("x:%d y:%d", x, y)
}
```

```
func main()  {
   /**
   D:\program\Go\src\summer\test\print.go:
      package test

      import "fmt"

      func P()  {
         fmt.Println("summer/test/print/p")
      }
   */
   test.P()
}
```

```
func main() {
   x := "hello!"
   for i := 0; i < len(x); i++ { // for语句创建了两个词法域：花括弧包含的是显式的部分是for的循环体部分词法域，另外一个隐式的部分则是循环的初始化部分
      x := x[i] // 和for循环类似，if和switch语句也会在条件部分创建隐式词法域，还有它们对应的执行体词法域。
      if x != '!' {
         x := x + 'A' - 'a'
         fmt.Printf("%c", x) // HELLO
      }
   }
}
```

```
func main() {
   c1 := sha256.Sum256([]byte("x"))
   c2 := sha256.Sum256([]byte("X"))
   fmt.Printf("%x\n%x\n%t\n%T\n", c1, c2, c1 == c2, c1) // Output: // 2d711642b726b04401627ca9fbac32f5c8530fb1903cc4db02258717921a4881 // 4b68ab3847feda7d6c62c1fbcbeebfa35eab7351ed5e78f4ddadea5df64b8015 // false // [32]uint8
}
```

```
func main() {
   counts := make(map[rune]int)
   var utflen [utf8.UTFMax + 1]int
   invalid := 0
   in := bufio.NewReader(os.Stdin)
   for {
      r, n, err := in.ReadRune()
      if err == io.EOF { // io包保证任何由文件结束引起的读取失败都返回同一个错误——io.EOF
         break
      }
      if err != nil {
         fmt.Fprintf(os.Stderr, "charcount: %v\n", err)
         os.Exit(1)
      }
      if r == unicode.ReplacementChar && n == 1 {
         invalid++
         continue
      }
      counts[r]++
      utflen[n]++
   }
   fmt.Printf("rune\tcount\n")
   for c, n := range counts {
      fmt.Printf("%q\t%d\n", c, n)
   }
   fmt.Print("\nlen\tcount\n")
   for i, n := range utflen {
      if i > 0 {
         fmt.Printf("%d\t%d\n", i, n)
      }
   }
   if invalid > 0 {
      fmt.Printf("\n%d invalid UTF-8 characters\n", invalid)
   }
}
```

```
type Employee struct {
   ID        int
   Name, Address      string
   DoB       time.Time
   Position  string
   Salary    int
   ManagerID int
} // 一个命名为S的结构体类型将不能再包含S类型的成员：因为一个聚合的值不能包含它自身。（该限制同 样适应于数组。）但是S类型的结构体可以包含*S指针类型的成员，这可以让我们创建递归的数据结构，比如链表和树结构等。
func main()  {
   var join Employee
   join.Name = "join"
   p := &join.Name // 对成员取址
   *p = "black"
   fmt.Println(join.Name)
   pt := &join
   pt.Name = "yellow"
   fmt.Println(join.Name)
}
```

```
type tree struct{
   value int // 值
   left, right *tree // 双向链表
}
func Sort(values []int) {
   var root *tree
   for _, v := range values { // 遍历进行排序
      root = add(root, v) // 生成二叉排序树
   }
   appendValues(values[:0], root) // 中序遍历生成有序数组
}
func appendValues(values []int, t *tree) []int {
   if t != nil {
      // 中序遍历 左中右
      values = appendValues(values, t.left)
      values = append(values, t.value)
      values = appendValues(values, t.right)
   }
   return values
}
func add(t *tree, value int) *tree {
   if t == nil {
      t = new(tree) // 二叉排序树的（递归）定义：1、若左子树非空，则左子树所有节点的值均小于它的根节点；2、若右子树非空，则右子树所有节点的值均大于于它的根节点；3、左右子树也分别为二叉排序树。
      t.value = value
      return t
   }
   if value < t.value {
      t.left = add(t.left, value)
   } else {
      t.right = add(t.right, value)
   }
   return t
}
func main()  {
   values := []int{1000,20,25,320,23420,230,420}

   Sort(values)
   fmt.Printf("%v", values)
}
```

```
type point struct {
   x,y int
}
type circle struct {
   point
   radius int
} // 匿名成员
type wheel struct {
   circle
   weight int
}
func main()  {
   w := wheel{circle{point{10,20},30},40}
   fmt.Printf("%#v", w)
}

type Movie struct {
   Color bool `json:"color"`
   Title string `json:"title"`
   Actors []string
}
func main()  {
   movies := []Movie{
      {false, "asdf", []string{"1111", "222222222"}},
      {false, "asdf", []string{"1111", "222222222"}},
   }

   data,_:=json.Marshal(movies)
   jsonStr := fmt.Sprintf("%s",data)
   fmt.Println(jsonStr)
}
```

```
func tempDirs() []string {
   return []string{"abc", "bbc", "cbc"}
}
func main() {
   var rmdirs []func()
   for _, d := range tempDirs() {
      dir := d // 创建临时变量
      os.MkdirAll(dir, 0755)
      rmdirs = append(rmdirs, func() { os.RemoveAll(dir) }) // 注意这里，不能使用循环变量
   }
   for _, rmdir := range rmdirs {
      rmdir()
   }
}
```

```
func sum(val... int) int {
   s := 0
   for _,v:=range val{
      s +=v
   }
   return s
}
```

```
func title(url string) error {
   resp, err := http.Get(url)
   if err != nil {
      return err
   }
   defer resp.Body.Close() //直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行，你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反。不要忘记defer语句后的圆括号。
   ct := resp.Header.Get("Content-Type")
   if ct != "text/html" && !strings.HasPrefix(ct, "text/html;") {
      //resp.Body.Close()
      return fmt.Errorf("%s has type %s, not text/html", url, ct)
   }
   doc, err := html.Parse(resp.Body)
   //resp.Body.Close()
   if err != nil {
      return fmt.Errorf("parsing %s as HTML: %v", url, err)
   }
   visitNode := func(n *html.Node) {
      if n.Type == html.ElementNode && n.Data == "title" && n.FirstChild != nil {
         fmt.Println(n.FirstChild.Data)
      }
   }
   forEachNode(doc, visitNode, nil)
   return nil
}
```

```
func bigSlowOperation() {
   defer trace("bigSlowOperation")()
   time.Sleep(10 * time.Second)
}
func trace(msg string) func() {
   start := time.Now()
   log.Printf("enter %s", msg)
   return func() { log.Printf("exit %s (%s)", msg, time.Since(start)) }
}
func main()  {
   bigSlowOperation()
}
```

```
type Point struct {
   x,y float64
}
func Distance(p,q Point) float64  {
   return math.Hypot(p.x-q.x,p.y-q.y)
}
func (p Point) Distance(q Point) float64  {
   return math.Hypot(p.x-q.x,p.y-q.y)
}
func main() {
   p := Point{10, 3}
   q := Point{15, 1}
   fmt.Println(Distance(p,q))
   fmt.Println(p.Distance(q))
}
```

```
type Point struct {
   x, y float64
}
type Path []Point
func (p Point) Distance(q Point) float64 { // 方法和属性不能重名
   return math.Hypot(p.x-q.x, p.y-q.y)
}
func (path Path) Distance() float64 { // Path是一个命名的slice类型，而不是Point那样的struct类型，然而我们依然可以为它定义方法
   sum := 0.0
   for i := range path {
      if i > 0 {
         sum += path[i-1].Distance(path[i])
      }
   }
   return sum
}
func main() {
   perim := Path{{1, 1}, {5, 1}, {5, 4}, {1, 1}} // 计算三角形的周长
   fmt.Println(perim.Distance())
}
```

```
a = [1, 2, 3, 4, 5], a[0:3] = [1, 2, 3] 左闭右开
s := ""
var s string
const(
   a  = iota + 1
   b
   c
)
i,j:= 1,0 // 简短变量声明语句 1 必须至少要声明一个新的变量。 2 如果变量是在外部词 法域声明的，那么简短变量声明语句将会在当前词法域重新声明一个新的变量。
i, j = j, i // 交换 i 和 j 的值

func main()  {
   str := "hello 你好"
   fmt.Println(len(str))
   fmt.Println(utf8.RuneCountInString(str))
   fmt.Println(len([]rune(str)))
   fmt.Printf("%c", str)
}
```

```
var mu sync.Mutex // 加锁
var count int
func main() {
   http.HandleFunc("/", handler)
   http.HandleFunc("/count", counter)
   log.Fatal(http.ListenAndServe("localhost:8000", nil))
}
func handler(w http.ResponseWriter, r *http.Request) {
   mu.Lock()
   count++
   mu.Unlock()
   fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}
func counter(w http.ResponseWriter, r *http.Request) {
   mu.Lock()
   fmt.Fprintf(w, "Count %d\n", count)
   mu.Unlock()
}
```

```
// 指针 任何类型的指针的零值都是nil。如果p != nil测试为真，那么p是指向某个有效变量。指针之间也是可以进行相等测试的，只有当它们指向同一个变量或全部是nil时才相等。
func main()  {
   p := new(int) // 创建一个int类型的指针
   *p = 1 // *p是变量的别名
   fmt.Println(f() == f())
}
func f() *int  {
   v := 1
   return &v
}

type C float64
type F float64
const(
   AC = -273.15
   FC = 0
   BC = 100
)
func C2F(c C)F  {
   return F(c*9/5 + 32) // 它们虽然有着相同的底 层类型float64，但是它们是不同的数据类型，因此它们不可以被相互比较或混在一个表达式运算。
}
func F2C(f F)C  {
   return C((f - 32) * 5 / 9)
}
func (c C) string()string  {
   return fmt.Sprintf("%g °C",c)
}
```

```
var a = b + c // c 第三个初始化
var b = f() // b 第二个初始化
var c = 10 // c 第一个初始化
func f()int  {
   return 2
}
func main()  {
   fmt.Println(a)
}
```

```
不要将作用域和生命周期混为一谈。声明语句的作用域对应的是一个源代码的文本区域；它是一个编译时的属性。一个变量的生命周期是指程序运行时变量存在的有效时间段，在此时间区域内它可以被程序的其他部分引用；是一个运行时的概念。
byte <--> int8 ; rune <--> int32
标准库中有四个包对字符串处理尤为重要：bytes、strings、strconv和unicode包
```

```
func main() {
   fmt.Println(basename("a/b/c.go"))
}
func basename(s string) string {
   for i := len(s) - 1; i >= 0; i-- {
      if s[i] == '/' {
         s = s[i+1:]
         break
      }
   }
   for i := len(s) - 1; i >= 0; i-- {
      if s[i] == '.' {
         s = s[:i]
         break
      }
   }
   return s
}
func basename2(s string) string {
   slash := strings.LastIndex(s, "/")
   s = s[slash+1:]
   if dot := strings.LastIndex(s, "."); dot >= 0 {
      s = s[:dot]
   }
   return s
}
```

```
s := "abc"
b := []byte(s)
s2 := string(b)
```

```
func IntsToString(values []int)string {
   var buf bytes.Buffer
   buf.WriteByte('[')
   for i,value := range values{
      if(i > 0){
         buf.WriteByte(',')
      }
      fmt.Fprintf(&buf, "%d", value)
   }
   buf.WriteByte(']')
   return buf.String()
}
func main()  {
   a := 100
   s := strconv.Itoa(a)
   fmt.Println(s)
   fmt.Println(IntsToString([]int{1,2,3,4}))
}
```

```
// 一个slice由三个部分构成：指针、长度 和容量。
func main()  {
   s := []int{1,2,3,4,5}
   f(s[:4])
   fmt.Println(s)

   cs := s[:0]
   cs = append(cs, 100)
   fmt.Println(s)

   var runes []rune
   str := "hello, 世界"
   for _,ch := range str{
      runes = append(runes, ch)
   }
   fmt.Printf("%q", runes)

   var stack = []int{100,200,300}
   stack = stack[:len(stack)-1] // 弹出栈顶的元素

}
func f(s []int)  {
   s[2] = s[3]
}
func remove(s []int, i int)  { // 删除s中间的某个元素并保存原有的元素顺序
   copy(s[i:], s[i+1:])
   s = s[:len(s)-1]
}
```

```
// map是一个无序的key/value对的集合，其中所有的key都是不同 的
func main() {
   //ages := make(map[string]int)
   ages := map[string]int{
      "join": 24,
      "rose": 22,
   }
   ages["joe"] = 30 // map中的元素并不是一个变量，不能引用map元素
   delete(ages, "joe")
   if _, ok := ages["bob"]; !ok {
      fmt.Println("bob不存在")
   }

   list := []string{"asdf", "阿斯蒂芬", "132123撒地方asdf"}
   fmt.Printf("%q", list)
}
var m = make(map[string]int)
func k(list []string) string  {
   return fmt.Sprintf("%q", list)
}
func Add(list []string)       {
   m[k(list)]++
}
func Count(list []string) int {
   return m[k(list)]
}
```

```
// 在函数调用时，Go语言没有默认参数值，也没有任何方法可以通过参数名指定形参，因此形参和返回值的变量名对于函数调用者而言没有意义。
// 如果实参包括引用类型，如指针，slice(切片)、map、function、channel等类型，实参可能会由于函数的简介引用被修改。
// 在Go中，函数被看作第一类值，函数像其他值一样，拥有类型，可以被赋值给 其他变量，传递给函数，从函数返回。对函数值的调用类似函数调用。
// 匿名函数可以访问完整的词法环境，这意味着 在函数中定义的内部函数可以引用该函数的变量。
func Split(path string) (dir, file string) {
   return "1", "2"
}
func squares() func() int { var x int
   return func() int { x++
      return x * x } }
func main() {
   f := squares()
   fmt.Println(f()) // squares返回后，变量x仍然隐式的存在于f中。
   fmt.Println(f())
   fmt.Println(f())
   fmt.Println(f())
}
```

```
var prereqs = map[string][]string{
   "algorithms":            {"data structures"},
   "calculus":              {"linear algebra"},
   "compilers":             {"data structures", "formal languages", "computer organization"},
   "data structures":       {"discrete math"},
   "databases":             {"data structures"},
   "discrete math":         {"intro to programming"},
   "formal languages":      {"discrete math"},
   "networks":              {"operating systems"},
   "operating systems":     {"data structures", "computer organization"},
   "programming languages": {"data structures", "computer organization"},
}
func main() {
   for i, course := range topoSort(prereqs) {
      fmt.Printf("%d:\t%s\n", i+1, course)
   }
}
func topoSort(m map[string][]string) []string {
   var order []string
   seen := make(map[string]bool)
   var visitAll func(items []string) // 当匿名函数需要被递归调用时，我们必须首先声明一个变量
   visitAll = func(items []string) {
      for _, item := range items {
         if !seen[item] {
            seen[item] = true
            visitAll(m[item])
            order = append(order, item)
         }
      }
   }
   var keys []string
   for key := range m {
      keys = append(keys, key)
   }
   sort.Strings(keys)
   visitAll(keys)
   return order
}
```

```
panic defer recover
func main() {
   defer func() {
      if err := recover(); err != nil {
         log.Printf("recover: %v", err)
      }
   }()

   panic("EDDYCJY.")
}
```

```
func main()  {
   const day = 24 * time.Hour
   fmt.Println(day.Seconds()) // 常量也可以有方法
}
```

```
type Values map[string][]string
func (v Values) Get(key string) string {
   if vs := v[key]; len(vs) > 0 {
      return vs[0]
   }
   return ""
}
func (v Values) Add(key, value string) {
   v[key] = append(v[key], value)
}
func main() {
   m := url.Values{"lang": {"en"}}
   m.Add("item", "1")
   m.Add("item", "2")
   fmt.Println(m.Get("lang"))
   fmt.Println(m.Get("q"))
   fmt.Println(m.Get("item"))
   fmt.Println(m["item"])
   m = nil
   fmt.Println(m.Get("item"))
   m.Add("item", "3")
}
```

```
// Go语言只有一种控制可见性的手段：大写首字母的标识符会从定义它们的包中被导出，小写字母的则不会。
var cache = struct {
   sync.Mutex
   mapping map[string]string
}{
   mapping: make(map[string]string),
}
func Lookup(key string) string {
   cache.Lock()
   v := cache.mapping[key]
   cache.Unlock()
   return v
}
```

```
func main() {
   db := database{"shoes": 50, "socks": 5}
   log.Fatal(http.ListenAndServe("localhost:8000", db))
}
type dollars float32
func (d dollars) String() string { return fmt.Sprintf("$%.2f", d) }
type database map[string]dollars
func (db database) ServeHTTP(w http.ResponseWriter, req *http.Request) {
   switch req.URL.Path {
   case "/list":
      for item, price := range db {
         fmt.Fprintf(w, "%s: %s\n", item, price)
      }
   case "/price":
      item := req.URL.Query().Get("item")
      price, ok := db[item]
      if !ok {
         w.WriteHeader(http.StatusNotFound)
         fmt.Fprintf(w, "no such item: %q\n", item)
         return
      }
      fmt.Fprintf(w, "%s\n", price)
   default:
      w.WriteHeader(http.StatusNotFound)
      fmt.Fprintf(w, "no such page: %s\n", req.URL)
   }
}
```

```
func main() {
   db := database{"shoes": 50, "socks": 5}
   mux := http.NewServeMux()
   mux.Handle("/list", http.HandlerFunc(db.list))
   mux.Handle("/price", http.HandlerFunc(db.price))
   log.Fatal(http.ListenAndServe("localhost:8000", mux))
}
type dollars float32
type database map[string]dollars

func (db database) list(w http.ResponseWriter, req *http.Request) {
   for item, price := range db {
      fmt.Fprintf(w, "%s: %s\n", item, price)
   }
}
func (db database) price(w http.ResponseWriter, req *http.Request) {
   item := req.URL.Query().Get("item")
   price, ok := db[item]
   if !ok {
      w.WriteHeader(http.StatusNotFound)
      fmt.Fprintf(w, "no such item: %q\n", item)
      return
   }
   fmt.Fprintf(w, "%s\n", price)
}
```

```
func main()  {
   var w io.Writer = os.Stdout
   f, ok := w.(*os.File) // 类型断言
   b, ok := w.(*bytes.Buffer)
   fmt.Println(f,ok, b)
}
```

```
func main() {
   conn, err := net.Dial("tcp", "localhost:8000")
   if err != nil {
      log.Fatal(err)
   }
   defer conn.Close()
   mustCopy(os.Stdout, conn)
}
func mustCopy(dst io.Writer, src io.Reader) {
   if _, err := io.Copy(dst, src); err != nil {
      log.Fatal(err)
   }
}
```

```
func init() {
   file := "./" +"message"+ ".txt"
   logFile, err := os.OpenFile(file, os.O_RDWR|os.O_CREATE|os.O_APPEND, 0766)
   if err != nil {
      panic(err)
   }
   log.SetOutput(logFile)
   log.SetPrefix("[qSkipTool]")
   log.SetFlags(log.LstdFlags | log.Lshortfile | log.LUTC)
   return
}
func main() {
   log.Println("Hello Davis!")
   log.Fatalf("error code: %d", 100)
   return
}
```

```
func add1(r rune) rune { return r + 1 }
func main()  {
   fmt.Println(strings.Map(add1, "HAL-9000")) // "IBM.:111"
   strings.Map(func(r rune) rune { return r + 1 }, "HAL-9000")
   fmt.Println(strings.Map(add1, "VMS")) // "WNT"
   fmt.Println(strings.Map(add1, "Admix")) // "Benjy"
}
```

```
func squares() func() int {
   var x int
   return func() int { // 闭包 函数值不仅仅是一串代码，还记录了状态
      x++
      return x * x
   }
}
func main() {
   f := squares()
   fmt.Println(f()) // "1"
   fmt.Println(f()) // "4"
   fmt.Println(f()) // "9"
   fmt.Println(f()) // "16"
}
```

```
type Point struct{ X, Y float64 }
func (p Point) Distance(q Point) float64 { // 方法和属性不能重名
   return math.Hypot(p.X-q.X, p.Y-q.Y)
}
type ColoredPoint struct {
   Point
   Color color.RGBA
}
func main()  {
   red := color.RGBA{255, 0, 0, 255}
   blue := color.RGBA{0, 0, 255, 255}
   var p = ColoredPoint{Point{1, 1}, red}
   var q = ColoredPoint{Point{5, 4}, blue}
   distanceFromP := p.Distance // 方法值
   fmt.Println(distanceFromP(q.Point)) // "5"，这里用q.Point而不是q，一个ColoredPoint并不是一个Point，但他"has a"Point，并且它有从Point类里引入的Distance
}
```

```
Go语言里的集合一般会用map[T]bool这种形式来表示
一个struct类型的字段对同一个包的所有代码都有可见性，无论你的代码是写在一个函数还是一个方法里。
```

```
func Fprintf(w Writer, format string, args ...interface{}) (int, error) {
   return 0, nil
}
func Printf(format string, args ...interface{}) (int, error) {
   return Fprintf(os.Stdout, format, args...)
}
func Sprintf(format string, args ...interface{}) string {
   var buf bytes.Buffer
   _, _ = Fprintf(&buf, format, args...)
   return buf.String()
}
type Writer interface {
   Write(p []byte) (n int, err error)
}
type ByteCounter int
func (c *ByteCounter) Write(p []byte) (int, error) {
   *c += ByteCounter(len(p))
   return len(p), nil
}
func main() {
   var c ByteCounter
   c.Write([]byte("hello"))
   fmt.Println(c)
   c = 0
   var name = "Dolly"
   fmt.Fprintf(&c, "hello, %s", name)
   fmt.Println(c)
}
```

```
// 一个类型如果拥有一个接口需要的所有方法，那么这个类型就实现了这个接口。
// 因为ReadWriter和ReadWriteCloser包含所有Writer的方法，所以任何实现了ReadWriter和 ReadWriteCloser的类型必定也实现了Writer接口。
// 我们可以将任意一个值赋给空接口类型
func main() {
   var any interface{}
   any = true
   any = 12.34
   any = "hello"
   any = map[string]int{"one": 1}
   any = new(bytes.Buffer)
   fmt.Printf("%T", any)
}

var period = flag.Duration("period", 1*time.Second, "sleep period") // flag.Value
func main() {
   flag.Parse()
   fmt.Printf("Sleeping for %v...", *period)
   time.Sleep(*period)
   fmt.Println()
}
```
~~~
//当一个程序启动时，其主函数即在一个单独的goroutine中运行，我们叫它main goroutine。新的goroutine会用go语句来创建。
//当主函数返回时，所有的goroutine都会直接打断，程序退出。除了从主函数退出或者直接退出程序之外，没有其它的编程方法能够让一个goroutine来打断另一个的执行，但是我们之后可以看到，可以通过goroutine之间的通信来让一个goroutine请求请求其它的goroutine，并让其自己结束执行。
func main() { // server
   listener, err := net.Listen("tcp", "192.168.152.1:8000")
   if err != nil {
      log.Fatal(err)
   }
   for {
      conn, err := listener.Accept()
      if err != nil {
         log.Print(err)
         continue
      }
      go handleConn(conn)
   }
}
func handleConn(c net.Conn) {
   defer c.Close()
   for {
      _, err := io.WriteString(c, time.Now().Format("15:04:05\n"))
      if err != nil {
         return
      }
      time.Sleep(1 * time.Second)
   }
}

~~~

~~~
func main() { // client
   conn, err := net.Dial("tcp", "localhost:8000")
   if err != nil {
      log.Fatal(err)
   }
   defer conn.Close()
   mustCopy(os.Stdout, conn)
}
func mustCopy(dst io.Writer, src io.Reader) {
   if _, err := io.Copy(dst, src); err != nil {
      log.Fatal(err)
   }
}
~~~
~~~
// Channel还支持close操作，用于关闭channel，随后对基于该channel的任何发送操作都将导致panic异常。对一个已经被close过的channel之行接收操作依然可以接受到之前已经成功发送的数据；如果channel中已经没有数据的话讲产生一个零值的数据。
ch = make(chan int) // unbuffered channel
ch = make(chan int, 0) // unbuffered channel
ch = make(chan int, 3) // buffered channel with capacity 3
~~~
~~~
func main() {
   naturals := make(chan int)
   squares := make(chan int)
   go func() {
      for x := 0; x<100 ; x++ {
         naturals <- x
      }
      close(naturals)
   }()
   go func() {
      for x := range naturals{
         squares <- x * x
      }
      close(squares)
   }()
   for x := range squares{
      fmt.Println(x)
   }
}
~~~
~~~
// 类型chan<-int表示一个只发送int的channel，只能发送不能接收。相反，类型<-chan int表示一个只接收int的channel，只能接收不能发送。
// 如果我们使用了无缓存的channel，那么两个慢的goroutines将会因为没有人接收而被永远卡住。这种情况，称为goroutines泄漏，这将是一个BUG。和垃圾变量不同，泄漏的goroutines并不会被自动回收，因此确保每个不再需要的goroutine能正常退出是重要的。
func mirroredQuery() string {
   responses := make(chan string, 3)
   go func() { responses <- request("asia.gopl.io") }()
   go func() { responses <- request("europe.gopl.io") }()
   go func() { responses <- request("americas.gopl.io") }()
   return <-responses // return the quickest response
}
func request(hostname string) (response string) { /* ... */ }
~~~
~~~
func service1(ch chan string) {
   time.Sleep(2 * time.Second)
   ch <- "from service1"
}
func service2(ch chan string) {
   time.Sleep(1 * time.Second)
   ch <- "from service2"
}
func main() {
   ch1 := make(chan string)
   ch2 := make(chan string)
   go service1(ch1)
   go service2(ch2)

   select {       // 会发送阻塞
   case s1 := <-ch1:
      fmt.Println(s1)
   case s2 := <-ch2:
      fmt.Println(s2)
   }
}
~~~
~~~
// 不要使用共享数据来通信；使用通信来共享数据
// 在以字符串作为参数传递给fmt.Println函数时，字符串的内容并没有被复制——传递的仅仅是字符串的地址和长度
~~~
~~~
package main

import "fmt"

var ch1 chan int
var ch2 chan int
var chs = []chan int{ch1, ch2}
var numbers = []int{1, 2, 3, 4, 5}

func main() {
   select {
   case getChan(0) <- getNumber(2):
      fmt.Println("1th case is selected.")
   case getChan(1) <- getNumber(3):
      fmt.Println("2th case is selected.")
   default:
      fmt.Println("default!.")
   }
}

func getNumber(i int) int {
   fmt.Printf("numbers[%d]\n", i)
   return numbers[i]
}
func getChan(i int) chan int {
   fmt.Printf("chs[%d]\n", i)
   return chs[i]
}
~~~
~~~
package main

import (
   "context"
   "fmt"
   "sync"
   "time"
)

var wg sync.WaitGroup

func worker(ctx context.Context) {
   go worker2(ctx)
LOOP:
   for {
      fmt.Println("worker")
      time.Sleep(time.Second)
      select {
      case <-ctx.Done(): // 等待上级通知
         break LOOP
      default:
      }
   }
   wg.Done()
}

func worker2(ctx context.Context) {
LOOP:
   for {
      fmt.Println("worker2")
      time.Sleep(time.Second)
      select {
      case <-ctx.Done(): // 等待上级通知
         break LOOP
      default:
      }
   }
}
func main() {
   ctx, cancel := context.WithCancel(context.Background())
   wg.Add(1)
   go worker(ctx)
   time.Sleep(time.Second * 3)
   cancel() // 通知子goroutine结束
   wg.Wait()
   fmt.Println("over")
}
~~~
```
func main()  {
 http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello"))
 })
 http.ListenAndServe("localhost:8000", nil)
}
```
```
func main()  {
   http.ListenAndServe("localhost:8000", server{})
}
type server struct {}
func (s server) ServeHTTP(w http.ResponseWriter, r *http.Request) {
   switch r.RequestURI {
   case "/":
      w.Write([]byte("home"))
   case "/joke":
      w.Write([]byte("joke"))
   }
}
```
```
func main()  {
   mux := http.NewServeMux()
   mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
      w.Write([]byte("home"))
   })
   mux.HandleFunc("/joke", func(w http.ResponseWriter, r *http.Request) {
      w.Write([]byte("joke"))
   })
   http.ListenAndServe("localhost:8000", mux)
}
```