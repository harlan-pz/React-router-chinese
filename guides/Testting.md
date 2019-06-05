## 测试
React Router依赖React上下文来工作。 这会影响您如何测试使用我们组件的组件。  

如果你对应用渲染的<Link>或者<Router>组件进行单元测试，那么你会得到一些有关上下文的错误和警告信息。虽然你可能会找出自己的路由上下文的内容，我们推荐你将你的单元测试包裹在<StaticRouter>或者<MemoryRouter>中。代码如下:
```
class Sidebar extends Component {
  // ...
  render() {
    return (
      <div>
        <button onClick={this.toggleExpand}>
          expand
        </button>
        <ul>
          {users.map(user => (
            <li>
               <Link to={user.path}>
                 {user.name}
               </Link>
            </li>
          ))}
        </ul>
      </div>
    )
  }
}

// broken
test('it expands when the button is clicked', () => {
  render(
    <Sidebar/>
  )
  click(theButton)
  expect(theThingToBeOpen)
})

// fixed!
test('it expands when the button is clicked', () => {
  render(
    <MemoryRouter>
      <Sidebar/>
    </MemoryRouter>
  )
  click(theButton)
  expect(theThingToBeOpen)
})
```
### 从特定路由开始
< MemoryRouter >支持initialEntries和initialIndex 属性。因此你可以从一个特定的路径来启动你的应用（或者是从应用的一小部分）。