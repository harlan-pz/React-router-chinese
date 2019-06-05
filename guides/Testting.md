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
< MemoryRouter >支持initialEntries和initialIndex 属性。因此你可以从一个特定的路径来启动你的应用（或者是从应用的一小部分)。
```
test("current user is active in sidebar", () => {
  render(
    <MemoryRouter initialEntries={["/users/2"]}>
      <Sidebar />
    </MemoryRouter>
  );
  expectUserToBeActive(2);
});
```  
### 导航
当路径改变时我们有许多路由测试的任务，因此你可能不需要再测试这个。但是如果你必须这样做的话，既然这发生在渲染时，那么我们可以这样做：
```
import { render, unmountComponentAtNode } from "react-dom";
import React from "react";
import { Route, Link, MemoryRouter } from "react-router-dom";
import { Simulate } from "react-addons-test-utils";

// a way to render any part of your app inside a MemoryRouter
// you pass it a list of steps to execute when the location
// changes, it will call back to you with stuff like
// `match` and `location`, and `history` so you can control
// the flow and make assertions.
const renderTestSequence = ({
  initialEntries,
  initialIndex,
  subject: Subject,
  steps
}) => {
  const div = document.createElement("div");

  class Assert extends React.Component {
    componentDidMount() {
      this.assert();
    }

    componentDidUpdate() {
      this.assert();
    }

    assert() {
      const nextStep = steps.shift();
      if (nextStep) {
        nextStep({ ...this.props, div });
      } else {
        unmountComponentAtNode(div);
      }
    }

    render() {
      return this.props.children;
    }
  }

  class Test extends React.Component {
    render() {
      return (
        <MemoryRouter
          initialIndex={initialIndex}
          initialEntries={initialEntries}
        >
          <Route
            render={props => (
              <Assert {...props}>
                <Subject />
              </Assert>
            )}
          />
        </MemoryRouter>
      );
    }
  }

  render(<Test />, div);
};

// our Subject, the App, but you can test any sub
// section of your app too
const App = () => (
  <div>
    <Route
      exact
      path="/"
      render={() => (
        <div>
          <h1>Welcome</h1>
        </div>
      )}
    />
    <Route
      path="/dashboard"
      render={() => (
        <div>
          <h1>Dashboard</h1>
          <Link to="/" id="click-me">
            Home
          </Link>
        </div>
      )}
    />
  </div>
);

// the actual test!
it("navigates around", done => {
  renderTestSequence({
    // tell it the subject you're testing
    subject: App,

    // and the steps to execute each time the location changes
    steps: [
      // initial render
      ({ history, div }) => {
        // assert the screen says what we think it should
        console.assert(div.innerHTML.match(/Welcome/));

        // now we can imperatively navigate as the test
        history.push("/dashboard");
      },

      // second render from new location
      ({ div }) => {
        console.assert(div.innerHTML.match(/Dashboard/));

        // or we can simulate clicks on Links instead of
        // using history.push
        Simulate.click(div.querySelector("#click-me"), {
          button: 0
        });
      },

      // final render
      ({ location }) => {
        console.assert(location.pathname === "/");
        // you'll want something like `done()` so your test
        // fails if you never make it here.
        done();
      }
    ]
  });
});
```
