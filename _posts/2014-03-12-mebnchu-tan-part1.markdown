---
layout: post
title: "MEBN初探-Part1"
date: 2014-03-12 18:05:43 +0800
comments: true
categories: Node.js
---

>对Node.js一直跃跃欲试，最近终于忍不住尝试了下,搭建一个MEBN版"Todo List",主要实现对数据库的CRUD操作，熟悉整个开发流程。 (MongoDB+Express+Bootstrap3.0+Node.js)

##最终效果图如下:
![最终效果图](http://ww3.sinaimg.cn/large/775c483ftw1eed5da9ya6j213b0idab8.jpg)

 
##安装开发环境
 
#####开始之前确定你已经安装了 Node.js, Express 和 MongoDB, 如果沒有可以參考下列链接.

* [安装MongoDB](http://www.mongodb.org)
* [安装express](http://expressjs.jser.us/)  
* [下载Bootstrap3.0](http://v3.bootcss.com/)  
* [安装Node.js](http://nodejs.org)    

##步骤

>1.用 Express的command line工具生成一個project雏形

<pre><code> 
$ express todo -e

create : todo
create : todo/package.json
create : todo/app.js
create : todo/public
create : todo/public/javascripts
create : todo/public/stylesheets
create : todo/public/stylesheets/style.css
create : todo/routes
create : todo/routes/index.js
create : todo/routes/user.js
create : todo/public/images
create : todo/views
create : todo/views/index.ejs
</code></pre>

>2.编辑 package.json
<pre><code> 
{
  "name"         : "todo",
  "version"      : "0.0.1",
  "private"      : true,
  "dependencies" : {
    "express"    : "3.4.7",
    "ejs"        : "0.8.5",
    "mongoose"   : "3.8.1"
  }
}
</code></pre>

>3.安裝 dependencies,启动项目。(安装mongoose时不稳定，需翻墙)
<pre><code> 
$ cd todo && npm install
$ node app.js
</code></pre>
 

>4.MongoDB以及Mongoose的设置。然后在index.js里require。      
<pre><code>
首先$ mongod 启动 MongoDB

然后在根目录下新增一個db.js来设置MongoDB和定义schema.

var mongoose = require( 'mongoose' );
var Schema   = mongoose.Schema;

var Todo = new Schema({
    user_id    : String,
    content    : String,
    updated_at : Date
});

mongoose.model( 'Todo', Todo );
mongoose.connect( 'mongodb://localhost/express-todo' );
</code></pre>
>5.修改 index view.主要是获取列表和添加事件的功能。

<pre><code>
	form action="/create" method="post" accept-charset="utf-8"
	input type="text" name="content" 
 	form 
</code></pre>

>6.更改routes/index.js,添加处理函数.
<pre><code>
首先先 require mongoose 和 Todo model.

var mongoose = require( 'mongoose' );
var Todo     = mongoose.model( 'Todo' );

新增成功后返回首页.

exports.create = function ( req, res ){
  new Todo({
    content    : req.body.content,
    updated_at : Date.now()
  }).save( function( err, todo, count ){
    res.redirect( '/' );
  });
};

获取首页信息列表

exports.index = function ( req, res ){
  Todo.find( function ( err, todos, count ){
    res.render( 'index', {
        title : 'Express Todo Example',
        todos : todos
    });
  });
};

将这个新增的路由规则加到app.js的routes中.
</code></pre>


>7.渲染views/index.ejs
<pre><code>
遍历数据列表

<% todos.forEach( function( todo ){ %>
  <%= todo.content %> 
<% }); %>
</code></pre>

>8.刪除一个实体

<pre><code>
 routes/index.js   
 // 根据 id 删除
 exports.destroy = function ( req, res ){
  Todo.findById( req.params.id, function ( err, todo ){
    todo.remove( function ( err, todo ){
      res.redirect( '/' );
    });
  });
};
修改views/index.ejs 
<% todos.forEach( function ( todo ){ %>
  <%= todo.content %>
    a href="/destroy/<%= todo._id %>" title="Delete this todo item">Delete</a>
<% }); %>
将这个新增的路由规则加到app.js的routes中.
app.get( '/destroy/:id', routes.destroy );
</code></pre>

>9.Just Node app!

 <pre><code>
 安装supervisor,提高测试效率
 直接用npm安装既可，键入命令: npm -g install supervisor
 这里注意一点的就是，supervisor必须安装到全局，如果你不安装到全局，错误命令会提示你安装到全局。
 如果不想安装到默认的全局,也可以自己修改全局路径到当前路径
 npm config set prefix "路径"
 安装完以后就可以用supervisor 来启动服务了。
 supervisor app.js
</code></pre>
 

>到此为止我们实现了添加/获取/删除的功能点，是不是很简单？赶快加入Node.js的阵营吧！刚刚上手Markdown，写文章还是不习惯，太费劲了。。看来要经常练习了 :0


##github地址 : [https://github.com/melonlee/Express-Demo](https://github.com/melonlee/Express-Demo)

