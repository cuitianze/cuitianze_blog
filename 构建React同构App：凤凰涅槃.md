### 构建React同构App：凤凰涅槃
##### 原文[The Pain and the Joy of creating isomorphic apps in ReactJS]
##### 翻译：cuitianze (https://github.com/cuitianze)
前方高能：这篇文章不是一篇教程。网上那些教程一抓一大把，而且保证你看完之后还是雾里看花。   
我们来分析一个真实的产品级App是一件十分有趣的事情，很少有公司愿意这么做。很多教程或模板教你如何去写ReactJS同构应用，但他们不会涉及我们在开发真正的产品时所遇到的问题。所以我们决定在github上分享我们的一些经验，开源项目代码。
这是运营中的[产品](http://itsquiz.com/en/activations)和[代码](https://github.com/webbylab/itsquiz-wall)。    
#### IMPORTANT:
这是一个真实的应用，所以我们在不断更新。我们的目标是在有限的时间内开发出能够上线的产品，所以我们没有时间去探索最佳策略，我们只用适合我们场景的。   
#### 前言
名词之争略... 不必细谈 "Isomorphic JavaScript" && "Universal JavaScript"，简言之，一套代码穿梭于浏览器和服务器之巅。*当之无愧“JavaScript是宇宙间最好的编程语言”。*   
人们怎么看待同构App？你可以在网上找到这张图。
![](http://blog.koorchik.com/isomorphic-react/isomorphic_separated.png)
但事实情况应该是这样的：
![](http://blog.koorchik.com/isomorphic-react/isomorphic_joined.png)
你的代码中至少有90%应该是被复用的，在大型应用中甚至能超过95%。
如果你想从头开发一个同构App，那可以阅读这篇教程“[Handcrafting an Isomorphic Redux Application (With Love)](https://medium.com/@bananaoomarang/handcrafting-an-isomorphic-redux-application-with-love-40ada4468af4)”，相当不错。   
#### 关于App   
产品广告略... 常规SPA（单页应用）和同构SPA的最本质区别是能同时处理多个请求。因此你需要以某种方式去解决用户依赖的全局state(比如flux或其变体处理存储state)    
简述产品的几个核心需求：   
1、内容开放用户，无需验证；   
2、能够被搜索引擎索引；    
3、具备社交分享功能；    
4、支持不同语言；    
5、一个字：快      
此案例中，会介绍同构应用最简单和最合适的解决方案。
#### 应用中的哪些块应该被同构？
1、视图    
2、样式    
3、路由
4、数据获取    
5、配置    
6、本地化    
让我们开始展开吧。
#### 同构视图
这是最简单的部分。说它简单，是因为Facebook的工程师已经在ReactJS库中解决了这一问题。我们唯一要做的事情就是引入ReactJS库，根据文档去使用它。     
客户端代码：
```
import ReactDOM from 'react-dom';
import App      from './App';

ReactDOM.render(
    <App />,
    document.getElementById('react-view')
);
```
服务端代码：
```
import ReactDOM from 'react-dom/server';
import App      from './App';

const componentHTML = ReactDOM.renderToString(
    <App />
);

const html = `
    <html>
        <head>
            <title>Quiz Wall</title>
        </head>
        <body>
            <div id="react-view">${componentHTML}</div>
        </body>
    </html>
`;

res.end(html)
```
正如你所见，我们只需用`ReactDOM.renderToString`替换`ReactDOM.render`即可。没什么复杂的，你可以在任何教程中看到。    
#### 同构样式
大部分教程都会忽略这部分，这是你在开发过程中会遇到的第一个棘手问题。      
* 痛点1 样式如何导入  
我们使用webpack，通常我们在组件中导入组件指定样式。举个例子，如果我们有一个组件名叫“Footer.jsx”，那么在同级目录中会有一个诸如“Footer.less”的样式文件，并且“Footer.jsx”引入“Footer.less”。组件是一个以“Footer”为类名的类，所有的样式会以这个类作为命名空间。      
这是一个小例子：
```
import React from 'react';
import './Footer.less';

export default class Footer extends React.Component {
    render() {
        return (
            <div className='Footer'>
                <small>
                    Developed by
                    <a href='http://webbylab.com' target='_blank'>
                        WebbyLab
                    </a>
                </small>
            </div>
        );
    }
}
```
这样的方法使我们的代码更加模块化。此外，如果我们组件，它会自动引入它的依赖（js库，样式，及其他资源）。Webpack负责处理所有的文件类型。    
~未完待续~ 
