**安装**
[官网安装](https://www.postgresql.org/download/linux/redhat/)

**连接**
```
sudo -u postgres psql
ALTER USER postgres WITH PASSWORD '123456';
\q

psql -U postgres -h localhost -p 5432 -d postgres
\dt
```