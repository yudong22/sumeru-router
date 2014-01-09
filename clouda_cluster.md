
## clouda在单机上开启多个执行单元的配置

#### 修改server_config/cluster.js

```code
    sumeru.config.cluster({
      enable : true,
      host : 'your_redis_ip',
      port : 6379
      dbname : 'your_redis_dbname',
      user: 'your_redis_user',
      password: 'your_redis_password',
  });
```
## clouda在bae上开启多个执行单元的配置(可以link到bae部署中）

#### 修改server_config/bae.js

```code
    sumeru.config.cluster({
      enable : true,
      dbname : 'yourdbname',
      user: 'yourpk',
      password: 'yoursk',
  });
```
