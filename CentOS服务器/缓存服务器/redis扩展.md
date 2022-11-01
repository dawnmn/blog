**安装**
```
docker run -d --name redis-stack \
   -p 6379:6379 \
   -p 8001:8001 \
   -v /usr/local/redis/data:/data \
   redis/redis-stack:latest

docker exec -it containerId exec
redis-cli
module list
```

**RedisSearch 搜索数据库**
```
FT.CREATE idx:movie ON hash PREFIX 1 "movie:" SCHEMA title TEXT SORTABLE release_year NUMERIC SORTABLE rating NUMERIC SORTABLE genre TAG SORTABLE
FT._LIST
FT.ADD idx:movie movie:m1 1.0 fields title "功夫" release_year 2001 rating 8.5 genre "动作"
FT.INFO idx:movie

FT.SEARCH idx:movie "功夫"
FT.SEARCH idx:movie "@genre:{动作}"
FT.SEARCH idx:movie "@release_year:[1900 2010]"
```

**RedisTimeSeries 时序数据库**
```
TS.CREATE sensor1
TS.ADD sensor1 1667293236094 160
```

