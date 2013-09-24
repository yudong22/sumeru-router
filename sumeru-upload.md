Clouda提供了从端到云上传文件的方法，使用简单几步的配置即可实现端上的文件上传到托管服务器。

此文档通过一个例子来展示如何通过 Clouda 进行文件上传。

### 1. router定义上传
在`app/config/router.js`中，指定`uri`用于处理文件上传，可由开发者通过`pattern`自由定义。

```js
sumeru.router.add({
    {
        pattern    :   '/files', //pattern用于定义匹配上传文件的uri
        type  :   'file',
        max_size_allowed:'10M',//support k,m
        file_ext_allowed:'' ,//allow all use '' , other use js array ["jpg",'gif','png','ico']
        rename:function(filename){//if rename_function is defined,the uploaded filename will be deal with this function.
            return "public/"+filename;
        }
    }
});
```
### 2. 浏览器端上传文件
此模块支持`显示进度`，`无刷新上传`，`自定义样式/uri`，`上传文件的服务端保存`。

```js
var myUploader = new fileUploader({
            routerPath:"/files",
            form:document.getElementById("upload_form"),
            inputFile:document.getElementById("myfile1"),
            sizeAllowed:"10M",
            typeAllowed:[],
            success:function(fileObj){//成功之后的处理，此处有保存文件的逻辑
                var oUploadResponse = document.getElementById('upload_response');
                oUploadResponse.innerHTML = fileObj.name;
                oUploadResponse.style.display = 'block';
            },
            fileSelect:function(e){//用户选择文件之后的处理
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

客户端上传文件方法：
```js
myUploader.startUpload();
```

客户端覆盖定义上传成功方法：
```js
myUploader.success = function(fileobj){//on success
    //fileobj contains name,link,size
    document.getElementById('hidden_file').innerHTML = fileobj.name;//用于用户使用/保存
}
```

