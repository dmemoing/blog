title: react和angular2比较
date: 2016-03-04 15:00:03
category: js
tags: [react,angular2,js]
---
本篇将介绍react和angualr2的基本使用方法，包括它们的语言、视图、数据、事件等放方面，同时会对两中js框架进行比较。

<!--more-->
# REACT
## JSX
react推荐使用JSX来编写代码，JSX的语法类似于XML，对定义的类可以像使用HTML标签来实例化，使得在具有很多组件，需要编写树状结构时比直接使用JavaScript更加直观。
在运行时react会将JSX的代码转换为JS代码。现在我们定义了一个Nav类，使用和转换的效果为：
```javascript
var Nav;
// Input (JSX):
var app = <Nav color="blue" />;
// Output (JS):
var app = React.createElement(Nav, {color:"blue"});
```
使用HTML标签实例化时，如果有参数则在标签中添加。react支持使用HTML标签的方式定义多层结构。react中的类一般通过`React.createClass()`函数定义。

在标签中添加参数时，如果是表达式注意使用`{}`括起来。
```javascript
var person = <Person name={window.isLoggedIn ? window.name : ''} />;
```
## View创建
一个简单的创建视图的示例如下：
```
  <body>
    <div id="content"></div>
    <script type="text/babel">
			var CommentList = React.createClass({
			  render: function() {
				return (
				  <div className="commentList">
					Hello, world! I am a CommentList.
				  </div>
				);
			  }
			});
			var CommentBox = React.createClass({
			  render: function() {
				return (
				  <div className="commentBox">
					<h1>Comments</h1>
					<CommentList />
				  </div>
				);
			  }
			});
			ReactDOM.render(
			  <CommentBox />,
			  document.getElementById('content')
			);
    </script>
  </body>
```
`React.createClass()`函数中的`render()`函数会返回所有的依赖到HTML中的React组件，而`ReactDOM.render()`函数会将最终产生的所有React组件插入`content`所指向的相应位置。`CommentBox`类中通过`<CommentList />`标签创建了`CommentList`类的实例，之后又将`<CommentBox />`的实例插入到HTML中的`comment`位置，完成React组件的定位。

运行效果如下：
![](/images/example1.png)
**注意**JSX中的所有的传统的HTML标签同样是React组件类型，在转换时同样会被转换成通过`React.createElement()`函数创建实例。

## 数据传递
React使用两中类型的变量传递数据，分别是`props`和`state`。
### Props
Props是指每个类自己的属性，这些属性一般是以参数的形式传给类的。
```
var Comment = React.createClass({
  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        {this.props.children}
      </div>
    );
  }
});

var comment = <Comment author="Pete Hunt">This is one comment</Comment>;
```
### State
State是指整个应用的状态信息，可以将React组件组成的应用想象成一个状态机，而这些状态是由**从后台获取的数据**和**用户的输入数据(包括用户的操作)**组成，状态的变换推动整个应用的运行，例如从后台获取了数据，使得状态发生了变化，相应的视图就会根据获得的数据做出相应的更新。这是一种简单的数据的单向绑定，视图根据数据的变换而变化。
某个类的状态信息是属于这个类的实例的，别的组件想要获得并使用这些状态只能通过参数的传递来进行，也就是使用props。
```
var CommentBox = React.createClass({
  getInitialState: function() {
    return {data: []};
  },
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, 2000);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
      </div>
    );
  }
});
```
`getInitialState()`函数在组件创建时被调用，初始化state中的data数据为空，`componentDidMount()`函数在视图render后会被自动调，该函数首先通过ajax从后台获取数据，并将数据通过`setState()`方法更新到state中，并且设置计时函数每个2秒调用一次`loadCommentsFromServer()`函数更新数据。
在`<CommentList data={this.state.data} />`语句中，state中的data数据被以参数的形式传递给了子组件。

## 事件
可以对组件已有的时间设置监听，例如在`<input>`组件中给onChange添加回调函数实现对改变事件的监听，一旦输入变化就会触发事件，调用`handleAuthorChange()`事件处理函数。
```
	<input
	  type="text"
	  placeholder="Your name"
	  value={this.state.author}
	  onChange={this.handleAuthorChange}
	/>
```
自定义事件，首先要定义事件名称，并且设置回调函数，在触发事件时，调用事件的名称并传入需要的参数，交给回调函数处理。

# angular2
angular2官方文档目前只支持Typescript，以下内容均是基于Typescript。
## View创建
```
import {Component} from 'angular2/core';

@Component({
    selector: 'my-app',
    template: '<h1>My First Angular 2 App</h1>'
})
export class AppComponent { }
```
通过在`template`中使用HTML标签定义视图显示格式，`import`对我们需要的组件进行引用，`@component`告诉angular如何创建该组件，最后的`export`控制视图的数据和方法，可以将数据和方法定义在后面的括号中。该示例中视图不需要数据和方法，所以为空。

