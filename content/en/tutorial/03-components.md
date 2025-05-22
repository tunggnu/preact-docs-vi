---
title: Thành phần
prev: /tutorial/02-events
next: /tutorial/04-state
solvable: true
---

# Thành phần

Như chúng ta đã đề cập ở phần một của hướng dẫn này, khối xây dựng chính trong các ứng dụng Virtual DOM là Thành phần (Component). Một Thành phần là một phần tự chứa của ứng dụng có thể được render như một phần của cây Virtual DOM giống như một phần tử HTML. Bạn có thể nghĩ về Thành phần như một lời gọi hàm: cả hai đều là cơ chế cho phép tái sử dụng mã và trừu tượng hóa.

Để minh họa, hãy tạo một thành phần đơn giản tên là `MyButton` trả về một cây Virtual DOM mô tả một phần tử HTML `<button>`:

```jsx
function MyButton(props) {
  return <button class="my-button">{props.text}</button>
}
```

Chúng ta có thể sử dụng thành phần này trong ứng dụng bằng cách tham chiếu nó trong JSX:

```js
let vdom = <MyButton text="Nhấn tôi!" />

// còn nhớ createElement chứ? dòng trên sẽ được biên dịch thành:
let vdom = createElement(MyButton, { text: "Nhấn tôi!" })
```

Bất cứ nơi nào bạn dùng JSX để mô tả cây HTML, bạn cũng có thể mô tả cây Thành phần. Sự khác biệt là một thành phần được mô tả trong JSX bằng một tên bắt đầu bằng chữ hoa tương ứng với tên thành phần (một biến JavaScript).

Khi Preact render cây Virtual DOM được mô tả bởi JSX của bạn, mỗi hàm thành phần mà nó gặp sẽ được gọi tại vị trí đó trong cây. Ví dụ, chúng ta có thể render thành phần `MyButton` vào body của trang web bằng cách truyền một phần tử JSX mô tả thành phần đó cho `render()`:

```jsx
import { render } from 'preact';

render(<MyButton text="Nhấn tôi!" />, document.body)
```

### Lồng thành phần

Các thành phần có thể tham chiếu các thành phần khác trong cây Virtual DOM mà chúng trả về. Điều này tạo ra một cây các thành phần:

```jsx
function MediaPlayer() {
  return (
    <div>
      <MyButton text="Phát" />
      <MyButton text="Dừng" />
    </div>
  )
}

render(<MediaPlayer />, document.body)
```

Chúng ta có thể sử dụng kỹ thuật này để render các cây thành phần khác nhau cho các trường hợp khác nhau. Hãy làm cho `MediaPlayer` hiển thị nút "Phát" khi chưa phát âm thanh, và nút "Dừng" khi đang phát:

```jsx
function MediaPlayer(props) {
  return (
    <div>
      {props.playing ? (
        <MyButton text="Dừng" />
      ) : (
        <MyButton text="Phát" />
      )}
    </div>
  )
}

render(<MediaPlayer playing={false} />, document.body)
// render ra <button>Phát</button>

render(<MediaPlayer playing={true} />, document.body)
// render ra <button>Dừng</button>
```

> **Nhớ rằng:** dấu ngoặc nhọn `{curly}` trong JSX cho phép chúng ta nhảy vào JavaScript thuần. Ở đây chúng ta dùng biểu thức [ternary] để hiển thị các nút khác nhau dựa trên giá trị của prop `playing`.

### Con của thành phần

Các thành phần cũng có thể được lồng vào nhau giống như các phần tử HTML. Một trong những lý do Thành phần là một nguyên thủy mạnh mẽ là vì chúng cho phép chúng ta áp dụng logic tùy chỉnh để kiểm soát cách các phần tử Virtual DOM lồng bên trong một thành phần được render.

Cách hoạt động rất đơn giản: bất kỳ phần tử Virtual DOM nào lồng bên trong một thành phần trong JSX sẽ được truyền vào thành phần đó dưới dạng prop đặc biệt `children`. Một thành phần có thể chọn vị trí đặt các con của nó bằng cách tham chiếu chúng trong JSX với biểu thức `{children}`. Hoặc, thành phần có thể chỉ cần trả về giá trị `children`, và Preact sẽ render các phần tử Virtual DOM đó ngay tại vị trí thành phần đó trong cây Virtual DOM.

```jsx
<Foo>
  <a />
  <b />
</Foo>

function Foo(props) {
  return props.children  // [<a />, <b />]
}
```

Quay lại ví dụ trước, thành phần `MyButton` của chúng ta mong đợi một prop `text` được chèn vào phần tử `<button>` làm nội dung hiển thị. Nếu chúng ta muốn hiển thị một hình ảnh thay vì văn bản thì sao?

Hãy viết lại `MyButton` để cho phép lồng nội dung bằng prop `children`:

```jsx
function MyButton(props) {
  return <button class="my-button">{props.children}</button>
}

function App() {
  return (
    <MyButton>
      <img src="icon.png" />
      Nhấn tôi!
    </MyButton>
  )
}

render(<App />, document.body)
```

Bây giờ, sau khi xem một vài ví dụ về các thành phần render các thành phần khác, hy vọng bạn đã thấy rõ cách các thành phần lồng nhau giúp chúng ta xây dựng các ứng dụng phức tạp từ nhiều mảnh nhỏ hơn.

---

### Các loại thành phần

