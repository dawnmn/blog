#### **实现锁机制**
加锁
```
SET resource-name token-string NX EX max-lock-time
```
释放锁：先判断 token-string，再DEL
