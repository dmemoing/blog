title: 跨平台开发TODO实例文档
date: 2017-04-12
tags: [ionic,angular,cordova]
---
我们要完成一个TODO的应用，完成后的TODO应用如下图
![TODO实例](/images/todo/step1.png)
[github](https://github.com/dmemoing/todo-project)上有实例的完整示例代码

## ionic安装
ionic的安装需要nodejs的环境，[官网](https://nodejs.org/en/)上有各个版本系统的安装包，下载安装即可。安装完成后，npm工具就应该可以使用了，通过命令`npm -v`查看是否成功安装。

ionic官网上可以找到[getting-started](http://ionicframework.com/docs/v1/getting-started/)文档，我们使用`ionic v1`版本，介绍了安装，创建初始项目，运行，添加platform，打包，模拟器模拟的命令。

## 创建空项目
安装完成环境后，我们开始我们应用的构建。
命令行输入`ionic start todo blank`，生成空项目，项目结构如下。
![项目架构](/images/todo/ionic.png)
我们之后实现TODO实例所需要修改的代码都在`www`文件夹中。

创建完成后，进入该文件夹，通过命令`ionic serve`，启动创建好的空项目，会自动打开浏览器，显示自动生成的空项目的内容，推荐使用chrome作为默认浏览器，按`F12`进入开发者模式，左上角点击![](/images/todo/button.png)图标，切换到响应式开发模式，这是网页被切换到手机屏幕大小，方便我们开发移动应用。运行效果如下图。
![空视图](/images/todo/blank.png)

## 设置路由
我们将实现一个多页面的应用，涉及到路由的跳转及页面切换，如何实现路由及页面跳转，首先我们需要在`index.html`中插入`<ion-nav-view>`标签，作为页面加载的位置
```
<body ng-app="starter">
  <ion-nav-bar class="bar-dark"></ion-nav-bar>
  <ion-nav-view></ion-nav-view>
</body>
```
页面会加载到该标签所在的位置，该标签起到一个定位的作用。

路由的跳转，将使用`$stateProvider`来实现，首先我们创建一个初始路由，在`www/app.js`文件中添加如下代码：
```
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider
    .state('tasks', {
      url: '/tasks',
      templateUrl: 'templates/tasks.html',
      controller: 'TasksController'
    });
  $urlRouterProvider.otherwise('/tasks');
});
```
`$stateProvider`根据状态进行跳转，我们最初的状态是`tasks`，改状态对应的路由是`/tasks`，对应的模板是`templates/tasks.html`，控制器为`TasksController`，`$urlRouterProvider.otherwise`设置`/tasks`作为初始路由，初始启动时该状态的模板会被加载到上面的`<ion-nav-view>`的位置，模板和控制器对应的文件我们都还没有创建，接下来我们来创建这两个文件。

## tasks状态
初始状态tasks将用list列表显示我们所有添加的task，所以在模板中我们应该将其定义出来，在`www`文件夹下创建`templates`文件夹，用来存储所有的状态的模板，我们在该文件夹下创建`tasks.html`文件，内容为
```
<ion-view view-title="Todo">
    <ion-nav-buttons side="right">
        <button class="button button-icon" ui-sref="create">
            <i class="icon ion-compose"></i>
        </button>
    </ion-nav-buttons>
    <ion-content>
        <!-- our list and list items -->
        <ion-list>
            <ion-item ng-repeat="task in tasks">
                {{task.title}}
            </ion-item>
        </ion-list>
    </ion-content>
</ion-view>
```
设置页面的标题为`TODO`，在`<ion-content>`中添加一个list列表，插入`<ion-list>`标签，列表中的每一项由`<ion-item>`定义，我们要将tasks填入其中，通过`ng-repeat`属性，类似于for循环，取出tasks数组中的每个元素，每个元素是一个json格式的对象，即其中数据以键值对的形式存储，我们获取其中title键对应的值，也就是我们所添加的一个task的内容，我们通过数据绑定的形式将数据绑定到指定位置，通过双大括号来实现，在双大括号中填入需要绑定的变量名，即可将其绑定，这样我们就在每个`<ion-item>`中显示了我们的task的内容。

以上我们就完成了`tasks.html`模板的实现，模板控制页面内容显示的格式，具体的数据需要在控制器中给出，我们在`www/js`下创建`controllers`文件夹用来存储所有的控制器，在该文件夹下创建`TasksController.js`文件，实现状态tasks的控制器，具体代码如下：
```
angular.module('starter.controllers')
.controller('TasksController', function ($scope) {
  $scope.tasks = [
    { title: 'Collect coins' },
    { title: 'Eat mushrooms' },
    { title: 'Get high enough to grab the flag' },
    { title: 'Find the Princess' }
  ];
});
```
这里需要解释一下`$scope`的概念，每个控制器都对应着一个域，每个控制器控制着html模板中的一部分视图，在该部分视图中使用的变量或者函数都需要定义在这个域中，反过来说，只要你要`$scope`中定义了的变量和函数，你都可以在相应视图中直接使用。我们这里先将静态的值赋给tasks数组。我们这里创建了一个控制器模块`starter.controllers`，我们需要在`app.js`文件中申明才能在项目中使用，将该模块添加到主模块中：
```
angular.module('starter', ['ionic', 'starter.controllers'])
```
同时在最下面申明：
```
angular.module('starter.controllers', []);
```

另外添加的新的控制器需要在`index.html`中进行申明，即在`index.html`中`<head>`标签范围内添加，我们之后编写的js代码同样需要添加到`index.html`文件中
```
<!-- your app's js -->
<script src="js/app.js"></script>
<script src="js/controllers/TasksController.js"></script>
```
具体各个申明添加的位置请在[github](https://github.com/dmemoing/todo-project)上给出的示例代码中找到，正确添加。

此时我们看到的运行效果应该如下：
![list](/images/todo/list.png)


## 跳转到create页
我们需要添加新的task，我们通过在标题栏的右边添加一个按钮，点击创建弹出新建task的页，这里涉及到页面跳转，即状态转换，在上述`tasks.html`模板中，有如下代码：
```
<ion-nav-buttons side="right">
    <button class="button button-icon" ui-sref="create">
        <i class="icon ion-compose"></i>
    </button>
</ion-nav-buttons>
```
这段代码在标题栏右侧添加了一个编辑的按钮，同时`ui-serf`属性使得点击该按钮后，状态会转换到`create`状态，也就是我们用来新建task的状态，`create`状态我们还没有创建，接下来我们添加`create`状态，在`app.js`的状态中添加：
```
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider
    .state('tasks', {
      url: '/tasks',
      templateUrl: 'templates/tasks.html',
      controller: 'TasksController'
    })
    .state('create', {
      url: "/tasks/create",
      templateUrl: "templates/create.html",
      controller: "CreateController"
    });
  $urlRouterProvider.otherwise('/tasks');
});
```
相应的我们需要创建其模板和控制器， 模板`create.html`如下：
```
<ion-view view-title="New Task">
	<ion-nav-buttons side="right">
		<button class="button button-clear button-white" ui-sref="tasks">Cancel</button>
	</ion-nav-buttons>

	<ion-content>
		<form ng-submit="createTask(task)">
			<div class="list">
				<label class="item item-input">
					<input type="text" placeholder="What do you need to do?" ng-model="task.title">
				</label>
			</div>
			<div class="padding">
				<button type="submit" class="button button-block button-positive">Create Task</button>
			</div>
		</form>
	</ion-content>
</ion-view>
```
模板中我们创建了一个表单，用来填写新的task并创建，包含一个输入框和一个按钮，表单拥有`ng-submit`属性，将`createTask()`函数赋值给该属性，表明该表单的提交交给该函数来处理，我们又一个输入的值，如何获取这个值并且传给函数，这里用到`ng-model`将输入框的值绑定到`task`变量的`title`键上，之后再将task变量传给函数作为参数。

控制器中：
```
angular.module('starter.controllers')
.controller('CreateController', function ($state, $scope, Tasks) {
  $scope.createTask = function(task) {
    Tasks.save(task);
    $state.go("tasks");
    //console.log(task);
  };
});
```
我们需要将新创建的`task`存储起来，我们新建一个`Tasks`服务来帮助我们进行该操作。
新建一个服务模块，并创建一个`factory`用来处理数据，在`www/js`文件夹下创建`services.js`文件：
```
angular.module('starter.services')
.factory('Tasks', function() {
	return {
		all: function() {
			var taskString = window.localStorage['tasks'];
			if(taskString) {
				return angular.fromJson(taskString);
			}
			return [];
		},
		save: function(task) {
			var tasks = [];
			var taskString = window.localStorage['tasks'];
			if(taskString) {
				tasks = angular.fromJson(taskString);
			}
			tasks.push(task);
			window.localStorage['tasks'] = angular.toJson(tasks);
		}
	}
})
```
这里我们创建了`all`和`save`两个函数，`all`函数用来在从`localStorage`中获取当前所有的tasks，在状态tasks加载时，会调用该函数，初始化`$scope.tasks`，所以`TasksCtronller.js`文件可以修改如下：
```
angular.module('starter.controllers')
.controller('TasksController', function ($rootScope, $scope, Tasks) {
	$scope.tasks = Tasks.all();
});
```
而`save`函数使用来存储一个新创建的task到`localStorage`中，所以在`createTask`函数中调用该函数来存储新创建的task，在创建完task后，我们需要跳转会tasks状态，使用`$state.go("tasks");`实现状态的跳转，另外页面标题的右侧添加`cancel`按钮，用来取消创建，直接返回tasks状态，通过`ui-serf`来实现跳转。

完成上述创建后，同样需要在`index.html`和`app.js`文件中进行申明：
`index.html`
```
<!-- your app's js -->
<script src="js/app.js"></script>
<script src="js/services.js"></script>
<script src="js/controllers/TasksController.js"></script>
<script src="js/controllers/CreateController.js"></script>
```
`app.js`
```
angular.module('starter', ['ionic','starter.controllers','starter.services'])

angular.module('starter.controllers', ['starter.services']);
angular.module('starter.services', []);
```
具体如何申明请参见[github](https://github.com/dmemoing/todo-project)代码

## 清除缓存
我们的实现已经基本完成，然而我们创建新的task后跳转会列表页面，但列表页面并没有刷新，这是因为angular框架默认对页面有缓存机制，对于已经显示过的页直接显示缓存的内容，不会重新获取数据，我们可以通过在`app.js`文件`.config`中状态配置中来关闭缓存：
```
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider
    .state('tasks', {
      cache: false,
      url: '/tasks',
      templateUrl: 'templates/tasks.html',
      controller: 'TasksController'
    })
    .state('create', {
      url: "/tasks/create",
      templateUrl: "templates/create.html",
      controller: "CreateController"
    });
  $urlRouterProvider.otherwise('/tasks');
});
```
我们设置`tasks`状态的`cache`键为`false`，这样每次跳转到这个状态时，都会重新加载获取数据。

## 打包运行
ionic应用打包十分简单，通过如下两条命令
```
$ ionic platform add android
$ ionic build android
```
以上两条命令就可以完成应用的打包，但这两条命令可以运行的前提是你需要安装`android sdk`，下载和安装都可以在`Android Developer`上找到，这里直接给出[下载链接](https://developer.android.com/studio/index.html?hl=zh-cn)。

打包ios应用也是使用上述类似命令，同样需要ios开发环境才能完成打包。

## 总结及作业
以上我们就完成了我们TODO实例的实现，通过这个实例，我们介绍了复杂应用构建的框架，包括模块、控制器、服务以及路由这些概念，希望通过以上的介绍，能够对前端开发有一个简单的认识。

请首先按照以上文档内容完成TODO实例的基础开发，在此基础上，添加标注完成task的功能，具体为：每个task条目左划显示出complete按钮，点击按钮后，表明该task已经完成，task内容上被划线，同时显示到列表的最下方，图解如下：
![初始状态](/images/todo/step1.png)
![左划显示complete按钮](/images/todo/step2.png)
![点击完成后](/images/todo/step3.png)

左划显示出按钮可以参见[ion-list](http://ionicframework.com/docs/v1/api/directive/ionList/)官方文档中的介绍进行修改；对于如何切换task内容的划线，stackoverflow上的一个问题可供参考[how-do-i-conditionally-apply-css-styles-in-angularjs](http://stackoverflow.com/questions/13813254/how-do-i-conditionally-apply-css-styles-in-angularjs)