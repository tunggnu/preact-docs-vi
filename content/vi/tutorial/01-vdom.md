---
title: Virtual DOM
prev: /tutorial
next: /tutorial/02-events
solvable: true
---

# Virtual DOM

Bạn có thể đã nghe mọi người nhắc đến "Virtual DOM" và tự hỏi:
điều gì làm cho nó "ảo"? "Virtual" DOM khác gì so với DOM thật mà chúng ta sử dụng khi lập trình cho trình duyệt?

Virtual DOM là một mô tả đơn giản về một cấu trúc cây sử dụng các đối tượng:

```js
let vdom = {
  type: 'p',         // một phần tử <p>
  props: {
    class: 'big',    // với class="big"
    children: [
      'Hello World!' // và văn bản "Hello World!"
    ]
  }
}
```

Các thư viện như Preact cung cấp một cách để xây dựng các mô tả này, sau đó có thể được so sánh với cây DOM của trình duyệt. Khi từng phần của cây được so sánh, cây DOM của trình duyệt sẽ được cập nhật để khớp với cấu trúc được mô tả bởi cây Virtual DOM.

Đây là một công cụ hữu ích, vì nó cho phép chúng ta xây dựng giao diện người dùng một cách _khai báo_ thay vì _tường minh_. Thay vì mô tả _cách_ cập nhật DOM khi có các sự kiện như bàn phím hoặc chuột, chúng ta chỉ cần mô tả _DOM nên trông như thế nào_ sau khi nhận được đầu vào đó. Điều này có nghĩa là chúng ta có thể liên tục cung cấp cho Preact các mô tả về cấu trúc cây, và nó sẽ cập nhật cây DOM của trình duyệt để khớp với mỗi mô tả mới – bất kể cấu trúc hiện tại là gì.

Trong chương này, chúng ta sẽ học cách tạo cây Virtual DOM, và cách yêu cầu Preact cập nhật DOM để khớp với các cây đó.

### Tạo cây Virtual DOM

Có một vài cách để tạo cây Virtual DOM:

- `createElement()`: một hàm được cung cấp bởi Preact
- [JSX]: cú pháp giống HTML có thể được biên dịch sang JavaScript
- [HTM]: cú pháp giống HTML bạn có thể viết trực tiếp trong JavaScript

Sẽ hữu ích nếu bắt đầu với cách đơn giản nhất, đó là gọi trực tiếp hàm `createElement()` của Preact:

```jsx
import { createElement, render } from 'preact';

let vdom = createElement(
  'p',              // một phần tử <p>
  { class: 'big' }, // với class="big"
  'Hello World!'    // và văn bản "Hello World!"
);

render(vdom, document.body);
```

Đoạn mã trên tạo ra một "mô tả" Virtual DOM của một phần tử đoạn văn.
Tham số đầu tiên của createElement là tên phần tử HTML.
Tham số thứ hai là "props" của phần tử - một đối tượng chứa các thuộc tính
(hoặc thuộc tính) để gán cho phần tử.
Bất kỳ tham số bổ sung nào là con của phần tử, có thể là chuỗi (như
`'Hello World!'`) hoặc các phần tử Virtual DOM từ các lần gọi `createElement()` khác.

Dòng cuối cùng yêu cầu Preact xây dựng một cây DOM thật khớp với "mô tả" Virtual DOM của chúng ta, và chèn cây DOM đó vào `<body>` của trang web.

### JSX tuyệt vời hơn!

Chúng ta có thể viết lại ví dụ trước bằng [JSX] mà không thay đổi chức năng.
JSX cho phép chúng ta mô tả phần tử đoạn văn bằng cú pháp giống HTML, giúp
giữ cho mã dễ đọc hơn khi mô tả các cây phức tạp hơn. Nhược điểm của JSX là
mã của chúng ta không còn là JavaScript thuần nữa, và phải được biên dịch bởi một công cụ như [Babel]. Các trình biên dịch sẽ chuyển đổi ví dụ JSX dưới đây thành đúng mã `createElement()` mà chúng ta đã thấy ở ví dụ trước.

```jsx
import { createElement, render } from 'preact';

let vdom = <p class="big">Hello World!</p>;

render(vdom, document.body);
```

Trông giống HTML hơn nhiều rồi!

Có một điều cuối cùng cần lưu ý về JSX: mã bên trong một phần tử JSX
(bên trong dấu ngoặc nhọn) là cú pháp đặc biệt và không phải JavaScript. Để sử dụng cú pháp JavaScript như số hoặc biến, bạn cần "nhảy" ra khỏi JSX bằng một `{biểu_thức}` - tương tự như các trường trong template. Ví dụ dưới đây cho thấy hai biểu thức: một để gán `class` thành một chuỗi ngẫu nhiên, và một để tính toán một số.

```jsx
let maybeBig = Math.random() > .5 ? 'big' : 'small';

let vdom = <p class={maybeBig}>Hello {40 + 2}!</p>;
                 // ^---JS---^       ^--JS--^
```

