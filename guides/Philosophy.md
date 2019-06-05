## 设计思想
这篇指南是是用来解释React Router使用时的心智模型。我们将其称为“动态路由”，这与你可能更熟悉的“静态路由”完全不同。  
### 静态路由
如果你使用过Rails，Express，Ember，Angular等框架，那么你已经使用过了静态路由。 在这些框架中，当你在进行任何渲染之前，会将路由声明为应用初始化的一部分。React Router pre-v4在大多数情况下也是静态的。下面我们来看看如何在express中配置路由：
```
// Express Style routing:
app.get("/", handleIndex);
app.get("/invoices", handleInvoices);
app.get("/invoices/:id", handleInvoice);
app.get("/invoices/:id/edit", handleInvoiceEdit);

app.listen();
```
请注意在应用监听请求之前是如何声明路由的。我们使用的客户端路由与其是相似当。在Angular中，你可以预先声明路由，然后在渲染之前将它们导入顶级AppModule中：
```
// Angular Style routing:
const appRoutes: Routes = [
  {
    path: "crisis-center",
    component: CrisisListComponent
  },
  {
    path: "hero/:id",
    component: HeroDetailComponent
  },
  {
    path: "heroes",
    component: HeroListComponent,
    data: { title: "Heroes List" }
  },
  {
    path: "",
    redirectTo: "/heroes",
    pathMatch: "full"
  },
  {
    path: "**",
    component: PageNotFoundComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(appRoutes)]
})
export class AppModule {}
```
Ember有一个常规的routes.js文件，在构建时会读取这个文件当内容并导入到应用中。这会在应用渲染前进行。
```
// Ember Style Router:
Router.map(function() {
  this.route("about");
  this.route("contact");
  this.route("rentals", function() {
    this.route("show", { path: "/:rental_id" });
  });
});

export default Router;
```
尽管API不同，但她们都遵循“静态路由”模型。React Router遵循这个模型直到v4。但是要成功使用React Router，你需要忘记这一切。
### 背景
坦率地说，我们React Router直到v2版本所采取的开发方向感到非常沮丧。 我们（Michael和Ryan）感觉现在采用的这种路由方式受限于API，我们正在重新实现React（生命周期等）的部分，并且它与React给我们创造UI的心智模型不匹配。当我们在研讨会之前在一家酒店的走廊互相讨论时。我们互相问道：“如果我们使用我们在之前讨论的模式构建路由会是什么样子？”开发只需要几个小时，我们就有了一个验证的概念，我们知道她是未来我们想要路由。 我们最终意识到API并不应该在React的“外部”，这是一个由React的其余部分组成并自然落实到位的API。我们认为你会喜欢她。
### 动态路由
动态路由指的是在应用渲染时发生的路由，而不是在正在运行的应用之外配置或规定好的路由。这意味着几乎所有东西都是React Router中的一个组件。接下来看一下这个API是如何工作的：  
  