<!--
Cho đến giờ, chúng ta đã thấy các Thành phần là hàm. Thành phần hàm nhận vào `props` làm đầu vào và trả về một cây Virtual DOM làm đầu ra. Nếu chúng ta muốn viết một Thành phần render các cây Virtual DOM khác nhau dựa trên đầu vào ngoài `props` thì sao?

Ngoài việc ánh xạ `props` sang cây Virtual DOM, thành phần cũng có thể tự cập nhật _chính nó_. Có hai cách để làm điều này: class component và hooks. Chúng ta sẽ nói về hooks sau.
-->

Cho đến giờ, chúng ta đã thấy các Thành phần là hàm. Thành phần hàm nhận vào `props` làm đầu vào và trả về một cây Virtual DOM làm đầu ra. Thành phần cũng có thể được viết dưới dạng class JavaScript, được Preact khởi tạo và cung cấp phương thức `render()` hoạt động giống như một thành phần hàm.

Class component được tạo bằng cách kế thừa lớp cơ sở `Component` của Preact. Trong ví dụ dưới đây, hãy chú ý cách `render()` nhận `props` làm đầu vào và trả về một cây Virtual DOM làm đầu ra - giống như một thành phần hàm!

```jsx
import { Component } from 'preact';

class MyButton extends Component {
  render(props) {
    return <button class="my-button">{props.children}</button>
  }
}

render(<MyButton>Nhấn tôi!</MyButton>, document.body)
```

Lý do chúng ta có thể dùng class để định nghĩa thành phần là để theo dõi _vòng đời_ của thành phần. Mỗi lần Preact gặp một thành phần khi render cây Virtual DOM, nó sẽ tạo một instance mới của class đó (`new MyButton()`).

Tuy nhiên, nếu bạn còn nhớ từ chương một - Preact có thể liên tục nhận các cây Virtual DOM mới. Mỗi lần chúng ta cung cấp cho Preact một cây mới, nó sẽ so sánh với cây trước để xác định những gì đã thay đổi, và áp dụng các thay đổi đó lên trang.

Khi một thành phần được định nghĩa bằng class, mọi _cập nhật_ cho thành phần đó trong cây sẽ tái sử dụng cùng một instance class. Điều đó có nghĩa là bạn có thể lưu trữ dữ liệu bên trong class component và dữ liệu đó sẽ có sẵn cho lần gọi `render()` tiếp theo.

Class component cũng có thể triển khai một số [phương thức vòng đời] mà Preact sẽ gọi khi có thay đổi trong cây Virtual DOM:

```jsx
class MyButton extends Component {
  componentDidMount() {
    console.log('Xin chào từ một component <MyButton> mới!')
  }
  componentDidUpdate() {
    console.log('Một component <MyButton> đã được cập nhật!')
  }
  render(props) {
    return <button class="my-button">{props.children}</button>
  }
}

render(<MyButton>Nhấn tôi!</MyButton>, document.body)
// log: "Xin chào từ một component <MyButton> mới!"

render(<MyButton>Nhấn tôi!</MyButton>, document.body)
// log: "Một component <MyButton> đã được cập nhật!"
```

Vòng đời của class component khiến chúng trở thành công cụ hữu ích để xây dựng các phần của ứng dụng phản ứng với thay đổi, thay vì chỉ ánh xạ `props` sang cây. Chúng cũng cung cấp cách lưu trữ thông tin riêng biệt tại mỗi vị trí chúng được đặt trong cây Virtual DOM. Ở chương tiếp theo, chúng ta sẽ xem cách các thành phần có thể cập nhật phần cây của mình bất cứ khi nào muốn thay đổi nó.

---

## Thực hành!

Để luyện tập, hãy kết hợp những gì bạn đã học về thành phần với kỹ năng sự kiện từ hai chương trước!

Tạo một thành phần `MyButton` nhận các prop `style`, `children` và `onClick`, và trả về một phần tử HTML `<button>` với các prop đó được áp dụng.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn đang trên con đường trở thành chuyên gia về thành phần!</p>
</solution>


```js:setup
useRealm(function (realm) {
  var options = require('preact').options;
  var win = realm.globalThis;
  var prevConsoleLog = win.console.log;
  var hasComponent = false;
  var check = false;

  win.console.log = function() {
    if (hasComponent && check) {
      solutionCtx.setSolved(true);
    }
    return prevConsoleLog.apply(win.console, arguments);
  };

  var e = options.event;
  options.event = function(e) {
    if (e.type === 'click') {
      check = true;
      setTimeout(() => check = false);
    }
  };

  var r = options.__r;
  options.__r = function(vnode) {
    if (typeof vnode.type === 'function' && /MyButton/.test(vnode.type)) {
      hasComponent = true;
    }
  }

  return function () {
    options.event = e;
    options.__r = r;
    win.console.log = prevConsoleLog;
  };
}, []);
```


```jsx:repl-initial
import { render } from "preact";

function MyButton(props) {
  // bắt đầu từ đây!
}

function App() {
  const clicked = () => {
    console.log('Xin chào!')
  }

  return (
    <div>
      <p class="count">Số đếm:</p>
      <button style={{ color: 'purple' }} onClick={clicked}>Nhấn tôi</button>
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render } from "preact";

function MyButton(props) {
  return <button style={props.style} onClick={props.onClick}>{props.children}</button>
}

function App() {
  const clicked = () => {
    console.log('Xin chào!')
  }

  return (
    <div>
      <p class="count">Số đếm:</p>
      <MyButton style={{ color: 'purple' }} onClick={clicked}>Nhấn tôi</MyButton>
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

[ternary]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator
[phương thức vòng đời]: /guide/v10/components#lifecycle-methods
