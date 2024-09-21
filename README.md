### 一个集成php，mysql，nginx的docker compose项目

#### 启动
启动时要注意启动的顺序，一般nginx放最后。
```
docker compose up -d mysql php-fpm nginx
```
