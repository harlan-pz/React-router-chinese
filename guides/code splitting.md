## 代码拆分
Web应用一个非常出色的特性就是我们不必下载整个应用就可以使用。你可以认为代码拆分是逐渐递增的加载我们的应用。在这里，我们使用webpack, @babel/plugin-syntax-dynamic-import,和loadable-components来实现。  

webpack内置了对动态导入的支持; 但是，如果你使用Babel（例如，将JSX编译为JavaScript），那么你将需要同时使用@ babel / plugin-syntax-dynamic-import插件。 这是一个仅只进行语法解析的插件，这意味着Babel不会进行任何其他转换。 该插件只允许Babel解析动态导入，因此webpack可以将它们打包成不同的代码模块。 你的.babelrc应该是这样的：
```
{
  "presets": ["@babel/preset-react"],
  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```
loadable-components是一个用于加载具有动态导入功能的组件的库。 她会自动处理各种边缘情况，使代码拆分变得简单，以下是如何使用loadable-components的示例：
```
import loadable from '@loadable/component'
import Loading from "./Loading";

const LoadableComponent = loadable(() => import('./Dashboard'), {
  fallback: Loading,
})

export default class LoadableDashboard extends React.Component {
  render() {
    return <LoadableComponent />;
  }
}
```
只需使用LoadableDashboard（或任何你自定义的组件），当你在应用程序中使用它时，它将自动加载和渲染。在实际组件加载时，fallback是一种占位的组件。[此处](https://www.smooth-code.com/open-source/loadable-components/docs/getting-started/)提供了完整的文档  

### 代码拆分和Server-Side渲染
loadable-components包含了server-side渲染的引导。