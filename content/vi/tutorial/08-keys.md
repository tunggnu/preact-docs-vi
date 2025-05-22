---
title: Keys (Khóa)
prev: /tutorial/07-side-effects
next: /tutorial/09-error-handling
solvable: true
---

# Keys (Khóa)

Ở chương đầu tiên, chúng ta đã thấy cách Preact sử dụng Virtual DOM để tính toán những gì đã thay đổi giữa hai cây được mô tả bởi JSX của chúng ta, sau đó áp dụng các thay đổi đó lên HTML DOM để cập nhật trang. Điều này hoạt động tốt trong hầu hết các trường hợp, nhưng đôi khi yêu cầu Preact phải "đoán" hình dạng của cây đã thay đổi như thế nào giữa hai lần render.

Tình huống phổ biến nhất mà dự đoán của Preact có thể khác với ý định của chúng ta là khi so sánh các danh sách. Hãy xem xét một component danh sách việc cần làm đơn giản:

```jsx
export default function TodoList() {
  const [todos, setTodos] = useState(['wake up', 'make bed'])

  function wakeUp() {
    setTodos(['make bed'])
  }

  return (
    <div>
      <ul>
        {todos.map(todo => (
          <li>{todo}</li>
        ))}
      </ul>
      <button onClick={wakeUp}>Tôi đã thức dậy!</button>
    </div>
  )
}
```

Lần đầu tiên component này được render, hai phần tử `<li>` sẽ được vẽ ra. Sau khi nhấn nút __"Tôi đã thức dậy!"__, mảng state `todos` của chúng ta được cập nhật chỉ còn mục thứ hai, `"make bed"`.

Đây là những gì Preact "thấy" ở lần render đầu tiên và lần render thứ hai:

<table><thead><tr>
  <th>Lần render đầu tiên</th>
  <th>Lần render thứ hai</th>
</tr></thead><tbody><tr><td>

```jsx
<div>
  <ul>
    <li>wake up</li>
    <li>make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td><td>

```jsx
<div>
  <ul>
    <li>make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td></tr></tbody></table>

Bạn có nhận ra vấn đề không? Mặc dù với chúng ta thì rõ ràng phần tử danh sách đầu tiên ("wake up") đã bị xóa, nhưng Preact không biết điều đó. Tất cả những gì Preact thấy là trước đó có hai mục, bây giờ chỉ còn một. Khi áp dụng cập nhật này, thực tế nó sẽ xóa mục thứ hai (`<li>make bed</li>`), sau đó cập nhật văn bản của mục đầu tiên từ `wake up` thành `make bed`.

Kết quả về mặt kỹ thuật là đúng – chỉ còn một mục với nội dung "make bed" – nhưng cách mà chúng ta đạt được kết quả đó là không tối ưu. Hãy tưởng tượng nếu có 1000 mục trong danh sách và chúng ta xóa mục đầu tiên: thay vì chỉ xóa một `<li>`, Preact sẽ cập nhật văn bản của 999 mục còn lại và xóa mục cuối cùng.

## __key__ cho việc render danh sách

Trong các tình huống như ví dụ trên, các mục đang thay đổi _thứ tự_. Chúng ta cần một cách để giúp Preact biết mục nào là mục nào, để nó có thể phát hiện khi nào mỗi mục được thêm, xóa hoặc thay thế. Để làm điều này, chúng ta có thể thêm prop `key` cho mỗi mục.

Prop `key` là một định danh cho một phần tử nhất định. Thay vì so sánh _thứ tự_ các phần tử giữa hai cây, các phần tử có prop `key` sẽ được so sánh bằng cách tìm phần tử trước đó có cùng giá trị prop `key`. Một `key` có thể là bất kỳ kiểu giá trị nào, miễn là nó "ổn định" giữa các lần render: các lần render lặp lại của cùng một mục phải có giá trị prop `key` giống hệt nhau.

