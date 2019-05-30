## 快速开始
你需要一个React web app 来添加react-router。  

如果你需要创建一个新的React Web App,最容易的方式是通过一个叫做[create-react-app](https://github.com/facebook/create-react-app)的官方脚手架来创建。    

第一步是先来安装[create-react-app](https://github.com/facebook/create-react-app),如果你以及安装过这个工具，那么可以直接使用她来创建一个新的项目。  
  
```
npm install -g create-react-app
create-react-app demo-app
cd demo-app
```
### 安装
React Router Dom 已经被发布到来npm上, 所以你可以用npm或yarn来安装她。  
```
npm install react-router-dom
```
### 示例：基础路由
在这个例子中，我们会使用<Router>来构建一个拥有3个页面的示例。
注意: 我们用 < Link to="/"> 来代替 < a href="/">

把下方的代码复制到demo-app里面的src目录下的App.js文件中
```
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

function Index() {
  return <h2>Home</h2>;
}

function About() {
  return <h2>About</h2>;
}

function Users() {
  return <h2>Users</h2>;
}

function AppRouter() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/about/">About</Link>
            </li>
            <li>
              <Link to="/users/">Users</Link>
            </li>
          </ul>
        </nav>

        <Route path="/" exact component={Index} />
        <Route path="/about/" component={About} />
        <Route path="/users/" component={Users} />
      </div>
    </Router>
  );
}

export default AppRouter;
```
### 示例：嵌套路由
这个示例向我们展示来嵌套路由如何工作，路由 '/topics'会加载Topics组件，这个组件会通过':id'的路由来渲染出更多的内容。  

把下方的代码复制到demo-app里面的src目录下的App.js文件中
```
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

function App() {
  return (
    <Router>
      <div>
        <Header />

        <Route exact path="/" component={Home} />
        <Route path="/about" component={About} />
        <Route path="/topics" component={Topics} />
      </div>
    </Router>
  );
}

function Home() {
  return <h2>Home</h2>;
}

function About() {
  return <h2>About</h2>;
}

function Topic({ match }) {
  return <h3>Requested Param: {match.params.id}</h3>;
}

function Topics({ match }) {
  return (
    <div>
      <h2>Topics</h2>

      <ul>
        <li>
          <Link to={`${match.url}/components`}>Components</Link>
        </li>
        <li>
          <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
        </li>
      </ul>

      <Route path={`${match.path}/:id`} component={Topic} />
      <Route
        exact
        path={match.path}
        render={() => <h3>Please select a topic.</h3>}
      />
    </div>
  );
}

function Header() {
  return (
    <ul>
      <li>
        <Link to="/">Home</Link>
      </li>
      <li>
        <Link to="/about">About</Link>
      </li>
      <li>
        <Link to="/topics">Topics</Link>
      </li>
    </ul>
  );
}

export default App;
```