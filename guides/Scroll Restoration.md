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
