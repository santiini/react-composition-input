# react-composition-input
针对中文、日语等需要输入法组词的语言进行优化的输入框。

[![npm version](https://badge.fury.io/js/react-composition-input.svg)](https://badge.fury.io/js/react-composition-input)

[Demo](https://leoeatle.github.io/react-composition-input/)

# 用法

`npm install --save react-composition-input`

```javascript
import { CInput } from 'react-composition-input';

class App extends Component {
  render() {
    return (
        <CInput onInputChange={this.handleInputChange} />
    );
  }
}
```

# 例子 & 开发

```bash
npm install
npm start
```

# Why
当我们用中文打字的时候，我们会需要输入法帮我们组词。但是输入框并不会等我们组词完毕之后才触发`change`事件，这样会导致每次输入一个字母输入框的`change`就会触发一次。

但还好我们的[DOM3规范](https://w3c.github.io/uievents/#event-type-compositionstart)有`compositionstart`和`compositionend`事件，我们可以通过一个变量标记输入时是否是组词状态。只要使用`react-composition-input`，`onInputChange`回调函数只会在`compositionend`事件触发后被调用。

你可以看下面两个gif图，左边是原生的`input`，在输入`ceshi`时搜索的函数已经被调用，右侧是使用`react-composition-input`，只有等到`测试`组词完毕后搜索才被调用。

![original_input](./assets/original_input.gif)![optimized_input](./assets/optimized_input.gif)


你还可以看下[尤雨溪的文章](http://blog.evanyou.me/2014/01/03/composition-event/)了解更多细节.

#更新
我发现不仅是有些浏览器触发`composition`和`input`事件的顺序不一样，用不同的输入法也能造成不同的结果。比如在iOS上如果你用原生的键盘去输入，`input`事件是在`composition`之前触发的，而如果你用Google键盘的话，你会发现`composition`事件从来都不会触发，`input`事件只会在组词结束后触发（虽然这样挺好但不符合规范）。 

所以我用了另一种策略来对付这种不兼容问题，在`1.0.4`版本以上iOS的这种情况将被兼容。

# Q & A
### 为什么我传`value`作为props不起作用？
react-composition-input 用内部的state管理value，所以**请不要传`value`作为props到CInput**，如果这么做的话会显示一个警告。

### 为什么要检测是否是Chrome并在`compositionend`事件触发后再次调用`onInputChange`?
Chrome v53之后，`compositionend`事件被改成了在`textInput`事件之后再触发，这造成`compositionend`事件触发了，但是`onInputChange`没有被调用，所以我们针对Chrome需要再一次调用`onInputChange`，在其他的大部分浏览器，`textInput`都是在`compositionend`之后触发的。

你可以查阅[chromium的源码](https://chromium.googlesource.com/chromium/src/+/afce9d93e76f2ff81baaa088a4ea25f67d1a76b3%5E!/)了解更多细节。

### 为什么用[Parcel](https://parceljs.org/)来打包？
因为用[Parcel](https://parceljs.org/)来开发组件的demo页真是太爽了，但这并不影响你用什么打包器去开发你的项目，因为库代码都是由[Babel](https://babeljs.io/)生成的。