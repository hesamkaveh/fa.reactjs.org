---
id: handling-events
title: Handling Events
permalink: docs/handling-events.html
prev: state-and-lifecycle.html
next: conditional-rendering.html
redirect_from:
  - "docs/events-ko-KR.html"
---
<div dir=rtl>
بکارگیری  event ها با  استفاده از المان‌های ری‌اکت بسیار مشابه با به کارگیری event ها در المان‌های DOM می‌باشد. با این تفاوت که کمی از نظر نوع نوشتن متفاوت هستند:
</div>

<ul dir=rtl>
	<li>
			در ری‌اکت event ها به صورت camelCase نوشته می‌شوند، به جای اینکه lowercase نوشته شوند.
	</li>
	<li>
با JSX شما یک تابع را به عنوان event handler انتقال می‌دهید به جای اینکه یک  string  را انتقال دهید.
	</li>
</ul>

<div dir=rtl>
برای مثال در HTML:
</div>

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```
<div dir=rtl>
کمی تفاوت نسبت به ری‌اکت می‌بینیم:
</div>

```js{1}
<button onClick={activateLasers}>
  Activate Lasers
</button>
```
<div dir=rtl>
یکی دیگر از تفاوت‌ها این  می‌باشد که در ری‌اکت شما نمی‌توانید برای جلوگیری از رفتار پیش فرض مقدار <code>false</code> را قرار دهید. شما باید به صراحت تابع <code>preventDefault</code> را صدا بزنید. برای مثال،  در HTML ساده،  برای جلوگیری از رفتار پیش فرض لینک در باز شدن یک صفحه، به شکل زیر بنویسید:
</div>

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```
<div dir=rtl>
در ری‌اکت، به این شکل می‌توان نوشت:
</div>

```js{2-5,8}
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```
<div dir=rtl>
 در اینجا <code>e</code>  یک رویداد مصنوعی می‌باشد. ری‌اکت این رویداد مصنوعی را با توجه به 
<a href="https://www.w3.org/TR/DOM-Level-3-Events/">W3C spec</a>
تعریف می‌کند، بنابر این نیاز نیست شما نگران سازگار بودن در مرورگرهای متفاوت باشید. برای مطالعه بیشتر در مورد رویدادهای مصنوعی به راهنمای مرجع 
<a href=""><code>SyntheticEvent</code></a>
مراجعه کنید.
</div>
<br />
<div dir=rtl>
زمانی که از ری‌اکت استفاده می‌کنید نیازی ندارید برای افزودن یک listener به المان DOM ، حتما بعد از ساخته شدنش تابع
 <code>addEventListener</code>
 اضافه کنید. به جای آن در زمان اولیه رندر المان برایش listener آماده کنید. 
</div>
<br />
<div dir=rtl>
زمانی که  برای تعریف یک کامپوننت از 
<a href="https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes"><code dir=rtl>کلاس ES6</code></a>
استفاده می‌کنید، یک الگوی متداول برای event handler یک تابع در کلاس می‌باشد. درواقع کامپوننت 
<code>Toggle</code>
دکمه‌ای را رندر می کند که اجازه می‌دهد که کاربر بین state های  "ON" و "OFF" تغییر وضعیت دهد:
</div>

```js{6,7,10-14,18}
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```
<div dir=rtl>
<b>
<a href="http://codepen.io/gaearon/pen/xEmzGg?editors=0010">
روی CodePen امتحان کنید
</a>
</b>
</dir>


You have to be careful about the meaning of `this` in JSX callbacks. In JavaScript, class methods are not [bound](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) by default. If you forget to bind `this.handleClick` and pass it to `onClick`, `this` will be `undefined` when the function is actually called.

This is not React-specific behavior; it is a part of [how functions work in JavaScript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/). Generally, if you refer to a method without `()` after it, such as `onClick={this.handleClick}`, you should bind that method.

If calling `bind` annoys you, there are two ways you can get around this. If you are using the experimental [public class fields syntax](https://babeljs.io/docs/plugins/transform-class-properties/), you can use class fields to correctly bind callbacks:

```js{2-6}
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

This syntax is enabled by default in [Create React App](https://github.com/facebookincubator/create-react-app).

If you aren't using class fields syntax, you can use an [arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) in the callback:

```js{7-9}
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

The problem with this syntax is that a different callback is created each time the `LoggingButton` renders. In most cases, this is fine. However, if this callback is passed as a prop to lower components, those components might do an extra re-rendering. We generally recommend binding in the constructor or using the class fields syntax, to avoid this sort of performance problem.

## Passing Arguments to Event Handlers {#passing-arguments-to-event-handlers}

Inside a loop it is common to want to pass an extra parameter to an event handler. For example, if `id` is the row ID, either of the following would work:

```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

The above two lines are equivalent, and use [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) and [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) respectively.

In both cases, the `e` argument representing the React event will be passed as a second argument after the ID. With an arrow function, we have to pass it explicitly, but with `bind` any further arguments are automatically forwarded.
