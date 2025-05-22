---
title: Xử lý lỗi (Error Handling)
prev: /tutorial/08-keys
next: /tutorial/10-links
solvable: true
---

# Xử lý lỗi (Error Handling)

JavaScript là một ngôn ngữ linh hoạt và được thông dịch, điều này có nghĩa là rất dễ (thậm chí là thường xuyên) gặp phải lỗi khi chạy chương trình. Dù là do một tình huống bất ngờ hay do lỗi trong mã mà chúng ta viết, việc có thể theo dõi lỗi và triển khai một số hình thức phục hồi hoặc xử lý lỗi hợp lý là rất quan trọng.

Trong Preact, cách chúng ta làm điều này là bắt lỗi và lưu chúng vào state. Điều này cho phép một component chặn một lần render bị lỗi hoặc không mong muốn và chuyển sang render một nội dung khác như một phương án dự phòng.

## Chuyển lỗi thành state

Có hai API để bắt lỗi và chuyển chúng thành state:
`componentDidCatch` và `getDerivedStateFromError`. Chúng khá giống nhau về chức năng, và đều là các phương thức bạn có thể triển khai trên một component lớp:

**componentDidCatch** nhận một đối số là `Error`, và có thể quyết định làm gì với lỗi đó tùy từng trường hợp. Nó có thể gọi `this.setState()` để render một cây dự phòng hoặc thay thế, điều này sẽ "bắt" lỗi và đánh dấu nó đã được xử lý. Hoặc, phương thức này cũng có thể chỉ log lỗi ở đâu đó và để nó tiếp tục không được xử lý (dẫn đến crash).

**getDerivedStateFromError** là một phương thức tĩnh nhận vào một `Error`, và trả về một đối tượng cập nhật state, đối tượng này sẽ được áp dụng cho component thông qua `setState()`. Vì phương thức này luôn tạo ra một thay đổi state dẫn đến việc component được render lại, nên nó luôn đánh dấu lỗi là đã được xử lý.

Ví dụ sau đây cho thấy cách sử dụng một trong hai phương thức để bắt lỗi và hiển thị một thông báo lỗi thân thiện thay vì bị crash:

```jsx
import { Component } from 'preact'

class ErrorBoundary extends Component {
  state = { error: null }

  static getDerivedStateFromError(error) {
    return { error: error.message }
  }

  componentDidCatch(error) {
    console.error(error)
    this.setState({ error: error.message })
  }

  render() {
    if (this.state.error) {
      return <p>Ôi không! Đã gặp lỗi: {this.state.error}</p>
    }
    return this.props.children
  }
}
```

Component trên là một ví dụ khá phổ biến về cách xử lý lỗi được triển khai trong các ứng dụng Preact, thường được gọi là _Error Boundary_ (biên lỗi).

## Lồng nhau và lan truyền lỗi

Các lỗi gặp phải khi Preact render cây Virtual DOM của bạn sẽ "lan truyền lên trên", giống như các sự kiện DOM. Bắt đầu từ component gặp lỗi, mỗi component cha trong cây sẽ có cơ hội xử lý lỗi đó.

Do đó, các Error Boundary có thể được lồng nhau nếu được triển khai bằng `componentDidCatch`. Khi phương thức `componentDidCatch()` của một component _không_ gọi `setState()`, lỗi sẽ tiếp tục lan lên cây Virtual DOM cho đến khi gặp một component có phương thức `componentDidCatch` _có_ gọi `setState()`.

## Thực hành!

Để kiểm tra kiến thức xử lý lỗi, hãy thêm xử lý lỗi cho một component App đơn giản. Một trong các component nằm sâu bên trong App có thể ném ra lỗi trong một số trường hợp, và chúng ta muốn bắt lỗi này để có thể hiển thị một thông báo thân thiện cho người dùng biết rằng đã gặp lỗi không mong muốn.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn đã học cách xử lý lỗi trong mã Preact!</p>
</solution>

```js:setup
useResult(function(result) {
  var options = require('preact').options;

  var oe = options.__e;
  options.__e = function(error, s) {
    if (/objects are not valid/gi.test(error)) {
      throw Error('Có vẻ bạn đang cố render một đối tượng Error trực tiếp: hãy thử lưu `error.message` thay vì chính đối tượng `error`.');
    }
    oe.apply(this, arguments);
    setTimeout(function() {
      if (result.output.textContent.match(/error/i)) {
        solutionCtx.setSolved(true);
      }
    }, 10);
  };

  return function () {
    options.__e = oe;
  };
}, []);
```

```jsx:repl-initial
import { render, Component } from 'preact';
import { useState } from 'preact/hooks';

function Clicker() {
  const [clicked, setClicked] = useState(false);

  if (clicked) {
    throw new Error('Tôi đang gây lỗi');
  }

  return <button onClick={() => setClicked(true)}>Nhấn tôi</button>;
}

class App extends Component {
  state = { error: null };

  render() {
    return <Clicker />;
  }
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render, Component } from 'preact';
import { useState } from 'preact/hooks';

function Clicker() {
  const [clicked, setClicked] = useState(false);

  if (clicked) {
    throw new Error('Tôi đang gây lỗi');
  }

  return <button onClick={() => setClicked(true)}>Nhấn tôi</button>;
}

class App extends Component {
  state = { error: null };

  componentDidCatch(error) {
    this.setState({ error: error.message });
  }

  render() {
    const { error } = this.state;
    if (error) {
      return <p>Ôi không! Đã có lỗi: {error}</p>
    }
    return <Clicker />;
  }
}

render(<App />, document.getElementById("app"));
```
