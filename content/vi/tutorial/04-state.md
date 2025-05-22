---
title: State
prev: /tutorial/03-components
next: /tutorial/05-refs
solvable: true
---

# State (Trạng thái)

Bây giờ chúng ta đã biết cách tạo các phần tử HTML và thành phần, cũng như cách truyền props và các hàm xử lý sự kiện cho cả hai bằng JSX, đã đến lúc tìm hiểu cách cập nhật cây Virtual DOM.

Như đã đề cập ở chương trước, cả thành phần hàm và thành phần lớp đều có thể có **state** (trạng thái) – dữ liệu được lưu trữ bởi một thành phần và được sử dụng để thay đổi cây Virtual DOM của nó. Khi một thành phần cập nhật state của nó, Preact sẽ render lại thành phần đó với giá trị state mới. Đối với thành phần hàm, điều này có nghĩa là Preact sẽ gọi lại hàm đó, còn với thành phần lớp thì chỉ gọi lại phương thức `render()` của lớp đó. Hãy cùng xem ví dụ cho từng loại.

## State trong thành phần lớp

Thành phần lớp có một thuộc tính `state`, là một đối tượng chứa dữ liệu mà thành phần có thể sử dụng khi phương thức `render()` của nó được gọi. Một thành phần có thể gọi `this.setState()` để cập nhật thuộc tính `state` của nó và yêu cầu Preact render lại thành phần đó.

```jsx
class MyButton extends Component {
  state = { clicked: false }

  handleClick = () => {
    this.setState({ clicked: true })
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.clicked ? 'Đã nhấn' : 'Chưa nhấn lần nào'}
      </button>
    )
  }
}
```

Khi nhấn nút sẽ gọi `this.setState()`, điều này khiến Preact gọi lại phương thức `render()` của lớp. Khi đó, `this.state.clicked` đã là `true`, nên phương thức `render()` trả về một cây Virtual DOM chứa văn bản "Đã nhấn" thay vì "Chưa nhấn lần nào", khiến Preact cập nhật nội dung nút trên DOM.

## State trong thành phần hàm với hooks

Thành phần hàm cũng có thể có state! Dù chúng không có thuộc tính `this.state` như thành phần lớp, một module nhỏ đi kèm với Preact cung cấp các hàm để lưu trữ và làm việc với state bên trong thành phần hàm, gọi là "hooks".

Hooks là các hàm đặc biệt có thể được gọi bên trong một thành phần hàm. Chúng đặc biệt vì chúng **ghi nhớ thông tin qua các lần render**, giống như thuộc tính và phương thức trên một class. Ví dụ, hook `useState` trả về một mảng gồm một giá trị và một hàm "setter" có thể được gọi để cập nhật giá trị đó. Khi một thành phần được gọi lại (render lại) nhiều lần, mọi lần gọi `useState()` sẽ trả về đúng cùng một mảng như trước.

> ℹ️ **_Hooks hoạt động như thế nào?_**
>
> Ở phía bên trong, các hàm hook như `setState` hoạt động bằng cách lưu trữ dữ liệu trong một chuỗi các "slot" gắn với mỗi thành phần trong cây Virtual DOM. Mỗi lần gọi một hook sẽ sử dụng một slot, và tăng một bộ đếm "slot number" nội bộ để lần gọi tiếp theo dùng slot tiếp theo. Preact sẽ đặt lại bộ đếm này trước khi gọi mỗi thành phần, nên mỗi lần gọi hook sẽ gắn với cùng một slot khi thành phần được render nhiều lần.
>
> ```js
> function User() {
>   const [name, setName] = useState("Bob")    // slot 0
>   const [age, setAge] = useState(42)         // slot 1
>   const [online, setOnline] = useState(true) // slot 2
> }
> ```
>
> Điều này gọi là "call site ordering", và đó là lý do tại sao các hook luôn phải được gọi theo cùng một thứ tự trong một thành phần, và không được gọi có điều kiện hoặc trong vòng lặp.

Hãy xem ví dụ về hook `useState`:

```jsx
import { useState } from 'preact/hooks'

const MyButton = () => {
  const [clicked, setClicked] = useState(false)

  const handleClick = () => {
    setClicked(true)
  }

  return (
    <button onClick={handleClick}>
      {clicked ? 'Đã nhấn' : 'Chưa nhấn lần nào'}
    </button>
  )
}
```

Khi nhấn nút sẽ gọi `setClicked(true)`, cập nhật trường state được tạo bởi `useState()`, điều này khiến Preact render lại thành phần này. Khi thành phần được render (gọi) lần thứ hai, giá trị của biến state `clicked` sẽ là `true`, và Virtual DOM trả về sẽ có văn bản "Đã nhấn" thay vì "Chưa nhấn lần nào". Điều này khiến Preact cập nhật nội dung nút trên DOM.

---

## Thực hành!

Hãy thử tạo một bộ đếm, bắt đầu từ mã chúng ta đã viết ở chương trước. Chúng ta sẽ cần lưu trữ một số `count` trong state, và tăng giá trị của nó lên `1` mỗi khi nhấn nút.

Vì chúng ta đã dùng thành phần hàm ở chương trước, nên có thể dễ dàng dùng hooks, nhưng bạn có thể chọn bất kỳ cách lưu trữ state nào bạn thích.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn đã học cách sử dụng state!</p>
</solution>


```js:setup
useResult(function () {
  var options = require('preact').options;

  var oe = options.event;
  options.event = function(e) {
    if (oe) oe.apply(this, arguments);

    if (e.currentTarget.localName !== 'button') return;
    var root = e.currentTarget.parentNode.parentNode;
    var text = root.innerText.match(/Count:\s*([\w.-]*)/i);
    if (!text) return;
    if (!text[1].match(/^-?\d+$/)) {
      return console.warn('Gợi ý: có vẻ bạn chưa render {count} ở đâu đó.');
    }
    setTimeout(function() {
      var text2 = root.innerText.match(/Count:\s*([\w.-]*)/i);
      if (!text2) {
        return console.warn('Gợi ý: bạn có nhớ render {count} không?');
      }
      if (text2[1] == text[1]) {
        return console.warn('Gợi ý: hãy nhớ gọi hàm "setter" để thay đổi giá trị của `count`.');
      }
      if (!text2[1].match(/^-?\d+$/)) {
        return console.warn('Gợi ý: có vẻ như `count` đang được gán thành một giá trị không phải số.');
      }

      if (Number(text2[1]) === Number(text[1]) + 1) {
        solutionCtx.setSolved(true);
      }
    }, 10);
  }

  return function () {
    options.event = oe;
  };
}, []);
```


```jsx:repl-initial
import { render } from 'preact';
import { useState } from 'preact/hooks';

function MyButton(props) {
  return <button style={props.style} onClick={props.onClick}>{props.children}</button>
}

function App() {
  const clicked = () => {
    // tăng count lên 1 tại đây
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

```jsx:repl-final
import { render } from 'preact';
import { useState } from 'preact/hooks';

function MyButton(props) {
  return <button style={props.style} onClick={props.onClick}>{props.children}</button>
}

function App() {
  const [count, setCount] = useState(0)

  const clicked = () => {
    setCount(count + 1)
  }

  return (
    <div>
      <p class="count">Số đếm: {count}</p>
      <MyButton style={{ color: 'purple' }} onClick={clicked}>Nhấn tôi</MyButton>
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

[ternary]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator
[lifecycle methods]: /guide/v10/components#lifecycle-methods
