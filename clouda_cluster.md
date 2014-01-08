## clouda在bae上开启多个执行单元的配置(可以link到bae部署中）

* #### server_config/bae.js

```code
    sumeru.config.cluster({
      enable : true,
      dbname : 'yourdbname',
      user: 'yourpk',
      password: 'yoursk',
  });
```
## clouda在单机上开启多个执行单元的配置

* #### server_config/database.js

```code
    sumeru.config.cluster({
      enable : true,
      host : 'your_redis_ip',
      port : 6379
      dbname : 'your_dbname',
      user: 'your_user',
      password: 'your_password',
  });
```
