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