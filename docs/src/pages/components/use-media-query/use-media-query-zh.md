---
title: React中的媒体查询用于响应式设计
---

# useMediaQuery

<p class="description">这是React的CSS媒体查询钩子。 它侦听与CSS媒体查询的匹配。 它允许根据查询是否匹配来呈现组件。</p>

一些重要特点：

- ⚛️它有一个惯用的React API。
- 🚀它具有高性能，它会观察文档以检测其媒体查询何时发生更改，而不是定期轮询值。
- 📦 [1 kB gzipped](/size-snapshot).
- 💄它是反应敏感和反应媒体的替代方案，旨在简化。
- 🤖它支持服务器端渲染。

## 简单的媒体查询

您应该为挂钩的第一个参数提供媒体查询。 媒体查询字符串可以由任何有效的CSS媒体查询，如 `'print'`。

{{"demo": "pages/components/use-media-query/SimpleMediaQuery.js", "defaultCodeOpen": true}}

## 使用Material-UI的断点助手

You can use Material-UI's [breakpoint helpers](/customization/breakpoints/) as follows:

```jsx
import { useTheme } from '@material-ui/core/styles';
import useMediaQuery from '@material-ui/core/useMediaQuery';

function MyComponent() {
  const theme = useTheme();
  const matches = useMediaQuery(theme.breakpoints.up('sm'));

  return <span>{`theme.breakpoints.up('sm') matches: ${matches}`}</span>;
}
```

{{"demo": "pages/components/use-media-query/ThemeHelper.js"}}

## 使用JavaScript语法

[json2mq](https://github.com/akiran/json2mq) 用于将JavaScript对象转换生成媒体查询字符串。

{{"demo": "pages/components/use-media-query/JavaScriptMedia.js", "defaultCodeOpen": true}}

## 服务器端呈现

服务器上需要实现 [matchMedia](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia) ，我们建议使用 [css-mediaquery](https://github.com/ericf/css-mediaquery)。 我们还鼓励使用从主题中获取属性的 `useMediaQueryTheme` 版本的钩子。 这样，您可以提供一次 `ssrMatchMedia` 选项，所有的React树都适用。

{{"demo": "pages/components/use-media-query/ServerSide.js"}}

## 迁徙自 `withWidth()`

`withWidth()` 高阶组件注入页面的屏幕宽度。 您可以对 `useWidth` 钩子重用相同的操作：

```jsx
/**
 * Be careful using this hook. It only works because the number of
 * breakpoints in theme is static. It will break once you change the number of
 * breakpoints. See https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level
 */
function useWidth() {
  const theme = useTheme();
  const keys = [...theme.breakpoints.keys].reverse();
  return (
    keys.reduce((output, key) => {
      // eslint-disable-next-line react-hooks/rules-of-hooks
      const matches = useMediaQuery(theme.breakpoints.only(key));
      return !output && matches ? key : output;
    }, null) || 'xs'
  );
}
```

{{"demo": "pages/components/use-media-query/UseWidth.js"}}

## API

### `useMediaQuery(query, [options]) => matches`

#### 参数

1. `query` （*String*）：表示要处理的媒体查询的字符串。
2. `选项` (*Object* [optional]): 
    - `options.defaultMatches` （*布尔值* [optional]）： 作为 `window.matchMedia()` 在服务器上不可用， 我们在第一次安装时返回默认匹配。 默认值为 `false`。
    - `options.noSsr` (*Boolean* [可选的]): 默认值为`false`。 为了执行服务器端呈现协调，它需要呈现两次。 第一次没有任何东西，第二次与孩子们在一起。 这种双遍渲染周期有一个缺点。 它慢了。 您可以将此标志设置为 `真` ，如果你是 **没有做服务器端渲染**。
    - `options.ssrMatchMedia` （*功能* [optional]）您可能希望使用启发式来近似 客户端浏览器的屏幕。 例如，您可以使用用户代理或客户端提示https://caniuse.com/#search=client%20hint。 You can provide a global ponyfill using [`custom properties`](/customization/globals/#default-props) on the theme. 检查 [服务器端呈现示例](#server-side-rendering)。

#### 返回结果

` matches `：如果文档当前能够匹配这个媒体查询，Matches 是 `true` ，否则为 `false` 。

#### 例子

```jsx
import React from 'react';
import useMediaQuery from '@material-ui/core/useMediaQuery';

export default function SimpleMediaQuery() {
  const matches = useMediaQuery('print');

  return <span>{`@media (min-width:600px) matches: ${matches}`}</span>;
}
```