## clouda在bae上开启多个执行单元的配置

* #### server_config/bae.js

```code
    sumeru.config.cluster({
      enable : true,
      dbname : 'yourdbname',
      user: 'yourpk',
      password: 'yoursk',
  });
```
