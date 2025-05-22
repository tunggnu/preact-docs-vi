---
title: Tham chiếu (Refs)
prev: /tutorial/04-state
next: /tutorial/06-context
solvable: true
---

# Tham chiếu (Refs)

Như chúng ta đã học ở chương đầu tiên, DOM cung cấp một API kiểu mệnh lệnh, cho phép chúng ta thay đổi bằng cách gọi các hàm trên các phần tử. Một ví dụ mà chúng ta có thể cần truy cập API DOM kiểu mệnh lệnh từ một component Preact là tự động chuyển focus vào một trường nhập liệu.

Prop `autoFocus` (hoặc thuộc tính `autofocus`) có thể được dùng để focus vào một input ngay lần đầu nó được render, tuy nhiên có những tình huống chúng ta muốn chuyển focus vào input tại một thời điểm cụ thể, hoặc khi có một sự kiện cụ thể xảy ra.

Trong các trường hợp cần tương tác trực tiếp với phần tử DOM, chúng ta có thể sử dụng một tính năng gọi là "ref" (tham chiếu). Một ref là một đối tượng JavaScript thông thường với thuộc tính `current` trỏ đến bất kỳ giá trị nào. Đối tượng JavaScript được truyền theo tham chiếu, nghĩa là bất kỳ hàm nào có quyền truy cập vào đối tượng ref đều có thể lấy hoặc gán giá trị của nó thông qua thuộc tính `current`. Preact không theo dõi các thay đổi của đối tượng ref, vì vậy chúng có thể được dùng để lưu trữ thông tin trong quá trình render, và sau đó có thể được truy cập bởi bất kỳ hàm nào có quyền truy cập vào đối tượng ref đó.

Chúng ta có thể xem cách sử dụng trực tiếp tính năng ref mà không cần render gì cả:

```js
import { createRef } from 'preact'

// tạo một ref:
const ref = createRef('giá trị ban đầu')
// { current: 'giá trị ban đầu' }

// đọc giá trị hiện tại của ref:
ref.current === 'giá trị ban đầu'

// cập nhật giá trị hiện tại của ref:
ref.current = 'giá trị mới'

// truyền ref đi nơi khác:
console.log(ref) // { current: 'giá trị mới' }
```

Điều làm cho ref hữu ích trong Preact là một đối tượng ref có thể được truyền vào một phần tử Virtual DOM trong quá trình render, và Preact sẽ gán giá trị của ref (thuộc tính `current`) thành phần tử HTML tương ứng. Khi đã được gán, chúng ta có thể sử dụng giá trị hiện tại của ref để truy cập và thay đổi phần tử HTML:

```jsx
import { createRef } from 'preact';

// tạo một ref:
const input = createRef()

// truyền ref như một prop cho phần tử Virtual DOM:
render(<input ref={input} />, document.body)

// truy cập phần tử DOM liên kết:
input.current // một phần tử <input> HTML
input.current.focus() // focus vào input!
```

Không nên sử dụng `createRef()` ở phạm vi toàn cục, vì nhiều lần render sẽ ghi đè giá trị hiện tại của ref. Thay vào đó, tốt nhất nên lưu ref dưới dạng thuộc tính của class:

```jsx
import { createRef, Component } from 'preact';

export default class App extends Component {
  input = createRef()

  // hàm này chạy sau khi <App> được render
  componentDidMount() {
    // truy cập phần tử DOM liên kết:
    this.input.current.focus();
  }

  render() {
    return <input ref={this.input} />
  }
}
```

Đối với thành phần hàm, hook `useRef()` cung cấp một cách tiện lợi để tạo ref và truy cập cùng ref đó ở các lần render tiếp theo. Ví dụ sau cũng cho thấy cách sử dụng hook `useEffect()` để gọi một hàm sau khi component được render, khi đó giá trị hiện tại của ref sẽ được gán thành phần tử input HTML:

```jsx
import { useRef, useEffect } from 'preact/hooks';

export default function App() {
  // tạo hoặc lấy lại ref của chúng ta:  (hook slot 0)
  const input = useRef()

  // hàm callback này sẽ chạy sau khi <App> được render:
  useEffect(() => {
    // truy cập phần tử DOM liên kết:
    input.current.focus()
  }, [])

  return <input ref={input} />
}
```

Hãy nhớ rằng, ref không chỉ giới hạn ở việc lưu trữ phần tử DOM. Chúng có thể được dùng để lưu thông tin giữa các lần render của một component mà không cần set state (vốn sẽ gây render lại). Chúng ta sẽ thấy một số ứng dụng của điều này ở các chương sau.

---

## Thực hành!

Bây giờ hãy thực hành bằng cách tạo một nút, khi nhấn vào sẽ focus vào trường nhập liệu bằng cách truy cập nó thông qua ref.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p><code>pro = createRef()</code> → <code>pro.current = 'bạn'</code></p>
</solution>

```js:setup
function patch(input) {
  if (input.__patched) return;
  input.__patched = true;
  var old = input.focus;
  input.focus = function() {
    solutionCtx.setSolved(true);
    return old.call(this);
  };
}

useResult(function (result) {
  var expectedInput;
  var timer;
  [].forEach.call(result.output.querySelectorAll('input'), patch);

  var options = require('preact').options;

  var oe = options.event;
  options.event = function(e) {
    if (e.currentTarget.localName !== 'button') return;
    clearTimeout(timer);
    var input = e.currentTarget.parentNode.parentNode.querySelector('input');
    expectedInput = input;
    if (input) patch(input);
    timer = setTimeout(function() {
      if (expectedInput === input) {
        expectedInput = null;
      }
    }, 10);
    if (oe) return oe.apply(this, arguments);
  }

  return function () {
    options.event = oe;
  };
}, []);
```

```jsx:repl-initial
import { render } from 'preact';
import { useRef } from 'preact/hooks';

function App() {
  function onClick() {

  }

  return (
    <div>
      <input defaultValue="Hello World!" />
      <button onClick={onClick}>Focus input</button>
    </div>
  );
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render } from 'preact';
import { useRef } from 'preact/hooks';

function App() {
  const input = useRef();

  function onClick() {
    input.current.focus();
  }

  return (
    <div>
      <input ref={input} defaultValue="Hello World!" />
      <button onClick={onClick}>Focus input</button>
    </div>
  );
}

render(<App />, document.getElementById("app"));
```
