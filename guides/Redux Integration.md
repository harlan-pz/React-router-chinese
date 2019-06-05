## Redux集成
Redux是React生态系统的重要组成部分。我们希望将React Router和Redux尽可能地无缝集成以满足人们想要同时使用她们的需求。
### 阻塞更新
通常，React Router和Redux可以很好地协同工作。但有时，应用可能会发生有一个组件在位置更改时不会更新（子路由或点击导航链接不会更新）的情况。  

如果这种情况发生在以下场景：  
1. 该组件通过connect()(comp)连接到redux。
2. 该组件不是“路由组件”，这意味着它不会像这样渲染：< Route component = {SomeConnectedThing} />

问题通常是Redux实现了本来应该由组件自动更新的部分，如果她没有从路由接收属性，则没有任何迹象表明组件应该发生改变。这很容易解决，找到连接组件的位置并将其封装在withRouter方法中。
```
// before
export default connect(mapStateToProps)(Something)

// after
import { withRouter } from 'react-router-dom'
export default withRouter(connect(mapStateToProps)(Something))

```
### 深度集成
一些人想要做到：
1. 从store中访问并同步数据。
2. 能够通过调用actions进行导航跳转。
3. 支持在Redux devtools中调试路由改变。  

所有这些需要深度集成。  

我们的建议是更不不要将路由放在Redux store中。  

原因：  
1. 路由数据已经成为你的大部分组件所在意的属性。无论她是来自store还是router，你的组件代码都大致相同。
2. 在大多数情况下，你可以使用Link，NavLink和Redirect来执行导航操作。有时你可能还需要在初始化操作启动某个异步任务之后以编程方式导航。
例如，你可以在用户提交登录表单时调度action。你的thunk，saga或其他异步操作需要验证身份，如果成功，她需要某种方式导航的新页面。解决方案是在action的payload中包含历史对象（提供给所有路由组件），并且在适当的时候你的异步操作可以使用这来进行导航跳转。
3. 路由改变对于时间旅行式的调试不太重要。唯一需要关注的情况是调试route/store同步的问题，但如果你根本不同步这个问题就会消失。  

但是如果你强烈想要将你的route与store同步，你可能想尝试 Connected React Router，一个绑定React Router v4和Redux的第三方类库。