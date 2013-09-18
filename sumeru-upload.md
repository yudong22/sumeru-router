Sumeru提供了端到云关于文件的上传方法，使用简单几个步骤的配置即可实现上传到托管服务器，管理，更新，删除文件。

此文档通过一个例子来说明一个文件上传与管理的过程。

### 1. 定义文件的数据Model
在`app/model`目录下定义model,三方model定义与普通model定义完全一致
```js
Model.fileModel = function(exports){
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

### 2. 定义model的订阅，并添加联动方法
保存上传文件的信息，用于以后通过浏览器进行更新/删除操作。

```js
fw.publish('fileModel', 'pub-upload-files', function(callback){
        var collection = this;
        collection.find({},function(err, items){
             callback(items);
         });
     },{
         beforeInsert : function(serverCollection, structData, userinfo,  callback){
             //插入文件，补充数据字段信息
             if (typeof structData.name == 'string'){
                 var match = structData.name.match(/^(.*?)([^\/]+)$/);
                 if (match){
                    structData.name=match[2];
                    structData.path=match[1];
                    structData.isTmp=true;
                    callback(structData);
                 }
             }
         },
        beforeDelete : function(serverCollection, structData, userinfo,  callback){
            //删除记录的同时删除文件本身
            getDb(serverCollection.baseModel,function(err, handler){
                handler.find({smr_id:ObjectId(structData.smr_id)},{},function(err, data){
                    data.toArray(function(terr, result){
                        if (result[0] && typeof result[0].name == 'string'){
                            if (fs.existsSync(result[0].path + result[0].name)) {
                                fs.unlink(result[0].path + result[0].name, function (err) {
                                    if (err) throw err;
                                    fw.log('successfully deleted '+result[0].name);
                                });
                            }
                        }
                    });
                    callback();//find成功了就进入下一环节
                });
            });
        },
        beforeUpdate : function(serverCollection, structData, userinfo, callback){
            //更新记录的同时更新文件
            getDb(serverCollection.baseModel,function(err, handler){
                handler.find({smr_id:ObjectId(structData.smr_id)},{},function(err, data){
                    data.toArray(function(terr, result){
                        if (result[0] && typeof result[0].name == 'string'){
                            if (fs.existsSync(result[0].path + result[0].name) && !fs.existsSync(fw.config.get("upload_dir")+ "/"+structData.name)) {
                                fs.renameSync(result[0].path + result[0].name, fw.config.get("upload_dir")+ "/"+structData.name);
                                if (result[0].isTmp) {
                                    structData.isTmp = false;
                                    structData.path = fw.config.get("upload_dir")+ "/";
                                }
                                callback(structData);//进入下一环节
                            }
                        }else{
                            return false;
                        }
                    });
                });
            });
        },
     });

```
### 3. router定义上传
哪一个`uri`是用于处理文件上传的，可由开发者在`router`中通过`pattern`自由定义。

```js
sumeru.router.add({
    {
        pattern    :   '/files', //pattern用于定义匹配上传文件的uri
        type  :   'file'
    }
});
```
### 4. 浏览器端上传文件
此模块支持`显示进度`，`无刷新上传`，`自定义样式/uri`，`上传/修改/删除等对文件的控制`。

```js
var myUploader = new fileUploader({
            // fileName:"myuploadFile",
            routerPath:"/files",
            form:document.getElementById("upload_form"),
            inputFile:document.getElementById("myfile1"),
            sizeAllowed:"10M",
            typeAllowed:[],
            success:function(e){//成功之后的处理，此处有保存文件的逻辑
                var oUploadResponse = document.getElementById('upload_response');
                oUploadResponse.innerHTML = e.target.responseText;
                oUploadResponse.style.display = 'block';
                //保存文件，session.fileObj 是前面定义的订阅
                //e.target.responseText 是服务器返回的文件路径
                session.fileObj.add({name:e.target.responseText,size:this.fileSize,extension:this.extension});
                session.fileObj.save();
                
            
                document.getElementById('progress_percent').innerHTML = '100%';
                document.getElementById('progress').style.width = '400px';
                document.getElementById('filesize').innerHTML = this.fileSize;
                document.getElementById('remaining').innerHTML = '| 00:00:00';
            },
            select:function(e){//用户选择文件之后的处理
                var oFile = e.target.files[0];
                
                var oImage = document.getElementById('preview');
                // prepare HTML5 FileReader
                var oReader = new FileReader();
                oReader.onload = function(e){
                    if (oFile.type == 'image/jpeg'){
                        oImage.src = e.target.result;
                        oImage.onload = function () { // binding onload event
                            console.log(oFile,oReader);
                        };
                    }
                    
                };
                oReader.readAsDataURL(oFile);
            },
            progress:function(e,me){//进度更新
                if (e.lengthComputable) {
                    
                    document.getElementById('progress_percent').innerHTML = me.iPercentComplete.toString() + '%';
                    document.getElementById('progress').style.width = (me.iPercentComplete * 4).toString() + 'px';
                    document.getElementById('b_transfered').innerHTML = me.iBytesTransfered;
                    if (me.iPercentComplete == 100) {
                        var oUploadResponse = document.getElementById('upload_response');
                        oUploadResponse.innerHTML = '<h1>Please wait...processing</h1>';
                        oUploadResponse.style.display = 'block';
                    }
                    document.getElementById('speed').innerHTML = me.iSpeed;
                    document.getElementById('remaining').innerHTML = '| ' + fileUploader.secondsToTime(me.secondsRemaining);
                } else {
                    document.getElementById('progress').innerHTML = 'unable to compute';
                }
            },
            error:function(e){//出错
                document.getElementById('error2').style.display = 'block';
            },
            abort:function(e){//中断
                document.getElementById('abort').style.display = 'block';
            },
        });
```

开始上传：
```js
myUploader.startUpload();
```

