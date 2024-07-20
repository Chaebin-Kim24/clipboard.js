# 복붙 자바스크립트 (clipboard.js)

![Build Status](https://github.com/zenorocha/clipboard.js/workflows/build/badge.svg)
![Killing Flash](https://img.shields.io/badge/killing-flash-brightgreen.svg?style=flat)

> 현대적인 클립보드에 복사하기. 플래시 없음. 압축된 3kb만 있음. 

<a href="https://clipboardjs.com/"><img width="728" src="https://cloud.githubusercontent.com/assets/398893/16165747/a0f6fc46-349a-11e6-8c9b-c5fd58d9099c.png" alt="Demo"></a>

## 이유

클립보드에 텍스트를 복사하는 것은 어렵지 않아야 한다. 여러 단계에 걸친 설정이나 수백 kB를 로드할 이유가 없다. 그리고 가장 중요한 것은, 플래시나 다른 비대한 프레임워크에 의존하지 않는 것이 좋다. 

이것이 복붙 자바스크립트 (clipboard.js)의 존재 이유이다.

## 설치

npm에서 다운받을 수 있다.

```
npm install clipboard --save
```

패키지 관리 프로그램에 흥미가 없으면, 그냥 [ZIP 파일 다운로드](https://github.com/zenorocha/clipboard.js/archive/master.zip)를 하면 된다.

## 사용할 준비

먼저, `dist` 폴더에 있는 스크립트를 include하거나, [클라우드](https://github.com/zenorocha/clipboard.js/wiki/CDN-Providers)에서 로드합니다.

```html
<script src="dist/clipboard.min.js"></script>
```

이제, 객체를 생성하기 위해서, you need to instantiate it by [passing a DOM selector](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-selector.html#L18), [HTML element](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-node.html#L16-L17), or [list of HTML elements](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-nodelist.html#L18-L19).

```js
new ClipboardJS('.btn');
```

Internally, we need to fetch all elements that matches with your selector and attach event listeners for each one. But guess what? If you have hundreds of matches, this operation can consume a lot of memory.

For this reason we use [event delegation](https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation) which replaces multiple event listeners with just a single listener. After all, [#perfmatters](https://twitter.com/hashtag/perfmatters).

# Usage

We're living a _declarative renaissance_, that's why we decided to take advantage of [HTML5 data attributes](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Using_data_attributes) for better usability.

### Copy text from another element

A pretty common use case is to copy content from another element. You can do that by adding a `data-clipboard-target` attribute in your trigger element.

The value you include on this attribute needs to match another's element selector.

<a href="https://clipboardjs.com/#example-target"><img width="473" alt="example-2" src="https://cloud.githubusercontent.com/assets/398893/9983467/a4946aaa-5fb1-11e5-9780-f09fcd7ca6c8.png"></a>

```html
<!-- Target -->
<input id="foo" value="https://github.com/zenorocha/clipboard.js.git" />

<!-- Trigger -->
<button class="btn" data-clipboard-target="#foo">
  <img src="assets/clippy.svg" alt="Copy to clipboard" />
</button>
```

### Cut text from another element

Additionally, you can define a `data-clipboard-action` attribute to specify if you want to either `copy` or `cut` content.

If you omit this attribute, `copy` will be used by default.

<a href="https://clipboardjs.com/#example-action"><img width="473" alt="example-3" src="https://cloud.githubusercontent.com/assets/398893/10000358/7df57b9c-6050-11e5-9cd1-fbc51d2fd0a7.png"></a>

```html
<!-- Target -->
<textarea id="bar">Mussum ipsum cacilds...</textarea>

<!-- Trigger -->
<button class="btn" data-clipboard-action="cut" data-clipboard-target="#bar">
  Cut to clipboard
</button>
```

As you may expect, the `cut` action only works on `<input>` or `<textarea>` elements.

### Copy text from attribute

Truth is, you don't even need another element to copy its content from. You can just include a `data-clipboard-text` attribute in your trigger element.

<a href="https://clipboardjs.com/#example-text"><img width="147" alt="example-1" src="https://cloud.githubusercontent.com/assets/398893/10000347/6e16cf8c-6050-11e5-9883-1c5681f9ec45.png"></a>

```html
<!-- Trigger -->
<button
  class="btn"
  data-clipboard-text="Just because you can doesn't mean you should — clipboard.js"
>
  Copy to clipboard
</button>
```

## Events

There are cases where you'd like to show some user feedback or capture what has been selected after a copy/cut operation.

That's why we fire custom events such as `success` and `error` for you to listen and implement your custom logic.

```js
var clipboard = new ClipboardJS('.btn');

clipboard.on('success', function (e) {
  console.info('Action:', e.action);
  console.info('Text:', e.text);
  console.info('Trigger:', e.trigger);

  e.clearSelection();
});

clipboard.on('error', function (e) {
  console.error('Action:', e.action);
  console.error('Trigger:', e.trigger);
});
```

For a live demonstration, go to this [site](https://clipboardjs.com/) and open your console.

## Tooltips

Each application has different design needs, that's why clipboard.js does not include any CSS or built-in tooltip solution.

The tooltips you see on the [demo site](https://clipboardjs.com/) were built using [GitHub's Primer](https://primer.style/css/components/tooltips). You may want to check that out if you're looking for a similar look and feel.

## Advanced Options

If you don't want to modify your HTML, there's a pretty handy imperative API for you to use. All you need to do is declare a function, do your thing, and return a value.

For instance, if you want to dynamically set a `target`, you'll need to return a Node.

```js
new ClipboardJS('.btn', {
  target: function (trigger) {
    return trigger.nextElementSibling;
  },
});
```

If you want to dynamically set a `text`, you'll return a String.

```js
new ClipboardJS('.btn', {
  text: function (trigger) {
    return trigger.getAttribute('aria-label');
  },
});
```

For use in Bootstrap Modals or with any other library that changes the focus you'll want to set the focused element as the `container` value.

```js
new ClipboardJS('.btn', {
  container: document.getElementById('modal'),
});
```

Also, if you are working with single page apps, you may want to manage the lifecycle of the DOM more precisely. Here's how you clean up the events and objects that we create.

```js
var clipboard = new ClipboardJS('.btn');
clipboard.destroy();
```

## Browser Support

This library relies on both [Selection](https://developer.mozilla.org/en-US/docs/Web/API/Selection) and [execCommand](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) APIs. The first one is [supported by all browsers](https://caniuse.com/#search=selection) while the second one is supported in the following browsers.

| <img src="https://clipboardjs.com/assets/images/chrome.png" width="48px" height="48px" alt="Chrome logo"> | <img src="https://clipboardjs.com/assets/images/edge.png" width="48px" height="48px" alt="Edge logo"> | <img src="https://clipboardjs.com/assets/images/firefox.png" width="48px" height="48px" alt="Firefox logo"> | <img src="https://clipboardjs.com/assets/images/ie.png" width="48px" height="48px" alt="Internet Explorer logo"> | <img src="https://clipboardjs.com/assets/images/opera.png" width="48px" height="48px" alt="Opera logo"> | <img src="https://clipboardjs.com/assets/images/safari.png" width="48px" height="48px" alt="Safari logo"> |
| :-------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------: |
|                                                   42+ ✔                                                   |                                                 12+ ✔                                                 |                                                    41+ ✔                                                    |                                                       9+ ✔                                                       |                                                  29+ ✔                                                  |                                                   10+ ✔                                                   |

The good news is that clipboard.js gracefully degrades if you need to support older browsers. All you have to do is show a tooltip saying `Copied!` when `success` event is called and `Press Ctrl+C to copy` when `error` event is called because the text is already selected.

You can also check if clipboard.js is supported or not by running `ClipboardJS.isSupported()`, that way you can hide copy/cut buttons from the UI.

## Bonus

A browser extension that adds a "copy to clipboard" button to every code block on _GitHub, MDN, Gist, StackOverflow, StackExchange, npm, and even Medium._

Install for [Chrome](https://chrome.google.com/webstore/detail/codecopy/fkbfebkcoelajmhanocgppanfoojcdmg) and [Firefox](https://addons.mozilla.org/en-US/firefox/addon/codecopy/).

## License

[MIT License](https://zenorocha.mit-license.org/) © Zeno Rocha
