```
type Duration int64 // 代表两个时间点之间经过的时间，以纳秒为单位。
time.Time // 类型代表一个纳秒精度的时间点。
```


```
time.Unix(sec, nsec int64) 通过 Unix 时间戳生成 time.Time 实例；
time.Time.Unix() 得到 Unix 时间戳；
time.Time.UnixNano() 得到 Unix 时间戳的纳秒表示；

// 时间字符串转time.Time
time.ParseInLocation("2006-01-02 15:04:05", "2023-01-05 11:01:00", time.FixedZone("CST", 8*3600))
// 格式化输出时间字符串
time.Now().Format("2006-01-02 15:04:05")

func After(d Duration) <-chan Time
func AfterFunc(d Duration, f func()) *Timer
func Tick(d Duration) <-chan Time
```

示例：
```
func main() {
	fmt.Println("begin")
	select {
	case <-time.After(time.Second):
		fmt.Println("after")
	}

	tick := time.Tick(time.Second * 2)
	for range tick {
		fmt.Println("tick")
	}
}
```

```
fmt.Println(time.Hour * 32) // 人类友好的格式：32h0m0s
```