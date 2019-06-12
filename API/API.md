这部分API文档翻译参考了项目[react-router-CN](https://github.com/react-translate-team/react-router-CN)
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
### Route渲染方法
这三种渲染方法都会获得相同的三个的属性：
* match
* location
* history

### component
只有在地址匹配的时候React的组件才会被渲染，route props也会随着一起被渲染。
```
<Route path="/user/:username" component={User}/>

const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```
如果你使用component(而不是像下面这样使用render),路由会根据指定的组件使用React.createElement来创建一个新的React element。这就意味着如果你提供的是一个内联的函数的话会带来很多意料之外的重新挂载。所以，对于内联渲染，要使用render属性(如下所示)。
### render: func
这种方式对于内联渲染和包装组件却不引起意料之外的重新挂载特别方便。  
使用render属性，你可以选择传一个在地址匹配时被调用的函数，而不是像使用component属性那样得到一个新创建的React element。使用render属性会获得跟使用component属性一样的route props。
```
// 便捷的行内渲染
<Route path="/home" render={() => <div>Home</div>}/>

// 包装/合成
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props}/>
    </FadeIn>
  )}/>
)

<FadingRoute path="/cool" component={Something}/>
```
警告: < Route component >的优先级要比< Route render >高，所以不要在同一个 <Route>中同时使用这两个属性。
### children: func
有时候你可能想不管地址是否匹配都渲染一些内容，这种情况你可以使用children属性。它与render属性的工作方式基本一样，除了它是不管地址匹配与否都会被调用。  
除了在路径不匹配URL时match的值为null之外，children渲染属性会获得与component和render一样的route props。这就允许你根据是否匹配路由来动态地调整UI了，来看这个例子，如果理由匹配的话就添加一个active类：
```
<ul>
  <ListItemLink to="/somewhere"/>
  <ListItemLink to="/somewhere-else"/>
</ul>

const ListItemLink = ({ to, ...rest }) => (
  <Route path={to} children={({ match }) => (
    <li className={match ? 'active' : ''}>
      <Link to={to} {...rest}/>
    </li>
  )}/>
)
```
这种属性对于动画也特别有用:
```
<Route children={({ match, ...rest }) => (
  {/* Animate总会被渲染, 所以你可以使用生命周期来使它的子组件出现
    或者隐藏
  */}
  <Animate>
    {match && <Something {...rest}/>}
  </Animate>
)}/>
```
警告: < Route component >和< Route render > 的优先级都比< Route children > 高，所以在同一个<Route>中不要同时使用一个以上的属性.
### path: string
可以是任何path-to-regexp能理解的有效URL。
```
<Route path="/users/:id" component={User}/>
```
没有path属性的Route 总是会 匹配。
### exact: bool
当值为true时，则要求路径与location.pathname必须 完全 匹配。
```
<Route exact path="/one" component={About}/>
```
路径  |	location.pathname|	exact|	是否匹配?
:-:  |  :-:     | :-:  | :-:|
/one | /one/two | true | 否|
/one | /one/two | false| 是|
### strict: bool
当设为true的时候，有结尾斜线的路径只能匹配有斜线的location.pathname，这个值并不会对location.pathname中有其他的片段有影响。
```
<Route strict path="/one/" component={About}/>
```
路径|	location.pathname|	是否匹配?
:-:  |  :-:     | :-:  |
/one/|	/one	|否|
/one/|	/one/	|是|
/one/|	/one/two	|是|
警告: stict可以强制location.pathname不包含结尾的斜线，但是要做到这点必须把strict和exect都设置为true。
```
<Route exact strict path="/one" component={About}/>
```
路径	|location.pathname|	是否匹配?|
:-:  |  :-:     | :-:  |
/one|	/one|	是|
/one|	/one/	|否|
/one|	/one/two|	否|

### location: object
< Route >元素尝试将路径path当前history location（通常是当前浏览器URL）进行匹配。 除此之外，具有不同pathname的location也可以被传统用来匹配。  

当你需要将< Route >匹配到当前history location以外的location时，这非常有用，如动画过渡示例中所示。  

如果< Route >包含在< Switch >中并匹配了传递给<Switch>的location（或当前history location），那么传递给< Route >的location属性将会被< Switch >使用的所覆盖。

### sensitive: bool
如果该属性为true,则在匹配时区分大小写
path|	location.pathname|	sensitive|	是否匹配?|
:-:|:-:|:-:|:-:|
/one|/one|	true|	yes|
/One|	/one|	true|	no|
/One|	/one| false| yes|

## < Router >
Router是所有路由组件共用的底层接口，一般我们的应用并不会使用这个接口，而是使用高级的路由：
* < BrowserRouter >
* < HashRouter >
* < MemoryRouter >
* < NativeRouter >
* < StaticRouter >

最常见的使用底层的< Router >的情形就是用来与Redux或者Mobx之类的状态管理库的定制的history保持同步。注意不是说使用状态管理库就必须使用< Router >，它仅用作于深度集成。
```
import { Router } from 'react-router'
import createBrowserHistory from 'history/createBrowserHistory'

const history = createBrowserHistory()

<Router history={history}>
  <App/>
</Router>
```
### history: object
用来导航的history对象.
```
import createBrowserHistory from 'history/createBrowserHistory'

const customHistory = createBrowserHistory()
<Router history={customHistory}/>
```
### children: node
需要渲染的单一组件。
```
<Router>
  <App/>
</Router>
```
## < StaticRouter >
< Router > 从不会改变地址  
当用户实际上没有点击时, 这在服务端的渲染场景中可能会非常有用, 所以这个地址从来没有改变. 因此, 称为: static (静态). 当您只需要插入一个位置并在渲染输出上作出断言时，它也可用于简单的测试 这里有一个简单 nodejs 服务 : 为< Redirect >和其他请求的常规HTML发送302状态代码：
```
import { createServer } from 'http'
import React from 'react'
import ReactDOMServer from 'react-dom/server'
import { StaticRouter } from 'react-router'

createServer((req, res) => {

  // This context object contains the results of the render
  const context = {}

  const html = ReactDOMServer.renderToString(
    <StaticRouter location={req.url} context={context}>
      <App/>
    </StaticRouter>
  )

  // context.url will contain the URL to redirect to if a <Redirect> was used
  if (context.url) {
    res.writeHead(302, {
      Location: context.url
    })
    res.end()
  } else {
    res.write(html)
    res.end()
  }
}).listen(3000)
```
### basename: string
所有地址的基本 URL . 正确格式化的基本名称应该有一个主要的斜杠，但没有尾部斜杠
```
<StaticRouter basename="/calendar">
  <Link to="/today"/> // renders <a href="/calendar/today">
</StaticRouter>
```
### location: string
服务器收到的 URL, 在 node 服务上可能是 req.url
```
<StaticRouter location={req.url}>
  <App/>
</StaticRouter>
```
### location: object
一个格式像 { pathname, search, hash, state } 的地址对象
```
<StaticRouter location={{ pathname: '/bubblegum' }}>
  <App/>
</StaticRouter>
```
### context: object
记录渲染结果的纯JavaScript对象。 见上面的例子
### children: node
要呈现的单个子元素。
## < Switch >
渲染匹配地址(location)的第一个 < Route > 或者 < Redirect >  
这与只使用一堆< Route >有什么不同？  
< Switch >的独特之处是独它仅仅渲染一个路由。相反地，每一个包含匹配地址(location)的< Route >都会被渲染。思考下面的代码：
```
<Route path="/about" component={About}/>
<Route path="/:user" component={User}/>
<Route component={NoMatch}/>
```
如果现在的URL是 /about ，那么 < About >, < User >, 还有 < NoMatch > 都会被渲染，因为它们都与路径(path)匹配。这种设计，允许我们以多种方式将多个 < Route > 组合到我们的应用程序中，例如侧栏(sidebars)，面包屑(breadcrumbs)，bootstrap tabs等等。 然而，偶尔我们只想选择一个< Route > 来渲染。如果我们现在处于 /about ，我们也不希望匹配 /:user （或者显示我们的 “404” 页面 ）。以下是使用 Switch 的方法来实现：
```
<Switch>
  <Route exact path="/" component={Home}/>
  <Route path="/about" component={About}/>
  <Route path="/:user" component={User}/>
  <Route component={NoMatch}/>
</Switch>
```
现在，如果我们处于 /about, <Switch> 将开始寻找匹配的 <Route>。 <Route path="/about"/> 将被匹配， <Switch> 将停止寻找匹配并渲染<About>。 同样，如果我们处于 /michael ， <User> 将被渲染。  

这对于过渡动画也是起作用的，因为匹配的 <Route> 在与前一个相同的位置被渲染。
```
<Fade>
  <Switch>
    {/* there will only ever be one child here */}
    {/* 这里只会有一个子节点 */}
    <Route/>
    <Route/>
  </Switch>
</Fade>

<Fade>
  <Route/>
  <Route/>
  {/* there will always be two children here,
      one might render null though, making transitions
      a bit more cumbersome to work out */}
   {/* 这里总是有两个子节点,
      一个可能会渲染为null, 使计算过渡增加了一点麻烦 */}    
</Fade>
```
### children: node
< Switch > 的所有子节点应为 < Route > 或 < Redirect > 元素。只有匹配当前地址(location)的第一个子节点才会被渲染。< Route > 元素使用它们的 path 属性匹配，< Redirect > 元素使用它们的 from 属性匹配。没有 path 属性的< Route > 或者 没有 from 属性的 < Redirect > 将总是可以匹配当前的地址(location)
```
<Switch>
  <Route exact path="/" component={Home}/>

  <Route path="/users" component={Users}/>
  <Redirect from="/accounts" to="/users"/>

  <Route component={NoMatch}/>
</Switch>
```
## history
本文档中的「history」以及「history对象」请参照 history 包中的内容。 History 是 React Router 的两大重要依赖之一（除去 React 本身），在不同的 Javascript 环境中，history 以多种形式实现了对于 session 历史的管理。  
我们会经常使用以下术语：
* 「browser history」 - history 在 DOM 上的实现，经常使用于支持 HTML5 history API 的浏览器端。
* 「hash history」 - history 在 DOM 上的实现，经常使用于旧版本浏览器端。
* 「memory history」 - 一种存储于内存的 history 实现，经常用于测试或是非 DOM 环境（例如 React Native）。

 history 对象通常会具有以下属性和方法：
* length -（ number 类型）指的是 history 堆栈的数量。
* action -（ string 类型）指的是当前的动作（action），例如 PUSH，REPLACE 以及 POP 。
* location -（ object类型）是指当前的位置（location），location 会具有如下属性：
  * pathname -（ string 类型）URL路径。
  * search -（ string 类型）URL中的查询字符串（query string）。
  * hash -（ string 类型）URL的 hash 分段。
  * state -（ string 类型）是指 location 中的状态，例如在 push(path, state) 时，state会描述什么时候 location 被放置到堆栈中等信息。这个 state 只会出现在 browser history 和 memory history 的环境里。
* push(path, [state]) -（ function 类型）在 hisotry 堆栈顶加入一个新的条目。
* replace(path, [state]) -（ function 类型）替换在 history 堆栈中的当前条目。
* go(n) -（ function 类型）将 history 对战中的指针向前移动 n 。
* goBack() -（ function 类型）等同于 go(-1) 。
* goForward() -（ function 类型）等同于 go(1) 。
* block(prompt) -（ function 类型）阻止跳转，（请参照 history 文档）。
### history 是可变的（mutable）
history 对象是可变的，因此我们建议从 <Route> 的 prop里来获取 location ，而不是从 history.location 直接获取。这样做可以保证 React 在生命周期中的钩子函数正常执行，例如以下代码：
```
class Comp extends React.Component {
  componentWillReceiveProps(nextProps) {
    // locationChanged 变量为 true
    const locationChanged = nextProps.location !== this.props.location

    // 不正确，locationChanged 变量会 *永远* 为 false ，因为 history 是可变的（mutable）。
    const locationChanged = nextProps.history.location !== this.props.history.location
  }
}

<Route component={Comp}/>
```
不同的实现也许会提供给你额外的属性，更多详情请参照 [history 文档](https://github.com/ReactTraining/history#properties)。
## location
Location 是指你当前的位置，下一步打算去的位置，或是你之前所在的位置，形式大概就像这样：
```
{
  key: 'ac3df4', // 在使用 hashHistory 时，没有 key
  pathname: '/somewhere'
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
```
你使用以下几种方式来获取 location 对象：
* 在 Route component 中，以 this.props.location 的方式获取，
* 在 Route render 中，以 ({ location }) => () 的方式获取，
* 在 Route children 中，以 ({ location }) => () 的方式获取，
* 在 withRouter 中，以 this.props.location 的方式获取。

你也可以在 history.location 中获取 location 对象，但是别那么写，因为 history 是可变的。更多信息请参见 [history 文档](https://github.com/ReactTraining/history#properties)。  

location 对象不会发生改变，因此你可以在生命周期的钩子函数中使用 location 对象来查看当前页面的位置是否发生改变，这种技巧在获取远程数据以及使用动画时非常有用。
```
对象来查看当前页面的位置是否发生改变，这种技巧在获取远程数据以及使用动画时非常有用。componentWillReceiveProps(nextProps) {
  if (nextProps.location !== this.props.location) {
    // 已经跳转了！
  }
}
```
你可以在不同环境中使用 location ：
* Web Link to
* Native Link to
* Redirect to
* history.push
* history.replace
通常情况下，你只需要给一个字符串当做 location ，但是，当你需要添加一些 location 的状态时，你可以对象的形式使用 location 。并且当你需要多个 UI ，而这些 UI 取决于历史时，例如弹出框（modal），使用location 对象会有很大帮助。
```
/ 通常你只需要这样使用 location
<Link to="/somewhere"/>

// 但是你同样可以这么用
const location = {
  pathname: '/somewhere'
  state: { fromDashboard: true }
}

<Link to={location}/>
<Redirect to={location}/>
history.push(location)
history.replace(location)
```
最后，你可以把 location 传入一下组件：
* Route
* Switch
这样做可以让组件不使用路由状态（router state）中的真实 location，因为我们有时候需要组件去渲染一个其他的 location 而不是本身所处的真实 location，比如使用动画或是等待跳转时。
## match
match 对象包含了 <Route path> 如何与URL匹配的信息。match 对象包含以下属性：
* params -（ object 类型）即路径参数，通过解析URL中动态的部分获得的键值对。
* isExact - 当为 true 时，整个URL都需要匹配。
* path -（ string 类型）用来做匹配的路径格式。在需要嵌套 <Route> 的时候用到。
* url -（ string 类型）URL匹配的部分，在需要嵌套 <Link> 的时候会用到。
你可以在以下地方获取 match 对象：
* 在 Route component 中，以 this.props.match 方式。
* 在 Route render 中，以 ({ match }) => () 方式。
* 在 Route children 中，以 ({ match }) => () 方式
* 在 withRouter 中，以 this.props.match 方式
* matchPath 的返回值
当一个 Route 没有 path 时，它会匹配一切路径，你会匹配到最近的父级。在 withRouter 里也是一样的。

### null matches
即使路径的路径与当前位置不匹配，< Route >也可以使用子属性来调用其子功能。 但在这种情况下，匹配将为null。 能够在匹配时渲染< Route >的内容可能是有用的，但是这种情况会产生一些挑战。

“解析”URL的默认方法是将match.url字符串拼接到“相对”路径。
```
`${match.url}/relative-path`
```
如果在匹配为空时尝试执行此操作，最终会出现TypeError。 这意味着在使用子属性尝试连接< Route >内的“relative”路径是不安全的。

当你在生成空匹配对象的< Route >中使用没有path的< Route >时，会出现类似但更微妙的情况。
```
// location.pathname = '/matches'
<Route path='/does-not-match' children={({ match }) => (
  // match === null
  <Route render={({ match:pathlessMatch }) => (
    // pathlessMatch === ???
  )}/>
)}/>
```
没有path属性的< Route >从其父级继承匹配对象。 如果她们的父匹配为null，那么她们的匹配也将为null。 这意味着  
a）任何子路由/链接必须是绝对路径，因为没有要解析的父级  
b）父级匹配可以为null的无路径路由将需要使用子属性来渲染。
## matchPath
这允许您使用与一样使用的相同的代码，除了在正常渲染循环之外，例如在服务器上渲染之前收集数据依赖关系
```
import { matchPath } from 'react-router'

const match = matchPath('/users/123', {
  path: '/users/:id'
  exact: true,
  strict: false
})
```
### pathname
第一参数是你想要匹配的 pathname, 如果你正在服务端的 nodos.js 下使用, 将会是 req.url
### props
第二个参数是不予匹配的属性, 他们于匹配 Route 接收的参数属性是相同的
```
{
  path, // like /users/:id
  strict, // 可选, 默认 false
  exact // 可选, 默认 false
}
```
## withRouter
你可以通过withRouter的高阶组件访问history对象的属性和最相似的< Route >的匹配。 每次路由更改时，无论如何是如何渲染的，withRouter将会给封装的组件传递更新的match,location 和 history属性。
```
mport React from "react";
import PropTypes from "prop-types";
import { withRouter } from "react-router";

// A simple component that shows the pathname of the current location
class ShowTheLocation extends React.Component {
  static propTypes = {
    match: PropTypes.object.isRequired,
    location: PropTypes.object.isRequired,
    history: PropTypes.object.isRequired
  };

  render() {
    const { match, location, history } = this.props;

    return <div>You are now at {location.pathname}</div>;
  }
}

// Create a new component that is "connected" (to borrow redux
// terminology) to the router.
const ShowTheLocationWithRouter = withRouter(ShowTheLocation);
```  
### 重要提醒

withRouter不追踪location的更改，而是在从< Router >组件传播出去的location改变后重新渲染，这意味着withRouter不会在路由改变时重新渲染，除非她的父组件重新渲染。  

静态方法和属性。

封装的组件的所有非React的静态方法和属性都会被自动的复制到已连接的组件。
### Component.WrappedComponent
封装的组件作为返回组件上的静态属性WrappedComponent暴露，这个组件可以用于测试。
```
// MyComponent.js
export default withRouter(MyComponent)

// MyComponent.test.js
import MyComponent from './MyComponent'
render(<MyComponent.WrappedComponent location={{...}} ... />)

```
### wrappedComponentRef: func
作为引用的属性被传递给封装的组件的函数
```
class Container extends React.Component {
  componentDidMount() {
    this.component.doSomething();
  }

  render() {
    return <MyComponent wrappedComponentRef={c => (this.component = c)} />;
  }
}
```