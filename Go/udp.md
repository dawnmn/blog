**带心跳检测的udp通信**
server
```
package main

import (
   "context"
   "fmt"
   "github.com/go-redis/redis/v8"
   "net"
   "strconv"
   "time"
)

func InitRedis(db int) *redis.Client {
   // 这是个redis连接池
   redisClient := redis.NewClient(&redis.Options{
      Addr:     "192.168.126.128:6379",
      Password: "mypass",
      DB:       db,
      PoolSize: 10,
   })
   if _, err := redisClient.Ping(context.TODO()).Result(); err != nil {
      fmt.Println(err)
      return nil
   }
   return redisClient
}

func main() {
   //建立一个UDP的监听，这里使用的是ListenUDP，并且地址是一个结构体
   listen, err := net.ListenUDP("udp", &net.UDPAddr{
      IP:   net.IPv4(0, 0, 0, 0),
      Port: 8080,
   })
   if err != nil {
      fmt.Printf("listen failed, err:%v\n", err)
      return
   }

   rds := InitRedis(0)
   go func() {
      rds.ZRemRangeByScore(context.TODO(), "robot-conn", "0", strconv.Itoa(int(time.Now().Unix())))
   }()

   // 限制协程数目，避免mysql连接数超负荷
   goCh := make(chan bool, 200)
   for {
      var data [1000]byte
      //读取UDP数据
      count, addr, err := listen.ReadFromUDP(data[:])
      if err != nil {
         fmt.Printf("read udp failed, err:%v\n", err)
         continue
      }

      goCh <- true
      go func() {
         if string(data[0:count]) == "PING" {
            rds.ZAdd(context.TODO(), "robot-conn", &redis.Z{float64(time.Now().Unix() + 120), addr.String()})
            //返回数据
            _, err := listen.WriteToUDP([]byte("PONG"), addr)
            if err != nil {
               fmt.Printf("write udp failed, err:%v\n", err)
            }
            return
         }

         fmt.Printf("data:%s addr:%v count:%d\n", string(data[0:count]), addr, count)
         //返回数据
         _, err := listen.WriteToUDP([]byte("hello client"), addr)
         if err != nil {
            fmt.Printf("write udp failed, err:%v\n", err)
         }
         <-goCh
      }()
   }
}
```
client
package main

import (
   "fmt"
   "net"
   "sync"
)

func main() {
   // 创建连接
   wg := sync.WaitGroup{}

   for i := 0; i < 100; i++ {
      wg.Add(1)
      go func() {
         socket, err := net.DialUDP("udp4", nil, &net.UDPAddr{
            IP:   net.IPv4(127, 0, 0, 1),
            Port: 8080,
         })
         if err != nil {
            fmt.Println("连接失败!", err)
            return
         }
         defer socket.Close()
         // 发送数据
         senddata := []byte("PING")
         _, err = socket.Write(senddata)
         if err != nil {
            fmt.Println("发送数据失败!", err)
            return
         }
         // 接收数据
         data := make([]byte, 4096)
         read, remoteAddr, err := socket.ReadFromUDP(data)
         if err != nil {
            fmt.Println("读取数据失败!", err)
            return
         }
         fmt.Println(read, remoteAddr)
         fmt.Printf("%s\n", data)
         wg.Done()
      }()
   }
   wg.Wait()
}