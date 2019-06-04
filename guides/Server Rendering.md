## 服务端渲染
由于服务端是无状态的，所以服务端渲染和客户端渲染并不相同. 最基本的就是我们封装app时, 使用无状态的 < StaticRouter >来代替< BrowserRouter >, 使用来自于服务端的请求url来匹配路由。接下来我们会讨论 context 属性 
```
// client
<BrowserRouter>
  <App/>
</BrowserRouter>

// server (not the complete story)
<StaticRouter
  location={req.url}
  context={context}
>
  <App/>
</StaticRouter>
```
当你在客户端渲染 < Redirect >, 浏览器地址栏会改变状态使我们能看到新的页面,然而在一个静态的服务环境下, 我们不能够改变app的状态。代替的是, 我们将渲染的结果赋给context属性. 如果我们找到了 context.url, 那么我们知道这个app重定向了. 这允许我们向服务端发送一个重定向请求 
```
const context = {}
const markup = ReactDOMServer.renderToString(
  <StaticRouter
    location={req.url}
    context={context}
  >
    <App/>
  </StaticRouter>
)

if (context.url) {
  // Somewhere a `<Redirect>` was rendered
  redirect(301, context.url)
} else {
  // we're good, send the response
}
```
### 添加明确的应用内容信息
路由只能添加 context.url. 但是你可能想要发送重定向的301或302的响应。或许你在某些特殊的UI渲染后需要发送一个404响应, 又或者在客户端没有认证的情况下发送401。 context属性是属于你的, 所以你可以任意改变她. 下面是分辨301与302重定向的方法。
```
const RedirectWithStatus = ({ from, to, status }) => (
  <Route render={({ staticContext }) => {
    // there is no `staticContext` on the client, so
    // we need to guard against that here
    if (staticContext)
      staticContext.status = status
    return <Redirect from={from} to={to}/>
  }}/>
)

// somewhere in your app
const App = () => (
  <Switch>
    {/* some other routes */}
    <RedirectWithStatus
      status={301}
      from="/users"
      to="/profiles"
    />
    <RedirectWithStatus
      status={302}
      from="/courses"
      to="/dashboard"
    />
  </Switch>
)

// on the server
const context = {}

const markup = ReactDOMServer.renderToString(
  <StaticRouter context={context}>
    <App/>
  </StaticRouter>
)

if (context.url) {
  // can use the `context.status` that
  // we added in RedirectWithStatus
  redirect(context.status, context.url)
}
```
### 404, 401, 或其他状态
我们现在可以做到和上面一样的事，创建一个包含想要内容的组件，当收到不同的的状态码时可以在应用的任何地方渲染该组件。  
```
function Status({ code, children }) {
  return (
    <Route
      render={({ staticContext }) => {
        if (staticContext) staticContext.status = code;
        return children;
      }}
    />
  );
}
```
现在，当你想要给静态内容添加一个状态码时，你可以在应用的任何地方渲染一种状态。
```
function NotFound() {
  return (
    <Status code={404}>
      <div>
        <h1>Sorry, can’t find that.</h1>
      </div>
    </Status>
  );
}

// somewhere else
<Switch>
  <Route path="/about" component={About} />
  <Route path="/dashboard" component={Dashboard} />
  <Route component={NotFound} />
</Switch>;
```
### 组合所有内容
虽然这不是一个真正的应用，但是她展现了将所有内容组合在一起所需的常规部分
```
import { createServer } from 'http'
import React from 'react'
import ReactDOMServer from 'react-dom/server'
import { StaticRouter } from 'react-router'
import App from './App'

createServer((req, res) => {
  const context = {}

  const html = ReactDOMServer.renderToString(
    <StaticRouter
      location={req.url}
      context={context}
    >
      <App/>
    </StaticRouter>
  )

  if (context.url) {
    res.writeHead(301, {
      Location: context.url
    })
    res.end()
  } else {
    res.write(`
      <!doctype html>
      <div id="app">${html}</div>
    `)
    res.end()
  }
}).listen(3000)
```
然后是客户端
```
import ReactDOM from 'react-dom'
import { BrowserRouter } from 'react-router-dom'
import App from './App'

ReactDOM.render((
  <BrowserRouter>
    <App/>
  </BrowserRouter>
), document.getElementById('app'))
```
### 数据加载
要做到这一点有很多不同的方法，对此并没有最佳的实践。所以我们寻求多种方法的不同组合方式，而不是规定或倾向某一种。我们相信React Router可以在你的应用的规则限制下找到一种合理的方式。  

最主要的约束是你希望在页面渲染前加载完数据。React Router暴露了一个matchPath静态函数，你可以用她来进行路由匹配。你可以在服务端用这个函数来确定哪些依赖的数据是要在渲染前完成的。   
  
这种方法的特点是在进行实际跳转前设定好静态匹配规则，在实际跳转前就已经知道要使用哪些数据。  
```
const routes = [
  { path: '/',
    component: Root,
    loadData: () => getSomeData(),
  },
  // etc.
]
```
然后使用这些规则在应用中渲染你的路由
```
import { routes } from './routes'

const App = () => (
  <Switch>
    {routes.map(route => (
      <Route {...route}/>
    ))}
  </Switch>
)
```
在服务端你可能会做这些；
```
import { matchPath } from 'react-router-dom'

// inside a request
const promises = []
// use `some` to imitate `<Switch>` behavior of selecting only
// the first to match
routes.some(route => {
  // use `matchPath` here
  const match = matchPath(req.url, route)
  if (match)
    promises.push(route.loadData(match))
  return match
})

Promise.all(promises).then(data => {
  // do something w/ the data so the client
  // can access it then render the app
})
```
最后，客户端需要获取数据。我们并不是给你的应用规定数据加载的模式，但这些是在开发中常用的形式。  

你可能会对我们的进行数据加载和静态路由配置的[React Router Config](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-config)包感兴趣。