## URI

uri改造后，所有链接到本域下的a标签里面的link会被自动阻止默认行为，进行执行sumeru.redirect("link.")进行替代;

* #### 标准格式
  link里面的格式推荐格式：/{controller}/{arguments[1]}/{arguments[2]}/...?params1=string&params2=string

* #### 向前兼容
  以前的sumeru url的link的格式：#/{controller}!params1=string&params2=string，也会被自动解析为上面的标准格式

## Router

router用于建立URL中params与Controller之间的对应关系，添加router的操作通常在Controller文件中一些定义。
router不仅可以运行与client端，同时也被server端渲染支持

一个Controller可以对应多个URL，一个URL只能对应一个Controller。

server router 默认是开启的，当有不需要开启server render时，
可以在app目录下config目录，添加设置,即可关闭，除了全局开关以外，还支持单个controller的禁用，如下add方法介绍

	fw.config.set('runServerRender',false);


* #### add()

  使用add()可以在router添加一组params与Controller的对于关系，方法如下：

	sumeru.router.add(

		{
			pattern: '/studentList',
			action: 'App.studentList',
      		}

	);
  
  	sumeru.router.add(

  		{
			pattern: '/studentList/index',
			action: 'App.studentList',
			server_render:false
      		}

	);

	* #### pattern

		URL中的匹配值，patten中的匹配项会作为controller进行匹配，
		特别的，当出现上面例子中都有 /studentList 的匹配中，会自动选取最长的进行匹配
    
	* #### action

		对应 可执行的JS的名称
	
 * ### 对一个链接的解析中，解析完controller之后，还有已/为分割的参数,如：
	
		localhost:8080/debug.html/studentList/index/123/007?p=2
		
	* #### arguments

		除了自动匹配到 /controller是/studentList/index之外，还会将后面的参数 /123/007 作为
		arguments传入env.arguments中，供程序直接使用。 
		env.arguments形如
		["/studentList/index","123","007"]
       
       * #### params
       		params的取法可以用
       		session.get("p")，也可以用params.p  以上两个结果都是"2"
       
  * #### server_render

	设置了此项，此controller会禁用server渲染
	
	在router中添加了hash与Controller的对应关系后，
	
	就可以使用“localhost:8080/debug.html/studentList”运行params为"/studentList"对应的Controller。

	同时我们还提供定义默认启动Controller的方法：

* #### setDefault()

		sumeru.router.setDefault('App.studentList');
	
	在Controller中使用setDefault()后，浏览器中输入“localhost:8080/debug.html”就可以启动该Controller，不需要在URL中带hash。

* ### url controller
      直接使用&lt;a href="/{controller}"&gt;标签，对于本域内的sumeru framework会自动使用history api进行无刷新页面跳转
      
* ### url的params与session
      启用新的router之后,在session变化以后，url会自动把session中保存的数据已明文参数的形式添加或更新原有url，以便于复制。
	简而言之，session中的数据与params的数据统一为一份，session改变，params同样改变。	

* ### 简写
    localhost:8080/{controller}默认通过index.html，如果需要调试或指定其他页面，可以自己指定localhost:8080/debug.html/{controller}
  
