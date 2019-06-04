## 滚动恢复
在早期的React Router版本中，我们为滚动恢复提供了开箱即用的支持，从那时起开发者就一直在使用。希望本文档可以帮助你获取你需要的滚动条和路由的内容。  

浏览器已经开始使用history.pushState自己处理滚动恢复，这就像她们以前使用普通浏览器导航一样处理滚动恢复。现在这个很酷的特性已经在Chrome中被支持了。  

因为浏览器开始处理“默认情况”，并且不同的应用程序具有不同的滚动需求（就像本站这样），所以我们不提供默认的滚动管理。但是本指南应该可以帮助你实现任何滚动需求。  
### 滚动到顶部
大多数情况下，你需要的是“滚动到顶部”这个功能，因为大部分情况是有一个包含了大量内容的长页面。使用<ScrollToTop>组件可以直接处理这中情况，该组件将会实现在每个导航上滚动窗口到顶部，你需要确保使用withRouter封装该组件以使其能够访问路由器的props。
```
class ScrollToTop extends Component {
  componentDidUpdate(prevProps) {
    if (this.props.location.pathname !== prevProps.location.pathname) {
      window.scrollTo(0, 0);
    }
  }

  render() {
    return this.props.children;
  }
}

export default withRouter(ScrollToTop);
```
然后将其渲染在应用的顶部
```
function App() {
  return (
    <Router>
      <ScrollToTop>
        <App />
      </ScrollToTop>
    </Router>
  );
}

// or just render it bare anywhere you want, but just one :)
<ScrollToTop />;
```
如果你有一个连接到路由的tab选项卡，那么你可能不希望在切换标签时滚动到顶部。此时可以使用<ScrollToTopOnMount>。
```
class ScrollToTopOnMount extends Component {
  componentDidMount() {
    window.scrollTo(0, 0);
  }

  render() {
    return null;
  }
}

class LongContent extends Component {
  render() {
    <div>
      <ScrollToTopOnMount />
      <h1>Here is my long content page</h1>
    </div>;
  }
}

// somewhere else
<Route path="/long-content" component={LongContent} />;
```
### 通用解决方案 
对于通用解决方案（以及浏览器开始本地实现）我们谈论两件事：
1. 导航跳转时自动滚动到页面顶部，这样不会出现创建一个新的页面滚动条在底部。
2. 在前进或后退时保留原来滚动条的位置（不包含点击跳转链接）    

对于第一点我们想要发布一个通用的api，这是我们想要的：
```
<Router>
  <ScrollRestoration>
    <div>
      <h1>App</h1>

      <RestoredScroll id="bunny">
        <div style={{ height: "200px", overflow: "auto" }}>I will overflow</div>
      </RestoredScroll>
    </div>
  </ScrollRestoration>
</Router>
```
首先，ScrollRestoration将在导航时向上滚动窗口。 其次，它将使用location.key将窗口滚动位置和RestoredScroll组件的滚动位置保存到sessionStorage。 然后，当ScrollRestoration或RestoredScroll组件挂载时，他们可以从sessionsStorage中查找它们的位置。  

对我来说棘手的是当我不希望管理窗口滚动时,如何实现一个“选择退出”API，。 例如，当你有一些浮动在页面上的选项卡导航时，你可能不希望滚动到顶部（选项卡可能会滚动到视图之外）。  

当我了解到chrome现在已经在为我们管理滚动位置，并且意识到不同的应用将有不同的滚动需求时，我有点失去了做这件事的信念 - 特别是当人们只想滚动到顶部时（ 你看到的是很容易直接添加到你的应用的内容）。  

基于此，我们觉得我们已经没有足够的力量来完成这项工作（就像你的时间是有限的一样）。但是，我们很乐意帮助任何愿意使用通用解决方案的人。如果你一开始就使用她，你的项目会有一个可靠的解决方案。
