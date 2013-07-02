## Router

router用于建立URL中params与Controller之间的对应关系，添加router的操作通常在Controller文件中一些定义。
router不仅可以运行与client端，同时也被server端渲染支持

一个Controller可以对应多个URL，一个URL只能对应一个Controller。

* #### add()

  使用add()可以在router添加一组params与Controller的对于关系，方法如下：

	sumeru.router.add(

		{
			pattern: '/studentList',
			action: 'App.studentList',
      		srender:false //关闭server渲染
		}

	);
  
  	sumeru.router.add(

  		{
			pattern: '/studentList',
			action: 'App.studentList',
      		srender:'index.js' //开启server渲染
		}

	);

	* #### pattern

		URL中params第一部分的值，
    
	* #### action

		对应Controller的名称
      
  * #### srender

		会自动从controller目录下读取此项js文件,启用server渲染
	
在router中添加了hash与Controller的对应关系后，
就可以使用“localhost:8080/debug.html/studentList”运行params为"/studentList"对应的Controller。

同时我们还提供定义默认启动Controller的方法：

* #### setDefault()

		sumeru.router.setDefault('App.studentList');
	
在Controller中使用setDefault()后，浏览器中输入“localhost:8080/debug.html”就可以启动该Controller，不需要在URL中带hash。

* ### url controller
      直接使用&lt;a href="/{controller}"&gt;标签，对于本域内的sumeru framework会自动使用history api进行无刷新页面跳转
      
* ### 简写
    localhost:8080/{controller}默认通过index.html，如果需要调试或指定其他页面，可以自己指定localhost:8080/debug.html/{controller}
  
