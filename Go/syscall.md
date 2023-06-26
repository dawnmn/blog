**文件锁**
syscall.Flock，只支持linux和mac
LOCK_SH：共享锁，每增加一个共享锁，共享锁的计数器会+1，如果获取排它锁，会失败。
LOCK_EX：排它锁，只能获取一个。
LOCK_NB：非阻塞，不会等待锁的获取。
LOCK_UN：释放锁
FD：通过fd区分是否是同一个进程
```
go func() {
	f, _ := os.Create(filename)
	defer f.Close()
	err := syscall.Flock(int(f.Fd()), syscall.LOCK_EX|syscall.LOCK_NB)
	if err != nil {
		fmt.Println(err)
		return
	}
	// TODO

	syscall.Flock(int(f.Fd()), syscall.LOCK_UN)
}()
```