运行时，应用会通过选择器找到`my-app`标签的位置，并将`AppComponent`插入到相应的位置，替换掉原来的内容。

包含子组件的情况将在下面介绍数据传递的时候一并介绍。
## 数据传递
**app.component.ts**
```
import {Component} from 'angular2/core';
import {Hero} from './hero';
import {HeroDetailComponent} from './hero-detail.component';
@Component({
  selector: 'my-app',
  template:`
    <h1>{{title}}</h1>
    <h2>My Heroes</h2>
    <ul class="heroes">
      <li *ngFor="#hero of heroes"
        [class.selected]="hero === selectedHero"
        (click)="onSelect(hero)">
        <span class="badge">{{hero.id}}</span> {{hero.name}}
      </li>
    </ul>
    <my-hero-detail [hero]="selectedHero"></my-hero-detail>
  `,
  directives: [HeroDetailComponent]
})
export class AppComponent {
  title = 'Tour of Heroes';
  heroes = HEROES;
  selectedHero: Hero;
  onSelect(hero: Hero) { this.selectedHero = hero; }
}
```
**hero-detail.component.ts**
```
import {Component} from 'angular2/core';
import {Hero} from './hero';
@Component({
  selector: 'my-hero-detail',
  template: `
    <div *ngIf="hero">
      <h2>{{hero.name}} details!</h2>
      <div><label>id: </label>{{hero.id}}</div>
      <div>
        <label>name: </label>
        <input [(ngModel)]="hero.name" placeholder="name"/>
      </div>
    </div>
  `,
  inputs: ['hero']
})
export class HeroDetailComponent {
  hero: Hero;
}
```
单个类里的数据传递由数据绑定来完成，单向数据绑定如**{**{hero.name}**}**,双向数据绑定如`[(ngModel)]="hero.name"`。
`HeroDetailComponent`类的实例是`AppComponent`类的子组件，换句话说，`AppComponent`类由`HeroDetailComponent`类元素和其它元素组合而成。我们可以看到在`AppComponent`的tempalte中，有一个`<my-hero-detail>`标签定义的元素，这就是`HeroDetailComponent`类的元素，标签名与`HeroDetailComponent`类的选择器相同，运行时，选择器找到标签的位置，并将template格式的内容替换进去。由于angular无法识别未声明的标签，所以我们要在`AppComponent`类中进行声明，代码为`directives: [HeroDetailComponent]`。这个元素需要父元素给它传递数据，在父元素中使用标签定义子元素时，数据`selectedHero`被绑定给`hero`作为子元素的数据传入子元素，在子元素中，为了接收传入的数据，声明`inputs: ['hero']`，这样就可以使用从父元素传入的数据了。
## 事件
上面的例子中，list中元素监听click事件，某个元素触发事件则将其设为`selectedHero`，回调函数的实现在`export class AppComponent`中，这里包含视图所拥有的数据和可以调用的方法。一旦selectedHero变量有了数据，单向绑定的hero也就有了数据，`HeroDetailComponent`就有了数据的传入，`*ngIf`判断hero是否有数据，有数据则将下面模版的视图显示出来。
# 总结和比较
上面分别从视图、数据、逻辑的角度对两种JS框架的具体使用方法做了介绍，下面对这两中框架进行比较。
* 视图方面，react通过将自定义的标签转换成JS代码来完成实例的创建，并且将一个个的单个组件通过这个创建过程组合起来，最终形成一个组件整体；angular2则是使用选择器找到自定义的标签位置，将标签替换为相应的视图模版，通过层层的替换最终形成完整的视图。
* 数据方面，react更多的是通过参数传递和单向绑定的方式将数据填充到视图中，angular2则是通过单向或双向绑定来完成数据的填充。
* react的运行过程可以看作是状态驱动的，有明确的state表现出不同的状态，angular2根据事件改变视图，事件触发改变数据信息，类似于react的状态，只是没有明确的表述出来。
* react的事件监听是基于JS的，在原有JS的事件的基础上进行事件监听，angular2则是根据angularJS的事件进行监听。
* angular2的框架中包含丰富的库，可以方便的加载并使用，react则相反，你需要自己集成很多开源的库来实现你额外的要求，例如路由。
* angular2是以HTML为中心的，实在HTML中插入JS，react则是以JS为中心，是在JS中插入HTML，两种框架react更便于学习，react只需要学习掌握JS就能很好的掌握，而angular2则需要记忆很多HTML的标签和angular标签。
* angular2框架的结构很完备，正式发布后应该不会有大的变动，react相对简洁，因而有较多辅助的开源代码库，这些代码库出现变动的可能性较大。


