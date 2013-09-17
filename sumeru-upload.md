Sumeru提供了端到云关于文件的上传方法，使用简单几个步骤的配置即可实现上传到托管服务器，管理，更新，删除文件。

此文档通过一个例子来说明一个文件上传与管理的过程。

### 1. 定义文件的数据Model
在`app/model`目录下定义model,三方model定义与普通model定义完全一致
```js
Model.testfileModel = function(exports){
    exports.config = {
        fields: [
            {name: 'name', type: 'string'},
            {name: 'path', type: 'string'},
            {name: 'isTmp', type: 'boolean'},
            {name: 'size', type: 'int'},
            {name: 'extension',type: 'string'}
        ]
    };
};
```

### 2. publish三方数据
collection.extfind方法表示此collection为三方数据，处理数据方式不同。

```js
//与普通的collection.find不同，三方数据的publish调用collection.extfind方法表示此collection为三方数据
fw.publish('student', 'pubext', function(/** arg1, arg2, arg3...*/ callback){

	var collection = this;
	collection.extfind('pubext', /** arg1, arg2, arg3...*/ callback);

});
```
