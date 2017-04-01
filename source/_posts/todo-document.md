title: 全栈开发TODO实例文档
date: 2017-04-01 16:01:21
tags: [ionic,nodejs,express,mongodb]
---
![TODO总览](/images/todo/todo.png)
这是我们要开发的TODO实例的架构总览，下面我们就来一步步实现这个实例。

## ionic
ionic的安装需要nodejs的环境，[官网](https://nodejs.org/en/)上有各个版本系统的安装包，下载安装即可。安装完成后，npm工具就应该可以使用了，通过命令`npm -v`查看是否成功安装。

ionic官网上可以找到[getting-started](http://ionicframework.com/docs/v1/getting-started/)文档，我们使用`ionic v1`版本，介绍了安装，创建初始项目，运行，添加platform，打包，模拟器模拟的命令。

#### 创建空项目
安装完成环境后，我们开始我们前端项目的构建。
命令行输入`ionic start todo blank`，生成空项目，项目结构如下。
![项目架构](/images/todo/ionic.png)

创建完成后，进入该文件夹，通过命令`ionic serve`，启动创建好的空项目，会自动打开浏览器，显示自动生成的空项目的内容，推荐使用chrome最为默认浏览器，按`F12`进入开发者模式，左上角点击![](/images/todo/button.png)图标，切换到响应式开发模式，这是网页被切换到手机屏幕大小，方便我们开发移动应用。运行效果如下图。
![空视图](/images/todo/blank.png)
#### 添加list
我们需要通过编写一个Web前端来实现app的开发，所以我们需要修改的内容主要是在`www`文件夹中。
在`www`文件夹中，我们主要修改的是`index.html`和`js/app.js`文件。

首先我们需要在`index.html`添加一个list以显示tasks，所以在html文件`<ion-content>`标签范围内添加如下代码：
```html
<ion-list>
  <ion-item ng-repeat="task in tasks">
    {{task.title}}
  </ion-item>
</ion-list>
```
标签`ion-header-bar`显示标题，`ion-content`则显示页面的相应内容，所以我们的list添加在`<ion-content>`标签范围内。

如上代码中，我们还做了数据的绑定，即将控制器中`tasks`数组里的每条数据，都绑定到一个`ion-item`标签中，作为list的一条内容，在angular中通过双大括号实现数据绑定，还有另外一种使用`ng-model`实现绑定的格式，我们会在下面的内容中介绍。

我们还没有定义所需的控制器，在`js/app.js`文件中，我们来定义我们对视图的控制器，将一下代码添加都文件的最后
```javascript
.controller('TodoCtrl', function($scope) {
  $scope.tasks = [
    { title: 'Collect coins' },
    { title: 'Eat mushrooms' },
    { title: 'Get high enough to grab the flag' },
    { title: 'Find the Princess' }
  ];
})
```
我们还需要在html中申明我们的控制器，即该控制器控制哪一部分的视图，在html代码中添加。
```html
<body ng-app="starter" ng-controller="TodoCtrl">
```
此时我们就完成了基本list的构建，同时将控制器中tasks数组绑定到了视图中。这里需要解释一下`$scope`的概念，每个控制器都对应着一个域，每个控制器控制着html模板中的一部分视图，在该部分视图中使用的变量或者函数都需要定义在这个域中，反过来说，只要你要`$scope`中定义了的变量和函数，你都可以在相应视图中直接使用。

此时我们看到的运行效果应该如下：
![list](/images/todo/list.png)

#### 添加输入框和按钮
接下来我们要添加输入框和创建按钮，以实现添加新task的功能。

在`index.html`代码中添加如下表单，添加在`<ion-list>`的上面
```html
<form>
    <label class="item item-input">
      <input type="text" placeholder="What do you need to do?" >
      <button type="submit" class="button button-small button-positive">Create Task</button>
    </label>
</form>
```
这里主要就两个组件，一个`input`输入框，还有一个`button`按钮，此时显示效果如下：
![输入框](/images/todo/input.png)
但我们输入后点击，没有任何效果，这是因为html中只定义视图，具体的控制逻辑需要使用javascript代码来定义，即在控制器中编写。

#### 添加控制逻辑
在添加控制逻辑之前，我们先要对上述`form`表单进行一些简单修改
```html
<form ng-submit="createTask(task)">
  <label class="item item-input">
    <input type="text" placeholder="What do you need to do?" ng-model="task.title">
    <button type="submit" class="button button-small button-positive">Create Task</button>
  </label>
</form>
```
添加`ng-submit`属性，将`createTask()`函数复制给该属性，表明该表单的提交交给该函数来处理，我们又一个输入的值，如何获取这个值并且传给函数，这里用到`ng-model`将输入框的值绑定到`task`变量的`title`键上，之后再将task变量传给函数作为参数。

后台控制其中对于`createTask()`函数的实现如下：
```javascript
// Called when the form is submitted
$scope.createTask = function(task) {
  $scope.tasks.push({
    title: task.title
  });
};
```
其操作是将新建的task添加到tasks数组中，因为我们进行了数据绑定，所以新的数据会自动更新到视图中，不需要我们额外编写任何代码，这也是angular框架很方便的一个地方。

以上一个基本的todo应用就完成了，但此时应用的数据都是没有存储的，只要我们一刷刷新，之前添加的task就全都没有了，这不是我们所预期的，我们希望应用新打开的时候，之前存入的数据还在，因此我们需要引入服务器及数据库的概念。

#### 打包运行
在将后端的内容之前，我们介绍ionic应用打包相关的内容。ionic应用打包十分简单，通过如下两条命令
```
$ ionic platform add android
$ ionic build android
```
以上两条命令就可以完成应用的打包，但这两条命令可以运行的前提是你需要安装`android sdk`，下载和安装都可以在`Android Developer`上找到，这里直接给出[下载链接](https://developer.android.com/studio/index.html?hl=zh-cn)。

打包ios应用也是使用上述类似命令，同样需要ios开发环境才能完成打包。

## mongodb数据库
接下来我们讲解后端的内容。

首先我们介绍数据存储。这里我们使用mongodb数据库来存储我们的数据，`mongodb`以`json`格式存储数据，即键值对的形式，如下是一个json格式的对象：
```
{
	name: "Nancy",
	school: "Nanjing University",
	ID: "141220016"
}
```
在我们上面完成的前端中，我们使用到的数据的格式也是json格式的，所以使用mongodb来存储数据十分方便。

#### mongodb安装
mongodb的安装，给出[官方下载地址](https://www.mongodb.com/download-center?jmp=nav#community)，选择对应的系统下载，下面的`Installation Instructions`提供详细的安装介绍。

#### 运行
安装完成后，我们运行数据库
```
$ mongod -dbpath "path to db directory"
```
后面需要自己规定数据库数据存储的位置，这是启动数据库

启动完成后，我们可以登录进数据库查看
```
$ mongo
```
命令会自动连接到本地的mongodb数据库，一些简单的数据库操作命令如下：
```
//查看具有的db
$ show dbs

//使用默认创建的test数据库
$ use test

//查看数据库具有的collection
$ show collections

//查看collection tasks中所有的document
$ db.tasks.find().pretty()
```
如何用代码来实现对数据库的插入、查找等操作，我们通过`mongoose`插件来进行。`mongoose`则需要结合`express`等框架来使用。

下面介绍服务器端的开发。

## 服务器
我们的服务器使用nodejs编写，为了方便快速开发，我们使用`express`框架。
通过如下命令：
```
$ npm install -g express express-generator
```
安装express项目生成器，安装完成后通过命令
```
$ express -e backend
```
自动生成一个空的项目，具有完善的架构。
空项目创建完成后，进入该项目，运行命令`npm install`安装项目所需的依赖包。
之后项目通过`npm start`来运行，注意每次对代码修改后，都需要重新运行该命令来查看新添加的功能。

#### 创建数据模型
上面我们提到要使用`mongoose`插件，我们通过如下命令安装
```
$ npm install --save mongoose
```
该命令只会讲`mongoose`插件安装到该项目，即将代码下载到`node_modules`文件夹中，不会全局安装，全局安装通过添加`-g`参数来实现，就想上面我们安装`express`一样。

`--save`参数将我们安装的`mongoose`信息写进项目插件的配置文件中，即`package,json`文件，下次只需要使用`npm install`命令就会将所有需要的插件都安装上，不需要每次单独安装。

我们在项目根目录下新建一个文件夹`models`用来存储我们的数据模型，在该文件夹中创建文件`task.js`，其中具体代码如下
```javascript
var mongoose = require('mongoose');

var TaskSchema = new mongoose.Schema({
  title: String,
  create_at: {
    type: Date,
    default: Date.now
  }
});

module.exports = mongoose.model("Task", TaskSchema);
```
首先需要申明使用到的插件，即`require`，将其返回值赋给变量`mongoose`，以备接下来使用。

使用`mongoose.Schema`定义出我们存储数据的模板， 包括两个键值对，一个即`title`，对应一个`String`类型的数据，还有一个键是`create_at`，对应着task创建的时间。

我们使用定义好的这个`TaskSchema`数据模板来定义我们的数据模型`Task`，因为我们需要在别的文件中使用到这个模型来进行创建、查找，所以我们将该模型`exports`出来。

光定义好模型还不行，我们需要先跟mongodb数据库建立连接，在`app.js`文件中添加
```javascript
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test', function(err) {
  if (err) {
    console.log('connection error', err);
  } else {
    console.log('connection successful');
  }
});
```
这样数据模型以及数据库连接就完成了。

#### 定义路由
我们编写后端是为了相应前端发送的请求，接下来我们定义RESTful的接口，以相应前端请求。

在`routes`文件夹中新建文件`task.js`，具体代码为
```javascript
var express = require('express');
var router = express.Router();
var Task = require('../models/task');

router.post("/", function(req, res, next){
	var task = req.body;
	Task.create(task, function(err, task){
		if (err) {
			return res.status(400).send("err in post /task");
		} else {
			return res.status(200).json(task);
		}
	});
});

router.get("/", function(req, res, next){
	Task.find({}, function(err, tasks){
		if(err){
			return res.status(400).send("err in get /task");
		}else{
			console.log(tasks);
			return res.status(200).json(tasks);
		}
	})
});

module.exports = router;
```
主要包含两个接口，一个用来接收post请求，对应着创建task，一个用来接收get请求，对应着获取所有tasks，这两个请求接收到后，都通过已经定义好的`Task`模型，完成对数据库的操作，之后再返回消息给前端。

定义完接口后，我们需要在`app.js`文件中申明并使用它
```
var task = require('./routes/task');
app.use('/task', task);
```

同时，我们需要使用另外一个插件`cors`，用来处理我们收到请求的头部信息，通过命令`npm install --save cors`安装，在`app.js`代码中添加
```
var cors = require('cors');
app.use(cors());
```

以上我们的服务器代码就完成了，使用命令`npm start`运行服务器。
我们可以使用[postman](https://www.getpostman.com/)来模拟发送请求到服务器，验证服务器是否正确接收请求并返回正确信息。
![post请求](/images/todo/post.png)
![get请求](/images/todo/get.png)
以上是模拟发送post和get请求。

## 前后端连接
接下来我们要修改前端代码，是应用发送相应请求到服务器，实现数据的存储和读取。

主要修改js部分的代码，我们创建一个`Factory`，专门用来跟服务器通信。
```javascript
.factory('Tasks', function($http) {
  var base = "http://localhost:3000";
  return {
    all: function() {
      return $http.get(base + "/task");
    },
    save: function(task) {
      return $http.post(base + "/task", {title: task.title});
    }
  }
})
```
之后我们在控制器中使用
```javascript
.controller('TodoCtrl', function($scope, $ionicModal, Tasks) {
  Tasks.all()
    .success(function(tasks){
      $scope.tasks = tasks;
      console.log($scope.tasks);
    })
    .error(function(){
      $scope.tasks = [];
    })

  // Called when the form is submitted
  $scope.createTask = function(task) {
    $scope.tasks.push({
      title: task.title
    });
    Tasks.save(task)
      .success(function(task){
        console.log(task);
      })
      .error(function(){
        console.log("request error");
      });
  };
})
```
分别是在应用刚开始运行的时候，调用`all()`函数，后去所有数据库中已经存储了的task，另外就是创建新的task的时候，将新的task存储到数据库。回调函数的内容在这里不再赘述。

这样我们的从前端到后端到数据存储的一个完整的应用就完成了，新建的task都会存储到服务器，每次应用打开都会显示已经创建的所有的tasks。

整个项目的代码都放在[github](https://github.com/dmemoing/TodoDemo)上。

希望同学们在按照这篇文档实践后，能够掌握全栈开发的技术，完成后续功能的添加。
