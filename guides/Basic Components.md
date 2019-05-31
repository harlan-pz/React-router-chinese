## 基础组件
React Router包含三种类型的组件： 路由组件，路由匹配组件，导航组件。  

在你使用这些组件前，都必须从react-router-dom导入她们。
```
import { BrowserRouter, Route, Link } from "react-router-dom";
```
### 路由组件
任何一个拥有路由跳转功能都React应用都核心都必须是一个路由组件。对于Web项目来说，react-router-dom提供了< BrowserRouter >和< HashRouter >这两种路由。她们会为你创建一个专业的history对象。通常来说，如果你有一个服务器来响应请求，那就使用< BrowserRouter >。如果你是用静态文件来提供服务，那么就使用 < HashRouter >
```
import { BrowserRouter } from "react-router-dom";
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  holder
);
```
### 路由匹配组件
路由匹配组件有两种：< Route >和< Switch >  
路由匹配组件通过比较< Router >的path属性和当前页面地址栏的路径来工作。当一个< Router >匹配成功的时候，她会渲染出对应的内容，当匹配不成功的时候，任何内容都不会被渲染出来。当一个< Router >没有path属性时她对任何路径都会匹配成功。  
你可以在你想要根据浏览器地址来渲染内容的任何地方使用< Router >,但是我们通常会把一组< Router >放在一起。< Switch >就是用来把多个< Router >组合在一起的。  
我们不是必须要用< Switch >把多个< Router >组合在一起，但是这种做法通常是有用的。 < Switch >将迭代其所有子< Route >元素，并仅渲染与当前路径匹配的第一个子元素。 她对于多个path匹配相同的路径、动画路由之间的转换、没有路径匹配时的识别（这样你就可以渲染“404”组件）是有很大帮助的。  
```
import { Route, Switch } from "react-router-dom";
// when location = { pathname: '/about' }
<Route path='/about' component={About}/> // renders <About/>
<Route path='/contact' component={Contact}/> // renders null
<Route component={Always}/> // renders <Always/>

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Switch>

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
  {/* when none of the above match, <NoMatch> will be rendered */}
  <Route component={NoMatch} />
</Switch>
```
### 路由渲染属性
对于一个< Router >组件，你可以设置三种属性：component, render, children 来渲染出相应的内容。在这里我们只关注component和render,因为她们是经常会用到的，更多的内容可以查看< Router >的API文档。  
  
当你有一个已存在的组件（无论是一个React组件还是一个无状态的函数组件）想要渲染时应该使用component。当你必须传递一些参数变量给组件时应该用render属性，她采用内联函数的形式。你不应该使用component属性来渲染一个带有参数变量的内联函数组件，这会导致不必要的组件的挂载和卸载。
```
const Home = () => <div>Home</div>;

const App = () => {
  const someVariable = true;

  return (
    <Switch>
      {/* these are good */}
      <Route exact path="/" component={Home} />
      <Route
        path="/about"
        render={props => <About {...props} extra={someVariable} />}
      />
      {/* do not do this */}
      <Route
        path="/contact"
        component={props => <Contact {...props} extra={someVariable} />}
      />
    </Switch>
  );
};
```
### 导航组件
React Router 提供了< Link >组件用来在你的应用中创建超链接。< Link >会在页面的任何地方被渲染成< a >标签  

< NavLink >是一种特殊的< Link >组件，当她的to属性匹配地址栏的路径时，她渲染成的< a >标签会带有'active'的样式。  

如果你想要强制跳转，你可以使用< Redirect >。当一个< Redirect >组件被渲染时，她会导航到其to属性匹配的路径。
```
<Link to="/">Home</Link>
// <a href='/'>Home</a>

// location = { pathname: '/react' }
<NavLink to="/react" activeClassName="hurray">
  React
</NavLink>
// <a href='/react' className='hurray'>React</a>

<Redirect to="/login" />
```