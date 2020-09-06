# 玩具 React

手动搭建一个玩具 React，实现了虚拟 DOM、组件和生命周期、diff 算法以及异步 setState 功能。

## 虚拟 DOM

使用 React 时，是使用 JSX 语法的。其实 JSX 不是 JavaScript 标准中的语法，需要使用 babel 将 JSX 编译成标准的 JavaScript 语法。

为了表示这个过程，可以使用[在线版 babel](https://babeljs.io/repl)进行研究：

```javascript
const title = <h1 className="title">Hello, world!</h1>;

// babel 后
const title = React.createElement(
  "h1",
  { className: "title" },
  "Hello, world!"
);
```

可以看到，为了实现 React，需要实现一个类似 `React.createElement` 方法。本项目中采用 webpack，利用 babel-loader 来进行配置：

```javascript
module: {
    rules: [
        {
        test: /\.js$/,
        use: {
            loader: "babel-loader",
            options: {
            presets: ["@babel/preset-env"],
            plugins: [
                [
                "@babel/plugin-transform-react-jsx",
                {
                    pragma: "React.createElement",
                },
                ],
            ],
            },
        },
        },
    ],
},
```

在 babel-loader 中引入的 @babel/plugin-transform-react-jsx 插件就是能将 JSX 转化为标准 JavaScript 的工具，其中的 pragma 项决定了调用的方法名称。

### React.createElement 方法

从转译的结果看，createElement 方法为：

```javascript
createElement(tag, attrs, ...children);
```

- tag 是 DOM 节点的标签名
- attrs 是一个对象，包含了所有属性
- children 是所有子节点

createElement 方法要做的事情也很简单，只要它能接收参数并返回一个对象，这个对象也就是虚拟 DOM（vNode）：

```javascript
function createElement(tag, attrs, ...children) {
  return {
    tag,
    attrs,
    children,
  };
}
```

### ReactDOM.render 方法

在上一步构建了 vnode，现在还需要将 vnode 挂载到真实 DOM 上，也就是创建 ReactDOM.render 方法。

先定义怎么调用：

```javascript
const element = (
  <div
    id="outer"
    style="width: 200px; height: 200px; background-color: yellow;"
  >
    <span id="inner">Hello world!</span>
    Forza juventus
    <span>GO!</span>
  </div>
);

console.log(element);

ReactDOM.render(element, document.getElementById("root"));
```

可以注意到此时 vNode 是：

```json
{
  "tag": "div",
  "attrs": {
    "id": "outer",
    "style": "width: 200px; height: 200px; background-color: yellow;"
  },
  "children": [
    {
      "tag": "span",
      "attrs": {
        "id": "inner"
      },
      "children": ["Hello world!"]
    },
    "Forza juventus", // 字符串
    {
      "tag": "span",
      "attrs": null, // 没有 attrs 时为 null
      "children": ["GO!"]
    }
  ]
}
```

render 实现：

```javascript
function render(vnode, container) {
  // 当解析到 `Forza juventus` 这段文本时，vnode 是一个字符串，需要特殊处理。
  if (typeof vnode === "string") {
    const dom = document.createTextNode(vnode);
    return container.appendChild(dom);
  }

  const dom = document.createElement(vnode.tag);

  if (vnode.attrs) {
    Object.keys(vnode.attrs).forEach((key) => {
      const value = vnode.attrs[key];
      setAttribute(dom, key, value);
    });
  }

  vnode.children.forEach((child) => {
    render(child, dom);
  });

  return container.appendChild(dom);
}

// 需要注意的是，处理 attributes 需要针对几种不同的情况处理。
function setAttribute(dom, key, value) {
  if (key === "className") {
    key = "class";
  }

  if (/on\w+/.test(key)) {
    key = key.toLowerCase();
    dom[key] = value || "";
  } else if (key === "style") {
    dom.style.cssText = value || "";
  } else {
    if (key in dom) {
      dom[key] = value || "";
    }

    if (value) {
      dom.setAttribute(key, value);
    } else {
      dom.removeAttribute(key);
    }
  }
}
```

从 render 方法我们也可以看出，在构建真实 DOM 树时，是按照先序、深度优先的遍历方式，先构建子节点的真实 DOM 的。

### 支持自定义组件

细心的你应该已经发现，上面的代码只支持了 HTML 原生的元素。但是，如果要跟 React 一样自定义组件，要如何实现呢？

比如，我们现在写了一个 Welcome 组件（先忽略组件的设计），然后在 `main.js` 中调用：

```javascript
class Welcome {
  render() {
    return <h1>hello frank</h1>;
  }
}

const element = <Welcome />;
```

当这个 JSX 被解析后，生成的 vNode 是：

```javascript
{
  attrs: null,
  children: [],
  tag: ƒ Welcome()
}
```

此时， `vNodeToDom.js` 是不支持的。需要添加额外的一种分支情况：

```javascript
// vNodeToDom.js
{
  // ...
  if (typeof vNode.tag === "function") {
    return componentVNodeToDom(vNode);
  }
  // ...
}
```

`componentVNodeToDom.js` 顾名思义，就是将 component 形式的 vNode 转化为 DOM：

```javascript
// componentVNodeToDom.js
function componentVNodeToDom(vNode) {
  const component = vNodeToComponent(vNode);
  const dom = componentToDom(component);

  return dom;
}
```

其中， `vNodeToComponent.js` 是将 vNode 转化为 component 对象， `componentToDom.js` 是将 component 对象转化为 DOM：

```javascript
// vNodeToComponent.js
function vNodeToComponent(vNode) {
  return new vNode.tag();
}

// componentToDom.js
function componentToDom(component) {
  const vNode = component.render();
  return vNodeToDom(vNode);
}
```

这样，自定义组件也就能渲染到页面上了。

## 增加 props 和 state