Hãy thêm key vào ví dụ trước. Vì danh sách việc cần làm của chúng ta là một mảng các chuỗi đơn giản không thay đổi, chúng ta có thể dùng chính các chuỗi đó làm key:

```jsx
export default function TodoList() {
  const [todos, setTodos] = useState(['wake up', 'make bed'])

  function wakeUp() {
    setTodos(['make bed'])
  }

  return (
    <div>
      <ul>
        {todos.map(todo => (
          <li key={todo}>{todo}</li>
          //  ^^^^^^^^^^ thêm prop key
        ))}
      </ul>
      <button onClick={wakeUp}>Tôi đã thức dậy!</button>
    </div>
  )
}
```

Lần đầu tiên chúng ta render phiên bản mới của component `<TodoList>`, hai mục `<li>` sẽ được vẽ ra. Khi nhấn nút "Tôi đã thức dậy!", mảng state `todos` của chúng ta được cập nhật chỉ còn mục thứ hai, `"make bed"`.

Đây là những gì Preact thấy bây giờ khi chúng ta đã thêm `key` cho các mục danh sách:

<table><thead><tr>
  <th>Lần render đầu tiên</th>
  <th>Lần render thứ hai</th>
</tr></thead><tbody><tr><td>

```jsx
<div>
  <ul>
    <li key="wake up">wake up</li>
    <li key="make bed">make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td><td>

```jsx
<div>
  <ul>
    <li key="make bed">make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td></tr></tbody></table>

Lần này, Preact có thể thấy rằng mục đầu tiên đã bị xóa, vì cây thứ hai không còn mục nào với `key="wake up"`. Nó sẽ xóa mục đầu tiên, và giữ nguyên mục thứ hai.

## Khi __không nên__ dùng key

Một trong những lỗi phổ biến nhất mà lập trình viên gặp phải với key là vô tình chọn key _không ổn định_ giữa các lần render. Trong ví dụ của chúng ta, hãy tưởng tượng nếu chúng ta dùng đối số index từ `map()` làm giá trị `key` thay vì chuỗi `item`:

`items.map((item, index) => <li key={index}>{item}</li>`

Điều này sẽ khiến Preact thấy các cây sau ở lần render đầu tiên và lần render thứ hai:

<table><thead><tr>
  <th>Lần render đầu tiên</th>
  <th>Lần render thứ hai</th>
</tr></thead><tbody><tr><td>

```jsx
<div>
  <ul>
    <li key={0}>wake up</li>
    <li key={1}>make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td><td>

```jsx
<div>
  <ul>
    <li key={0}>make bed</li>
  </ul>
  <button>Tôi đã thức dậy!</button>
</div>
```

</td></tr></tbody></table>

Vấn đề là `index` thực ra không xác định một _**giá trị**_ trong danh sách của chúng ta, nó xác định một _**vị trí**_. Render như vậy thực ra _bắt buộc_ Preact phải khớp các mục theo thứ tự, giống như nó sẽ làm nếu không có key nào. Việc dùng index làm key thậm chí có thể gây ra kết quả tốn kém hoặc sai khi áp dụng cho các mục danh sách có kiểu khác nhau, vì key không thể khớp các phần tử có kiểu khác nhau.

> 🚙 __Ví dụ minh họa!__ Hãy tưởng tượng bạn gửi xe ở bãi đỗ xe có nhân viên trông xe.
>
> Khi bạn quay lại lấy xe, bạn nói với nhân viên rằng bạn lái một chiếc SUV màu xám. Thật không may, hơn một nửa số xe ở bãi là SUV màu xám, và bạn nhận nhầm xe của người khác. Chủ xe SUV màu xám tiếp theo cũng nhận nhầm xe, và cứ thế tiếp tục.
>
> Nếu thay vào đó bạn nói với nhân viên rằng bạn lái chiếc SUV màu xám với biển số "PR3ACT", bạn chắc chắn sẽ nhận lại đúng xe của mình.

<!--
> 🍫 **Ví dụ sô-cô-la:** Hãy tưởng tượng một người bạn cầm một hộp sô-cô-la và hỏi bạn muốn thử cái nào. Bạn để ý đến viên sô-cô-la vị bạc hà.
>
> Nếu bạn trả lời "viên thứ tư", có nguy cơ các viên sô-cô-la đã bị xáo trộn hoặc đổi chỗ, và bạn có thể nhận nhầm viên. (ôi trời!)
>
> Nếu bạn trả lời "viên bạc hà", sẽ rõ ràng bạn muốn thử viên nào, bất kể thứ tự ra sao.
-->

Nguyên tắc chung là không bao giờ dùng index của mảng hoặc vòng lặp làm `key`. Hãy dùng chính giá trị của mục trong danh sách, hoặc tạo một ID duy nhất cho từng mục và dùng nó:

```jsx
const todos = [
  { id: 1, text: 'wake up' },
  { id: 2, text: 'make bed' }
]