Nếu chúng ta `render(vdom, document.body)`, văn bản "Hello 42!" sẽ được hiển thị.

### Thêm một lần nữa với HTM

[HTM] là một lựa chọn thay thế cho JSX sử dụng template string chuẩn của JavaScript,
loại bỏ nhu cầu về trình biên dịch. Nếu bạn chưa từng gặp template string,
chúng là một loại chuỗi đặc biệt có thể chứa các trường `${biểu_thức}`:

```js
let str = `Số lượng: ${40 + 2} đơn vị`;  // "Số lượng: 42 đơn vị"
```

HTM sử dụng `${biểu_thức}` thay vì cú pháp `{biểu_thức}` của JSX, điều này
giúp phân biệt rõ hơn đâu là phần tử HTM/JSX, đâu là JavaScript thuần:

```js
import { html } from 'htm/preact';

let maybeBig = Math.random() > .5 ? 'big' : 'small';

let vdom = html`<p class=${maybeBig}>Hello ${40 + 2}!</p>`;
                        // ^--JS--^          ^-JS-^
```

Tất cả các ví dụ trên đều cho ra kết quả giống nhau: một cây Virtual DOM có thể
được đưa cho Preact để tạo hoặc cập nhật một cây DOM hiện có.

---

### Ngoại truyện: Component

Chúng ta sẽ tìm hiểu chi tiết hơn về Component ở các chương sau, nhưng
hiện tại điều quan trọng là biết rằng các phần tử HTML như `<p>` chỉ là một trong
_hai_ loại phần tử Virtual DOM. Loại còn lại là Component, là một phần tử Virtual DOM mà type là một hàm thay vì một chuỗi như `p`.

Component là các khối xây dựng của ứng dụng Virtual DOM. Hiện tại, chúng ta sẽ
tạo một component đơn giản bằng cách chuyển JSX vào một hàm:

```jsx
import { createElement } from 'preact';

export default function App() {
    return (
        <p class="big">Hello World!</p>
    )
}

render(<App />, document.getElementById("app"));
```

Khi truyền một component vào `render()`, điều quan trọng là để Preact tự khởi tạo thay vì gọi trực tiếp component của bạn, điều này sẽ gây lỗi không mong muốn:

```jsx
const App = () => <div>foo</div>;

// KHÔNG NÊN: Gọi trực tiếp component nghĩa là chúng sẽ không được tính là một
// VNode và do đó không thể sử dụng các chức năng liên quan đến vnode.
render(App(), rootElement); // LỖI
render(App, rootElement); // LỖI

// NÊN: Truyền component bằng createElement() hoặc JSX cho phép Preact render đúng:
render(createElement(App), rootElement); // thành công
render(<App />, rootElement); // thành công
```

## Thực hành!

Ở phía bên phải của trang này, bạn sẽ thấy mã từ ví dụ trước ở phía trên cùng.
Bên dưới là một hộp với kết quả khi chạy mã đó. Bạn có thể
chỉnh sửa mã và xem các thay đổi ảnh hưởng (hoặc làm hỏng!) kết quả như thế nào.

Để kiểm tra những gì bạn đã học trong chương này, hãy thử làm cho văn bản nổi bật hơn!
Làm cho từ `World` nổi bật bằng cách bọc nó trong thẻ HTML: `<em>` và `</em>`.

Sau đó, làm cho toàn bộ văn bản <span style="color:purple">màu tím</span> bằng cách thêm thuộc tính
`style`. Thuộc tính `style` là đặc biệt, cho phép truyền một đối tượng với
một hoặc nhiều thuộc tính CSS để gán cho phần tử. Để truyền một đối tượng làm giá trị prop, bạn cần dùng `{biểu_thức}`, ví dụ: `style={{ property: 'value' }}`.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Chúng ta đã làm cho mọi thứ xuất hiện trên màn hình. Tiếp theo chúng ta sẽ làm cho chúng tương tác.</p>
</solution>


```js:setup
useResult(function(result) {
  var hasEm = result.output.innerHTML.match(/<em>World\!?<\/em>/gi);
  var p = result.output.querySelector('p');
  var hasColor = p && p.style && p.style.color === 'purple';
  if (hasEm && hasColor) {
    solutionCtx.setSolved(true);
  }
}, []);
```


```jsx:repl-initial
import { createElement, render } from 'preact';

function App() {
  return (
    <p class="big">Hello World!</p>
  );
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { createElement, render } from 'preact';

function App() {
  return (
    <p class="big" style={{ color: 'purple' }}>
      Hello <em>World</em>!
    </p>
  );
}

render(<App />, document.getElementById("app"));
```

[JSX]: https://en.wikipedia.org/wiki/JSX_(JavaScript)
[HTM]: https://github.com/developit/htm
[Babel]: https://babeljs.io
