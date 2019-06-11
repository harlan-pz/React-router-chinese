这部分API文档翻译参考了该项目[react-router-CN](https://github.com/react-translate-team/react-router-CN)
## < BrowserRouter >
<Router> 使用HTML5提供的history API(pushState, replaceState和popstate事件)来同步UI和URL。
```
import { BrowserRouter } from 'react-router-dom'

<BrowserRouter
  basename={optionalString}
  forceRefresh={optionalBool}
  getUserConfirmation={optionalFunc}
  keyLength={optionalNumber}
>
  <App/>
</BrowserRouter>

```
### basename: String
当前位置的基准URL。如果你的页面部署在服务器的二级子目录，你需要将basename设置到此子目录。 正确的URL格式是前面有一个前导斜杠，但不能有尾部斜杠。
```
<BrowserRouter basename="/calendar"/>
<Link to="/today"/> // 渲染为 <a href="/calendar/today">
```
### getUserConfirmation: func
当导航需要确认时执行的函数。默认使用window.confirm。
```
// 使用默认的确认函数
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message)
  callback(allowTransition)
}

<BrowserRouter getUserConfirmation={getConfirmation}/>
```
### forceRefresh: bool
当设置为true时，在导航的过程中整个页面将会刷新。只有当浏览器不支持HTML5的 history API 时，才设置为true。
```
const supportsHistory = 'pushState' in window.history
<BrowserRouter forceRefresh={!supportsHistory}/>
```
### keyLength: number
location.key的长度。默认是6。
```
<BrowserRouter keyLength={12}/>
```
### children: node
渲染单一子组件（元素）。

## < HashRouter >
HashRouter 是一种特定的 <Router>， HashRouter 使用 URL 的 hash (例如：window.location.hash) 来同步UI和URL。  

注意：使用 hash 的方式记录导航历史不支持 location.key 和 location.state。在以前的版本中，我们为这种行为提供了 shim，但是仍有一些问题我们无法解决。任何依赖此行为的代码或插件都将无法正常使用。 由于该技术仅用于支持传统的浏览器，因此在用于浏览器时可以使用 <BrowserHistory> 代替。
```
import { HashRouter } from 'react-router-dom'

<HashRouter>
  <App/>
</HashRouter>
```
### basename: string
当前位置的基准 URL。正确的 URL 格式是前面有一个前导斜杠，但不能有尾部斜杠。
```
<HashRouter basename="/calendar"/>
<Link to="/today"/> // renders <a href="#/calendar/today">
```
### getUserConfirmation: func
当导航需要确认时执行的函数。默认使用 window.confirm。
```
// 使用默认的确认函数
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message)
  callback(allowTransition)
}

<HashRouter getUserConfirmation={getConfirmation}/>
```
### hashType: string
window.location.hash 使用的 hash 类型。有如下几种：
* "slash" - 后面跟一个斜杠，例如 #/ 和 #/sunshine/lollipops
* "noslash" - 后面没有斜杠，例如 # 和 #sunshine/lollipops
* "hashbang" - Google 风格的 “ajax crawlable”，例如 #!/ 和 #!/sunshine/lollipops  

默认为 "slash"。
### children: node
渲染单一子组件（元素）。
## < Link >
为您的应用提供声明式的、无障碍导航。
```
import { Link } from 'react-router-dom'

<Link to="/about">关于</Link>
```
### to: string
需要跳转到的路径(pathname)或地址（location）。
```
<Link to="/courses"/>
```
### to: object
需要跳转到的地址（location）。
```
<Link to={{
  pathname: '/courses',
  search: '?sort=name',
  hash: '#the-hash',
  state: { fromDashboard: true }
}}/>
```
### replace: bool
当设置为 true 时，点击链接后将使用新地址替换掉访问历史记录里面的原地址。  
当设置为 false 时，点击链接后将在原有访问历史记录的基础上添加一个新的纪录。  
默认为 false。
```
<Link to="/courses" replace />
```
## < NavLink >
< NavLink >是 < Link > 的一个特定版本, 会在匹配上当前 URL 的时候会给已经渲染的元素添加样式参数
```
import { NavLink } from 'react-router-dom'

<NavLink to="/about">About</NavLink>
```
### activeClassName: string
当元素匹配上当前 URL 的时候, 这个类会被赋予给这个元素. 其默认值为 active, 这个值会被添加到 className 属性的后面(追加)
```
<NavLink
  to="/faq"
  activeClassName="selected"
>FAQs</NavLink>
```
### activeStyle: object
当元素被选中时, 为此元素添加样式
```
<NavLink
  to="/faq"
  activeStyle={{
    fontWeight: 'bold',
    color: 'red'
   }}
>FAQs</NavLink>
```
### exact: bool
当值为 true 时, 只有当地址完全匹配 class 和 style 才会应用
```
<NavLink
  exact
  to="/profile"
>Profile</NavLink
```
### strict: bool
当值为 true 时，在确定位置是否与当前 URL 匹配时，将考虑位置 pathname 后的斜线 有关详细信息，请参阅[< Route strict >](https://reacttraining.com/react-router/core/api/Route/strict-bool)文档。
```
<NavLink
  strict
  to="/events/"
>Events</NavLink>
```
### isActive: func
添加用于确定链接是否活动的额外逻辑的功能。 如果您想要做的更多，请验证链接的路径名是否与当前URL的 pathname 匹配。
```
// only consider an event active if its event id is an odd number
const oddEvent = (match, location) => {
  if (!match) {
    return false
  }
  const eventID = parseInt(match.params.eventID)
  return !isNaN(eventID) && eventID % 2 === 1
}

<NavLink
  to="/events/123"
  isActive={oddEvent}
>Event 123</NavLink>
```
## < Prompt >
当用户离开当前页的时候做出提示. 当你的应用处在特定状态, 此状态不希望用户离开时(例如填写表格到一半), 你应该使用<Prompt>。
```
import { Prompt } from 'react-router'

<Prompt
  when={formIsHalfFilledOut}
  message="Are you sure you want to leave?"
/>

```
### message: string
当用户尝试导航离开时，提示用户的消息。
```
<Prompt message="Are you sure you want to leave?" />

```
### message: func
会与用户试图前往下一个地址（location） 和 action 一起被调用。  
函返回一个字符串用作向用户提示，或者返回true用作允许过渡。
```
Prompt
  message={location =>
    location.pathname.startsWith("/app")
      ? true
      : `Are you sure you want to go to ${location.pathname}?`
  }
/>
```
### when: bool
你可以随时渲染<Prompt>，而不是有条件地在警戒后面渲染它。
* 当when={true} 时，禁止导航
* 当when={false} 时，允许导航
```
<Prompt when={formIsHalfFilledOut} message="Are you sure?" />
```
### < MemoryRouter >
< Router > 能在内存保存你 “URL” 的历史纪录(并没有对地址栏读写). 在非浏览器或者测试环境比如React Native下很有用。
```
import { MemoryRouter } from 'react-router'

<MemoryRouter>
  <App/>
</MemoryRouter>
```
### initialEntries: array
在历史栈中的一个 location 数组. 这些可能会成为含有 { pathname, search, hash, state } 或一些简单的 URL 字符串的完整的地址对象
```
<MemoryRouter
  initialEntries={[ '/one', '/two', { pathname: '/three' } ]}
  initialIndex={1}
>
  <App/>
</MemoryRouter>
```
### initialIndex: number
initialEntries 数组中的初始化地址索引
### getUserConfirmation: func
用于确认导航的函数. 当使用<MemoryRouter>直接使用<Prompt>时，你必须使用这个选项
### keyLength: number
location.key 的长度, 默认为 6
```
<MemoryRouter keyLength={12}/>
```
### children: node
要呈现的 单个子元素。
## < Redirect >
渲染<Redirect> 的时候将会导航到一个新的地址（location）。这个新的地址（location）将会覆盖在访问历史记录里面的原地址，就像服务端的重定向（HTTP 3XX）一样
```
import { Route, Redirect } from 'react-router'

<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard"/>
  ) : (
    <PublicHomePage/>
  )
)}/>
```
### to: string
重定向目标URL。
```
<Redirect to="/somewhere/else"/>
```
### to: object
重定向目标地址(location)。
```
<Redirect to={{
  pathname: '/login',
  search: '?utm=your+face',
  state: { referrer: currentLocation }
}}/>
```
### push: bool
当设置为 true 时，重定向（redirecting）将会把新地址加入访问历史记录里面，而不是替换掉目前的地址
```
<Redirect push to="/somewhere/else"/>
```
### from: string
需要被重定向的路径（pathname）。当渲染一个包含在<Switch>里面的<Redirect>的时候，这可以用作匹配一个地址（location）。
```
<Switch>
  <Redirect from='/old-path' to='/new-path'/>
  <Route path='/new-path' component={Place}/>
</Switch>
```
## < Route >
想要理解并使用好React Router，最重要的可能就是Route组件了。Route组件主要的作用就是当一个location匹配路由的path时，渲染某些UI。  
考虑这样的代码：
```
import { BrowserRouter as Router, Route } from 'react-router-dom'

<Router>
  <div>
    <Route exact path="/" component={Home}/>
    <Route path="/news" component={NewsFeed}/>
  </div>
</Router>
```
如果应用的地址是/,那么相应的UI会类似这个样子：
```
<div>
  <Home/>
  <!-- react-empty: 2 -->
</div>
```
如果应用的地址是/news,那么相应的UI就会成为这个样子：
```
<div>
  <!-- react-empty: 1 -->
  <NewsFeed/>
</div>
```
这里的react-empty注释只是演示了React渲染null的细节，但对我们具有启发性。其实Route就算是null也会被渲染，只要地址与路由的路径匹配，组件就会渲染。


