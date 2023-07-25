**SetFinalizer**
在变量被GC回收时，触发的函数。
```
package main

import (
   "fmt"
   "log"
   "runtime"
   "time"
)

type Road int

func findRoad(r *Road) {
   log.Println("road:", *r)
}
func entry() {
   var rd Road = Road(999)
   r := &rd
   runtime.SetFinalizer(r, findRoad)
}
func main() {
   entry()
   for i := 0; i < 10; i++ {
      fmt.Println("-------", i)
      time.Sleep(time.Second)
      runtime.GC()
   }
}
```