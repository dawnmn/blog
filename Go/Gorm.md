**通过gen自动生成struct代码**
```
go install gorm.io/gen/tools/gentool@latest

gentool -dsn "user:pwd@tcp(localhost:3306)/database?charset=utf8mb4&parseTime=True&loc=Local" -tables "orders,doctor"

gentool -db postgres -dsn "host=135.0.120.182 user=dolphinscheduler password=mypas dbname=alarm_center port=5432 sslmode=disable"
```