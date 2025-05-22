---
title: Sự kiện
prev: /tutorial/01-vdom
next: /tutorial/03-components
solvable: true
---

# Sự kiện

Sự kiện là cách chúng ta làm cho ứng dụng trở nên tương tác, phản hồi các đầu vào như bàn phím và chuột, cũng như phản ứng với các thay đổi như khi một hình ảnh được tải xong. Sự kiện hoạt động trong Preact giống hệt như trong DOM – bất kỳ loại sự kiện hoặc hành vi nào bạn tìm thấy trên [MDN] đều có thể dùng trong Preact. Ví dụ, đây là cách các trình xử lý sự kiện thường được đăng ký bằng API DOM kiểu mệnh lệnh:

```js
function clicked() {
  console.log('đã nhấn')
}
const myButton = document.getElementById('my-button')
myButton.addEventListener('click', clicked)
```

Điểm khác biệt của Preact so với API DOM là cách đăng ký các trình xử lý sự kiện.
Trong Preact, các trình xử lý sự kiện được đăng ký một cách khai báo như các prop trên phần tử, giống như `style` và `class`. Nói chung, bất kỳ prop nào có tên bắt đầu bằng "on" đều là một trình xử lý sự kiện. Giá trị của prop này là hàm sẽ được gọi khi sự kiện đó xảy ra.

Ví dụ, chúng ta có thể lắng nghe sự kiện "click" trên một nút bằng cách thêm prop `onClick` với hàm xử lý của mình làm giá trị:

```jsx
function clicked() {
  console.log('đã nhấn')
}
<button onClick={clicked}>
```

Tên các prop xử lý sự kiện phân biệt chữ hoa chữ thường, giống như tất cả các prop khác. Tuy nhiên, Preact sẽ tự động nhận biết khi bạn đăng ký một loại sự kiện chuẩn trên một Element (click, change, touchmove, v.v.), và sử dụng đúng kiểu chữ phía sau. Đó là lý do tại sao `<button onClick={..}>` hoạt động dù sự kiện là `"click"` (chữ thường).

---

## Thực hành!

Để hoàn thành chương này, hãy thử thêm trình xử lý sự kiện click của riêng bạn vào JSX cho phần tử nút ở bên phải. Trong hàm xử lý, hãy ghi một thông báo bằng `console.log()` như ví dụ trên.

Khi mã của bạn chạy, hãy nhấn nút để gọi hàm xử lý sự kiện và chuyển sang chương tiếp theo.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn vừa học cách xử lý sự kiện trong Preact.</p>
</solution>

```js:setup
useRealm(function (realm) {
  var win = realm.globalThis;
  var prevConsoleLog = win.console.log;
  win.console.log = function() {
    solutionCtx.setSolved(true);
    return prevConsoleLog.apply(win.console, arguments);
  };

  return function () {
    win.console.log = prevConsoleLog;
  };
}, []);
```

```jsx:repl-initial
import { render } from "preact";

function App() {
  return (
    <div>
      <p class="count">Số đếm:</p>
      <button>Nhấn tôi!</button>
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render } from "preact";

function App() {
  const clicked = () => {
    console.log('xin chào')
  }

  return (
    <div>
      <p class="count">Số đếm:</p>
      <button onClick={clicked}>Nhấn tôi!</button>
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

[MDN]: https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events
