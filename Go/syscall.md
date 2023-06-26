**文件锁**
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