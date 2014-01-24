## clouda在BAE上开启websocket的配置

### 在BAE扩展服务中添加开通Port服务

#### 1.在Port服务内，配置内部端口号到`10666`

#### 2.修改代码 config/sumeru.js

添加一行sockHost项，填入bae自动分配的ip:port

```code
    sumeru.config({
 	   httpServerPort: 8080,
	   sumeruPath: '/../sumeru',
       sockHost:'IP:Port'
    });
```
