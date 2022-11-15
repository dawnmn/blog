要基于 TCP 协议来构建自己的应用层协议，**就必须要自己定义消息的边界**，最常见的两种解决方案就是**基于长度或者终结符**。
go标准库net包的write read方法都加锁了。读用一个协程，写用多个，保证读写完整的业务包。
*****
server net包示例
```
package main

import (
   "bufio"
   "fmt"
   "io"
   "net"
)

func main() {
   listen, err := net.Listen("tcp", ":8080")
   if err != nil {
      fmt.Println("Listen失败", err)
      return
   }
   defer listen.Close()
   for {
      //Accept阻塞，直到有新的连接
      conn, err := listen.Accept()
      if err != nil {
         fmt.Println("Accept", err)
         return
      }
      go func() {
         defer conn.Close()
         reader := bufio.NewReader(conn)
         for {
            //ReadString阻塞，直到读到\n
            msg, err := reader.ReadString('\n')
            if err != nil {
               if err == io.EOF {
                  fmt.Println("connection close")
               } else {
                  fmt.Println("ReadString err", err)
               }
               return
            }
            fmt.Println("read data：", msg)
            b := []byte("ok")
            conn.Write(b)
         }
      }()
   }
}
```
server gnet2.0示例
```
package main

import (
   "flag"
   "fmt"
   "github.com/panjf2000/gnet/v2"
   "github.com/panjf2000/gnet/v2/pkg/pool/goroutine"
   "log"
)

type echoServer struct {
   gnet.BuiltinEventEngine

   eng       gnet.Engine
   addr      string
   multicore bool
   pool      *goroutine.Pool
}

func (es *echoServer) OnBoot(eng gnet.Engine) gnet.Action {
   es.eng = eng
   log.Printf("echo server with multi-core=%t is listening on %s\n", es.multicore, es.addr)
   return gnet.None
}

func (es *echoServer) OnTraffic(c gnet.Conn) gnet.Action {
   buf, _ := c.Next(-1)
   es.pool.Submit(func() {
      c.AsyncWrite(buf, func(c gnet.Conn) error {
         return nil
      })
   })
   return gnet.None
}

func main() {
   var port int
   var multicore bool

   // Example command: go run echo.go --port 9000 --multicore=true
   flag.IntVar(&port, "port", 9000, "--port 9000")
   flag.BoolVar(&multicore, "multicore", false, "--multicore true")
   flag.Parse()
   echo := &echoServer{addr: fmt.Sprintf("tcp://:%d", port), multicore: multicore, pool: goroutine.Default()}
   log.Fatal(gnet.Run(echo, echo.addr, gnet.WithMulticore(multicore)))
}
```


client
```
package main

import (
   "bufio"
   "fmt"
   "net"
   "os"
)

func main() {
   conn, err := net.Dial("tcp", "127.0.0.1:8080")
   if err != nil {
      fmt.Println("Dial err:", err)
      return
   }

   //接收终端输入
   reader := bufio.NewReader(os.Stdin)
   //缓冲
   buf := make([]byte, 1024)
   fmt.Println("Please input data...")
   for {
      //读取终端输入直到读取到\n
      input, err := reader.ReadString('\n')
      if err != nil {
         fmt.Println("ReadString err:", err)
         return
      }
      //写入数据
      conn.Write([]byte(input))
      //服务器端返回的数据写入buf
      count, err := conn.Read(buf)
      if err != nil {
         fmt.Println("Read err:", err)
         return
      }
      //服务器端回传的信息
      fmt.Println("server response:", string(buf[0:count]))
   }
}
```