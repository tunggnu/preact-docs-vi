---
title: Tác động phụ (Side Effects)
prev: /tutorial/06-context
next: /tutorial/08-keys
solvable: true
---

# Tác động phụ (Side Effects)

Tác động phụ là những đoạn mã được chạy khi có sự thay đổi trong cây Virtual DOM. Chúng không tuân theo cách tiếp cận tiêu chuẩn là nhận `props` và trả về một cây Virtual DOM mới, và thường "vươn ra ngoài" cây để thay đổi trạng thái hoặc gọi mã kiểu mệnh lệnh, như gọi các API của DOM. Tác động phụ cũng thường được dùng để kích hoạt việc lấy dữ liệu.

### Effects: tác động phụ trong thành phần hàm

Chúng ta đã từng thấy một ví dụ về tác động phụ trong một chương trước, khi học về refs và hook `useRef()`. Khi ref của chúng ta đã được gán thuộc tính `current` trỏ đến một phần tử DOM, chúng ta cần một cách để "kích hoạt" mã sẽ tương tác với phần tử đó.

Để kích hoạt mã sau khi render, chúng ta dùng hook `useEffect()`, đây là cách phổ biến nhất để tạo tác động phụ từ một thành phần hàm:

```jsx
import { useRef, useEffect } from 'preact/hooks';

export default function App() {
  const input = useRef()

  // hàm callback này sẽ chạy sau khi <App> được render:
  useEffect(() => {
    // truy cập phần tử DOM liên kết:
    input.current.focus()
  }, [])

  return <input ref={input} />
}
```

Lưu ý mảng rỗng được truyền làm đối số thứ hai cho `useEffect()`. Các hàm callback effect sẽ chạy khi bất kỳ giá trị nào trong mảng "phụ thuộc" đó thay đổi giữa các lần render. Ví dụ, lần đầu tiên một thành phần được render, tất cả các callback effect sẽ chạy vì không có giá trị "phụ thuộc" trước đó để so sánh.

Chúng ta có thể thêm giá trị vào mảng "phụ thuộc" để kích hoạt callback effect dựa trên điều kiện, thay vì chỉ khi thành phần được render lần đầu. Điều này thường được dùng để chạy mã khi dữ liệu thay đổi, hoặc khi một thành phần bị loại khỏi trang ("unmounted").

Hãy xem một ví dụ:

```js
import { useEffect, useState } from 'preact/hooks';

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('<App> vừa được render lần đầu')
  }, [])

  useEffect(() => {
    console.log('giá trị count đã thay đổi thành: ', count)
  }, [count])
  //  ^ chạy đoạn này mỗi khi `count` thay đổi, và cả lần render đầu tiên

  return <button onClick={() => setCount(count+1)}>{count}</button>
}
```

### Phương thức vòng đời: tác động phụ trong thành phần lớp

Thành phần lớp cũng có thể định nghĩa các tác động phụ, bằng cách triển khai bất kỳ [phương thức vòng đời] nào mà Preact cung cấp. Dưới đây là một số phương thức vòng đời thường dùng nhất:

| Phương thức vòng đời         | Khi nào nó chạy:                        |
|:----------------------------|:----------------------------------------|
| `componentWillMount`        | ngay trước khi một component được render lần đầu
| `componentDidMount`         | sau khi một component được render lần đầu
| `componentWillReceiveProps` | trước khi một component được render lại
| `componentDidUpdate`        | sau khi một component được render lại

Một trong những ví dụ phổ biến nhất về việc sử dụng tác động phụ trong thành phần lớp là lấy dữ liệu khi một component được render lần đầu, sau đó lưu dữ liệu đó vào state. Ví dụ sau đây cho thấy một component yêu cầu thông tin người dùng từ một API JSON sau lần render đầu tiên, rồi hiển thị thông tin đó.

```jsx
import { Component } from 'preact';

export default class App extends Component {
  // hàm này được gọi sau khi component được render lần đầu:
  componentDidMount() {
    // lấy thông tin user JSON, lưu vào `state.user`:
    fetch('/api/user')
      .then(response => response.json())
      .then(user => {
        this.setState({ user })
      })
  }

  render(props, state) {
    const { user } = state;

    // nếu chưa nhận được dữ liệu, hiển thị thông báo đang tải:
    if (!user) return <div>Đang tải...</div>

    // đã có dữ liệu! hiển thị tên người dùng lấy về từ API:
    return (
      <div>
        <h2>Xin chào, {user.username}!</h2>
      </div>
    )
  }
}
```

## Thực hành!

Chúng ta sẽ giữ bài tập này đơn giản: hãy thay đổi đoạn mã mẫu bên phải để log ra mỗi khi `count` thay đổi, thay vì chỉ khi `<App>` được render lần đầu.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn đã học cách sử dụng tác động phụ trong Preact.</p>
</solution>


```js:setup
useRealm(function (realm) {
  var win = realm.globalThis;
  var prevConsoleLog = win.console.log;
  win.console.log = function(m, s) {
    if (/Count is now/.test(m) && s === 1) {
      solutionCtx.setSolved(true);
    }
    return prevConsoleLog.apply(win.console, arguments);
  };

  return function () {
    win.console.log = prevConsoleLog;
  };
}, []);
```


```jsx:repl-initial
import { render } from 'preact';
import { useEffect, useState } from 'preact/hooks';

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('Count is now: ', count)
  }, []);
  // ^^ bắt đầu từ đây!

  return <button onClick={() => setCount(count+1)}>{count}</button>
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render } from 'preact';
import { useEffect, useState } from 'preact/hooks';

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('Count is now: ', count)
  }, [count]);
  // ^^ bắt đầu từ đây!

  return <button onClick={() => setCount(count+1)}>{count}</button>
}

render(<App />, document.getElementById("app"));
```

[phương thức vòng đời]: /guide/v10/components#lifecycle-methods
