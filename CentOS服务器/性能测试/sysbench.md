**PHP配置HTTP**
```
server {
   listen       9501;
   server_name  192.168.126.129;
   index index.html index.htm index.php;
   root  /var/www/html/ac-auth/app/web;

   if ($request_method = OPTIONS){
      return 200;
   }

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

   location ~ /\. {
        deny all;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass   php:19000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;

        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        try_files $fastcgi_script_name =404;
    }
}
```
**配置跨域**
```
server {
   ...
    add_header Access-Control-Allow-Methods *;
    add_header Access-Control-Max-Age 3600;
    add_header Access-Control-Allow-Credentials true;
    add_header Access-Control-Allow-Origin $http_origin;
    add_header Access-Control-Allow-Headers $http_access_control_request_headers;
    if ($request_method = OPTIONS){
        return 200;
    }
    location /api {
        proxy_pass http://172.17.215.80:9603;
    }
    ...
}
```
**配置HTTPS**
```
server {
    ...
    ssl on;
    ssl_certificate   /var/cert/yourfile.pem;
    ssl_certificate_key  /var/cert/yourfile.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ...
}

```

**配置gzip**
```
server {
    ...
   gzip on;
   gzip_min_length 2k;
   gzip_comp_level 3;
   gzip_types application/css application/javascript text/plain text/css text/javascript font/ttf font/otf;
   gzip_vary on;
   ...
}
```

**配置反向代理**
```
server {
    ...
   location / {
      proxy_pass http://www.domain1.com/;
   }
   location / {
      proxy_pass https://www.domain2.com/;
   }
   location /image {
      proxy_pass http://www.domain3.com/;
   }
   ...
}
```
**安装**
```
mkdir /var/download/temp
cd /var/download/temp
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.rpm.sh | sudo bash
sudo yum -y install sysbench
sysbench --version
```
**CPU**
```
sysbench cpu --cpu-max-prime=200000 run
sysbench threads --thread-yields=100 --thread-locks=2 --threads=64 run
```
**IO**
```
sysbench fileio --threads=16 --file-total-size=3G --file-test-mode=rndrw prepare
sysbench fileio --threads=16 --file-total-size=3G --file-test-mode=rndrw run
sysbench fileio --threads=16 --file-total-size=3G --file-test-mode=rndrw cleanup
```
**Memory**
```
sysbench memory --memory-block-size=8k --memory-total-size=4G run
```
**MySQL**
```
# 创建数据库
mysql  -uroot  -p
create database sbtest;

find / -name oltp.lua
sysbench /usr/share/sysbench/tests/include/oltp_legacy/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=29JdX5jhNxa09+ --oltp-test-mode=complex --oltp-tables-count=10 --oltp-table-size=100000 --threads=10 --time=120 --report-interval=10 prepare
sysbench /usr/share/sysbench/tests/include/oltp_legacy/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=29JdX5jhNxa09+ --oltp-test-mode=complex --oltp-tables-count=10 --oltp-table-size=100000 --threads=10 --time=120 --report-interval=10 run >> /var/download/temp/mysysbench.log
sysbench /usr/share/sysbench/tests/include/oltp_legacy/oltp.lua --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=29JdX5jhNxa09+ cleanup
```