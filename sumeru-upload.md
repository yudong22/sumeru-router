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
        upload_dir:"public",//default dir is public
        rename:function(filename){//if rename_function is defined,the uploaded filename will be deal with this function.
            return filename;
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
        target:document.getElementById("myfile1"),
        onSuccess:function(fileObj){//成功之后的处理，此处有保存文件的逻辑
            var oUploadResponse = document.getElementById('upload_response');
            oUploadResponse.innerHTML = fileObj.name;
            oUploadResponse.style.display = 'block';
            document.getElementById('hidden_file').innerHTML = fileobj.name;//用于用户使用/保存
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
        onProgress:function(e,me){//进度更新
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
        onError:function(e){//出错
            document.getElementById('error2').style.display = 'block';
        },
        onAbort:function(e){//中断
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
myUploader.onSuccess = function(fileobj){//on success
    //fileobj contains name,link,size
    document.getElementById('hidden_file').innerHTML = fileobj.name;//用于用户使用/保存
}
```

### 3. 用户上传头像，一个简单的demo
html示例
```html
<form id="upload_form" enctype="multipart/form-data" method="post">
    <div>
        <div><label for="myfile1">Please select image file</label></div>
        <div><input type="file" name="myfile1" id="myfile1" /></div>
    </div>
    <div>
        <input type="button" id="startupload" value="Upload" />
    </div>
    <div id="fileinfo">
        <div id="filename"></div>
        <div id="filesize"></div>
        <div id="filetype"></div>
        <div id="filedim"></div>
    </div>
    <div id="error">You should select valid image files only!</div>
    <div id="error2">An error occurred while uploading the file</div>
    <div id="abort">The upload has been canceled by the user or the browser dropped the connection</div>
    <div id="warnsize">Your file is very big. We can't accept it. Please select more small file</div>

    <div id="progress_info">
        <div id="progress"></div>
        <div id="progress_percent">&nbsp;</div>
        <div class="clear_both"></div>
        <div>
            <div id="speed">&nbsp;</div>
            <div id="remaining">&nbsp;</div>
            <div id="b_transfered">&nbsp;</div>
            <div class="clear_both"></div>
        </div>
        <div id="upload_response"></div>
    </div>
</form>
<form name="user_save_form"  method="post">
    <ul>
        <li>用户名：<input type="text" id="user_save_name"/> </li>
        <li>电话：<input type="number" id="user_save_phone"/> </li>
        <li><button id="user_save_button">保存</button></li>
    </ul>
    <input type="hidden" name ="hidden_file" id ="hidden_file" /><!--存放存储的用户头像-->
</form>
```
js示例
```js
    var myUploader = new fileUploader({
        routerPath:"/files",
        form:document.getElementById("upload_form"),
        target:document.getElementById("myfile1"),
        onSuccess:function(fileObj){//成功之后的处理，此处有保存文件的逻辑
            var oUploadResponse = document.getElementById('upload_response');
            oUploadResponse.innerHTML = fileObj.name;
            oUploadResponse.style.display = 'block';
            document.getElementById('hidden_file').innerHTML = fileobj.name;//用于用户使用/保存
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
        onProgress:function(e,me){//进度更新
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
        onError:function(e){//出错
            document.getElementById('error2').style.display = 'block';
        },
        onAbort:function(e){//中断
            document.getElementById('abort').style.display = 'block';
        },
    });
        
    var save_user_info = function(){
        var user = {
            name:document.getElementById("user_save_name").value,
            phone:document.getElementById("user_save_phone").value,
            photo:document.getElementById("hidden_file").value
        };
        session.pubuser.add(user);
        session.pubuser.save();
    }
    document.getElementById("user_save_button").addEventListener("click",function(){
        
        if (document.getElementById("hidden_file").value == "") {//未上传图片
            if (document.getElementById("myfile1").value == "") {//未选择图片
                alert("your photo cant't be empty ");
                return false;
            }else{
                myUploader.onSuccess = function(fileObj){//on success
                    //fileobj contains name,link,size
                    var oUploadResponse = document.getElementById('upload_response');
                    oUploadResponse.innerHTML = fileObj.name;
                    oUploadResponse.style.display = 'block';
                    document.getElementById('hidden_file').innerHTML = fileObj.name;//用于用户使用/保存
                    //触发保存
                    save_user_info();
                }
                myUploader.startUpload();//先上传图片，等待返回
                return false;
            }
        }
        save_user_info();
        return false;
    });
```