export default function ToDos() {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
        </li>
      ))}
    </ul>
  )
}
```

Hãy nhớ: nếu bạn thực sự không thể tìm được một key ổn định, tốt hơn là bỏ qua prop `key` hoàn toàn thay vì dùng index làm key.

## Thực hành!

Ở bài tập chương này, chúng ta sẽ kết hợp những gì đã học về key với kiến thức về tác động phụ từ chương trước.

Hãy dùng một effect để gọi hàm `getTodos()` đã cho sau khi `<TodoList>` được render lần đầu. Lưu ý rằng hàm này trả về một Promise, bạn có thể lấy giá trị bằng cách gọi `.then(value => { })`. Khi đã có giá trị Promise, hãy lưu nó vào hook useState `todos` bằng cách gọi phương thức `setTodos` liên quan.

Cuối cùng, cập nhật JSX để render từng mục từ `todos` thành một `<li>` chứa giá trị thuộc tính `.text` của mục todo đó.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>
    Bạn đã hoàn thành chương áp chót,
    và đã học cách render danh sách một cách hiệu quả.
  </p>
</solution>

```js:setup
useRealm(function (realm) {
  // the app element
  var out = realm.globalThis.document.body.firstElementChild;
  var options = require('preact').options;

  var oldRender = options.__r;
  var timer;
  options.__r = function(vnode) {
    timer = setTimeout(check, 10);
    if (oldRender) oldRender(vnode);
  };

  function check() {
    timer = null;
    var c = out.firstElementChild.children;
    if (
      c.length === 2 &&
      /learn preact/i.test(c[0].textContent) &&
      /make an awesome app/i.test(c[1].textContent)
    ) {
      solutionCtx.setSolved(true);
    }
  }

  return () => {
    options.__r = oldRender;
  };
});
```

```jsx:repl-initial
import { render } from 'preact';
import { useState, useEffect } from 'preact/hooks';

const wait = ms => new Promise(r => setTimeout(r, ms))

const getTodos = async () => {
  await wait(500);
  return [
    { id: 1, text: 'learn Preact', done: false },
    { id: 2, text: 'make an awesome app', done: false },
  ]
}

function TodoList() {
  const [todos, setTodos] = useState([])

  return (
    <ul>
    </ul>
  )
}

render(<TodoList />, document.getElementById("app"));
```

```jsx:repl-final
import { render } from 'preact';
import { useState, useEffect } from 'preact/hooks';

const wait = ms => new Promise(r => setTimeout(r, ms))

const getTodos = async () => {
  await wait(500);
  return [
    { id: 1, text: 'learn Preact', done: false },
    { id: 2, text: 'make an awesome app', done: false },
  ]
}

function TodoList() {
  const [todos, setTodos] = useState([])

  useEffect(() => {
    getTodos().then(todos => {
      setTodos(todos)
    })
  }, [])

  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
        </li>
      ))}
    </ul>
  )
}

render(<TodoList />, document.getElementById("app"));
```
