# 복붙 자바스크립트 (clipboard.js)

![Build Status](https://github.com/zenorocha/clipboard.js/workflows/build/badge.svg)
![Killing Flash](https://img.shields.io/badge/killing-flash-brightgreen.svg?style=flat)

> 현대적인 클립보드에 복사하기. 플래시 없음. 압축된 3kb만 있음. 

<a href="https://clipboardjs.com/"><img width="728" src="https://cloud.githubusercontent.com/assets/398893/16165747/a0f6fc46-349a-11e6-8c9b-c5fd58d9099c.png" alt="Demo"></a>

## 이유

클립보드에 텍스트를 복사하는 것은 어렵지 않아야 한다. 여러 단계에 걸친 설정이나 수백 kB를 로드할 이유가 없다. 그리고 가장 중요한 것은, 플래시나 다른 비대한 프레임워크에 의존하지 않는 것이 좋다. 

이것이 복붙 자바스크립트 (clipboard.js)의 존재 이유다.

## 설치

npm에서 다운받을 수 있다.

```
npm install clipboard --save
```

패키지 관리 프로그램에 흥미가 없으면, 그냥 [ZIP 파일 다운로드](https://github.com/zenorocha/clipboard.js/archive/master.zip)를 하면 된다.

## 사용할 준비

먼저, `dist` 폴더에 있는 스크립트를 include하거나, [클라우드](https://github.com/zenorocha/clipboard.js/wiki/CDN-Providers)에서 로드한다.

```html
<script src="dist/clipboard.min.js"></script>
```

이제, 객체를 생성한다. 생성자의 파라미터로 [DOM 셀렉터](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-selector.html#L18), [HTML 요소](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-node.html#L16-L17), or [HTML 요소 리스트](https://github.com/zenorocha/clipboard.js/blob/master/demo/constructor-nodelist.html#L18-L19) 중 하나를 전달한다.

```js
new ClipboardJS('.btn');
```

내부적으로, 우리는 셀렉터에 매칭된 모든 요소들을 찾아서 각각에 리스너를 붙여야 한다. 하지만 매칭된 결과가 수백개면, 이 작업이 메모리를 많이 소모할 수 있다.

이러한 이유로 우리는 [이벤트 델리게이션](https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation)을 써서 이벤트 리스너 여러개를 이벤트 리스너 한개로 대체한다. 결국, [#perfmatters](https://twitter.com/hashtag/perfmatters)다.

# Usage

우리는 _선언 르네상스_ 시대에 살고 있다. 그래서 우리는 [HTML5 data attributes](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Using_data_attributes)을 써서 사용하기 쉽게 만들었다.

### 다른 요소에서 텍스트 복사하기

꽤 보편적인 사용 케이스는 다른 요소에서 내용을 복사해오는 것이다. `data-clipboard-target` 속성을 트리거 요소에 추가해서 복사할 수 있다. 

속성에 넣은 값은 다른 요소의 셀렉터에 매칭이 되어야 한다.

<a href="https://clipboardjs.com/#example-target"><img width="473" alt="example-2" src="https://cloud.githubusercontent.com/assets/398893/9983467/a4946aaa-5fb1-11e5-9780-f09fcd7ca6c8.png"></a>

```html
<!-- 타겟 (복사할 값이 저장되어 있는 요소) -->
<input id="foo" value="https://github.com/zenorocha/clipboard.js.git" />

<!-- 트리거 (복사하는 버튼) -->
<button class="btn" data-clipboard-target="#foo">
  <img src="assets/clippy.svg" alt="클립보드로 복사하기" />
</button>
```

### 다른 요소에서 텍스트 오려내기

추가적으로, `data-clipboard-action` 속성을 지정하면 `복사`를 할지 `오려넣기`를 할지 정할 수 있다.

속성을 생략할 시에는, `복사`가 디폴트 값이다.

<a href="https://clipboardjs.com/#example-action"><img width="473" alt="example-3" src="https://cloud.githubusercontent.com/assets/398893/10000358/7df57b9c-6050-11e5-9cd1-fbc51d2fd0a7.png"></a>

```html
<!-- 타겟 (복사할 값이 저장되어 있는 요소) -->
<textarea id="bar">Mussum ipsum cacilds...</textarea>

<!-- 트리거 (복사하는 버튼) -->
<button class="btn" data-clipboard-action="cut" data-clipboard-target="#bar">
  클립보드로 오려넣기 
</button>
```

예상했겠지만, `오려넣기` 작업은 `<input>` 요소나 `<textarea>` 요소에서만 동작한다.

### 속성에서 텍스트 복사하기

사실, 내용을 복사해올 다른 요소가 꼭 있어야하는 것은 아니다. 트리거 요소에 `data-clipboard-text` 속성을 추가하기만 해도 된다.

<a href="https://clipboardjs.com/#example-text"><img width="147" alt="example-1" src="https://cloud.githubusercontent.com/assets/398893/10000347/6e16cf8c-6050-11e5-9883-1c5681f9ec45.png"></a>

```html
<!-- 트리거 (복사하는 버튼) -->
<button
  class="btn"
  data-clipboard-text="할 수 있다고 해서 꼭 해야하는 것은 아니다 — clipboard.js"
>
  클립보드에 복사하기
</button>
```

## 이벤트

어떤 경우에는 유저 피드백을 보여주거나, 복사/오려넣기 작업 후에도 텍스트를 저장할 필요가 있다.

그래서 우리는 `success` 또는 `error` 등의 맞춤 이벤트를 만들어서 당신이 듣고, 코딩을 할 수 있게 지원한다.

```js
var clipboard = new ClipboardJS('.btn');

clipboard.on('success', function (e) {
  console.info('작업:', e.action);
  console.info('텍스트:', e.text);
  console.info('트리거:', e.trigger);

  e.clearSelection();
});

clipboard.on('error', function (e) {
  console.error('작업:', e.action);
  console.error('트리거:', e.trigger);
});
```

라이브 시연을 보려면, [홈페이지](https://clipboardjs.com/)에 접속하고 콘솔을 열어보면 된다.

## 커서를 올려놓으면 나오는 메시지 (Tooltips)

각 어플리케이션은 다른 디자인이 요구되며, 그래서 복붙 자바스크립트 (clipboard.js)에 CSS나 빌트인 커서 메시지 솔루션이 없다.

[데모 홈페이지](https://clipboardjs.com/)에서 보이는 커서 메시지는 [깃허브 프라이머](https://primer.style/css/components/tooltips)로 만들었다. 비슷한 모양과 느낌을 원한다면 확인해볼 것을 권장한다. 

## 고급 옵션

HTML을 수정하고 싶지 않다면 쓸 수 있는 꽤 편리한 명령형 API가 있다. 함수를 선언하고, 함수를 써서 작업을 하고, 값을 반환하기만 하면 된다. 

예를 들어서, `target`을 동적으로 설정하고 싶으면, 노드를 반환하기만 하면 된다. 

```js
new ClipboardJS('.btn', {
  target: function (trigger) {
    return trigger.nextElementSibling;
  },
});
```

`text`를 동적으로 설정하고 싶으면, 문자열을 반환하면 된다. 

```js
new ClipboardJS('.btn', {
  text: function (trigger) {
    return trigger.getAttribute('aria-label');
  },
});
```

부트스트랩 형식 또는 포커스를 바꿀 수 있는 다른 라이브러리를 쓰기 위해서는, 포커스된 요소를 `container` 값으로 설정합니다. 

```js
new ClipboardJS('.btn', {
  container: document.getElementById('modal'),
});
```

또한, 단일 페이지 앱을 만들고 있다면, DOM을 더 정밀하게 관리하고 싶을 수 있습니다. 우리가 만든 이벤트와 객체는 이렇게 정리할 수 있습니다.

```js
var clipboard = new ClipboardJS('.btn');
clipboard.destroy();
```

## 브라우저 지원

본 라이브러리는 [Selection](https://developer.mozilla.org/en-US/docs/Web/API/Selection)과 [execCommand](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand) API들에 의존하고 있습니다. Selection API는 [모든 브라우저에서 지원](https://caniuse.com/#search=selection)되고, execCommand는 다음 브라우저에서 지원됩니다.

| <img src="https://clipboardjs.com/assets/images/chrome.png" width="48px" height="48px" alt="Chrome logo"> | <img src="https://clipboardjs.com/assets/images/edge.png" width="48px" height="48px" alt="Edge logo"> | <img src="https://clipboardjs.com/assets/images/firefox.png" width="48px" height="48px" alt="Firefox logo"> | <img src="https://clipboardjs.com/assets/images/ie.png" width="48px" height="48px" alt="Internet Explorer logo"> | <img src="https://clipboardjs.com/assets/images/opera.png" width="48px" height="48px" alt="Opera logo"> | <img src="https://clipboardjs.com/assets/images/safari.png" width="48px" height="48px" alt="Safari logo"> |
| :-------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------: |
|                                                   42+ ✔                                                   |                                                 12+ ✔                                                 |                                                    41+ ✔                                                    |                                                       9+ ✔                                                       |                                                  29+ ✔                                                  |                                                   10+ ✔                                                   |

좋은 소식은 오래된 브라우저를 지원해야한다면, 복붙 자바스크립트 (clipboard.js)는 상태에 특별한 변화 없이 동작을 정지합니다. 따라서 `성공` 이벤트가 발생했을 때 `복사됨!"이라는 메세지를 보여주고, `실패` 이벤트가 발생했을 때는 텍스트가 이미 선택되었으므로 `복사하려면 Ctrl+C 누르기`이라는 메세지를 보여주면 됩니다. 

또한, 복붙 자바스크립트 (clipboard.js)가 지원되는지 지원되지 않는지를 체크하려면 `ClipboardJS.isSupported()`를 실행하면 됩니다. 지원되지 않으면 유저 인터페이스에서 복사/오려넣기 버튼을 숨길 수 있습니다. 

## 보너스

_GitHub, MDN, Gist, StackOverflow, StackExchange, npm, and even Medium._ 의 모든 코드 블록에 "클립보드에 복사하기" 버튼을 추가하는 브라우저 확장

[Chrome](https://chrome.google.com/webstore/detail/codecopy/fkbfebkcoelajmhanocgppanfoojcdmg)과 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/codecopy/)에 지원됨.

## 저작권

[MIT License](https://zenorocha.mit-license.org/) © Zeno Rocha