首先，创建一个路由组件，将其渲染在应用的顶部。
```
// react-native
import { NativeRouter } from "react-router-native";

// react-dom (what we'll use here)
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  el
);
```
接下来，创建一个链接组件将其链接到一个新的地址。
```
const App = () => (
  <div>
    <nav>
      <Link to="/dashboard">Dashboard</Link>
    </nav>
  </div>
);
```
最后，当用户查看‘/dashboard'路径时，渲染该路由并展示对应UI
```
const App = () => (
  <div>
    <nav>
      <Link to="/dashboard">Dashboard</Link>
    </nav>
    <div>
      <Route path="/dashboard" component={Dashboard} />
    </div>
  </div>
);
```
Route将渲染<Dashboard {... props} />，props是一些看起来像{match，location，history}这样的特定的路由的属性。如果用户不在‘/dashboard'路径下，那么Route将不渲染任何东西。这就是它的全部内容。
### 嵌套路由
很多路由库都有一些“嵌套路由”的概念。如果你使用过React Router V4以前的版本，那么你就会知道我们也是这样做的。然而当你从静态路由配置移动到动态渲染路由时，该如何“嵌套路由”呢？
```
const App = () => (
  <BrowserRouter>
    {/* here's a div */}
    <div>
      {/* here's a Route */}
      <Route path="/tacos" component={Tacos} />
    </div>
  </BrowserRouter>
);

// when the url matches `/tacos` this component renders
const Tacos = ({ match }) => (
  // here's a nested div
  <div>
    {/* here's a nested Route,
        match.url helps us make a relative path */}
    <Route path={match.url + "/carnitas"} component={Carnitas} />
  </div>
);
```
如上代码，React Router并没有嵌套的API，Router仅仅是一个组件，就想一个普通的div一样。
### 响应路由
设想一下用户导航到'/invoice'。你的应用需要适用于不同的尺寸的屏幕，当她们具有较窄的视口时，你只需向其显示发票列表和到发票详情的链接。她们可以导航到更深入的层级。
```
Small Screen
url: /invoices

+----------------------+
|                      |
|      Dashboard       |
|                      |
+----------------------+
|                      |
|      Invoice 01      |
|                      |
+----------------------+
|                      |
|      Invoice 02      |
|                      |
+----------------------+
|                      |
|      Invoice 03      |
|                      |
+----------------------+
|                      |
|      Invoice 04      |
|                      |
+----------------------+
```
在更大的屏幕上，我们会想要显示一个类别-详细内容的视图，其中导航位于左侧，详情或特定发票显示在右侧。
```
Large Screen
url: /invoices/dashboard

+----------------------+---------------------------+
|                      |                           |
|      Dashboard       |                           |
|                      |   Unpaid:             5   |
+----------------------+                           |
|                      |   Balance:   $53,543.00   |
|      Invoice 01      |                           |
|                      |   Past Due:           2   |
+----------------------+                           |
|                      |                           |
|      Invoice 02      |                           |
|                      |   +-------------------+   |
+----------------------+   |                   |   |
|                      |   |  +    +     +     |   |
|      Invoice 03      |   |  | +  |     |     |   |
|                      |   |  | |  |  +  |  +  |   |
+----------------------+   |  | |  |  |  |  |  |   |
|                      |   +--+-+--+--+--+--+--+   |
|      Invoice 04      |                           |
|                      |                           |
+----------------------+---------------------------+
```
现在暂停考虑一下两种屏幕尺寸的/invoice URL,她是大尺寸屏幕的有效路由吗？我们应该把右边的东西放在什么位置？
```
Large Screen
url: /invoices
+----------------------+---------------------------+
|                      |                           |
|      Dashboard       |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 01      |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 02      |             ???           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 03      |                           |
|                      |                           |
+----------------------+                           |
|                      |                           |
|      Invoice 04      |                           |
|                      |                           |
+----------------------+---------------------------+
```
在大屏幕上，'/invoice'不是有效的路线，但在小屏幕上她是有效的，更有趣的是，请考虑一个拥尺寸手机的人。她/他可以用横屏和竖屏两种方式来查看手机页面。突然间，我们有足够的空间来显示类别-详情这种UI，所以你应该立即重定向！  

React Router在先前版本的静态路由并没有真正成功的答案。但是，当路由是动态的时，你可以声明性地重组这些功能。如果你开始考虑将路由作为UI而不是静态配置，那么你会写出以下代码：
```
const App = () => (
  <AppLayout>
    <Route path="/invoices" component={Invoices} />
  </AppLayout>
);

const Invoices = () => (
  <Layout>
    {/* always show the nav */}
    <InvoicesNav />

    <Media query={PRETTY_SMALL}>
      {screenIsSmall =>
        screenIsSmall ? (
          // small screen has no redirect
          <Switch>
            <Route exact path="/invoices/dashboard" component={Dashboard} />
            <Route path="/invoices/:id" component={Invoice} />
          </Switch>
        ) : (
          // large screen does!
          <Switch>
            <Route exact path="/invoices/dashboard" component={Dashboard} />
            <Route path="/invoices/:id" component={Invoice} />
            <Redirect from="/invoices" to="/invoices/dashboard" />
          </Switch>
        )
      }
    </Media>
  </Layout>
);
```
当用户将手机从纵向旋转到横向时，此代码会自动将其重定向到详情页。该组有效路由将根据用户手中的移动设备的动态特性而改变。  

这只是一个例子。我们还有许多其他问题可以讨论，我们总结一下规律：我们应该考虑组件，而不是静态路由。考虑如何使用React的声明可组合性来解决问题，因为几乎每个“React Router问题”都可能是“React问题”。