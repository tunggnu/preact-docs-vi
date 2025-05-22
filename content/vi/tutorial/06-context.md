---
title: Ngữ cảnh (Context)
prev: /tutorial/05-refs
next: /tutorial/07-side-effects
solvable: true
---

# Ngữ cảnh (Context)

Khi một ứng dụng phát triển lớn hơn, cây Virtual DOM của nó thường trở nên lồng sâu và bao gồm nhiều thành phần khác nhau. Các thành phần ở nhiều vị trí khác nhau trong cây đôi khi cần truy cập dữ liệu chung – thường là các phần của trạng thái ứng dụng như xác thực, thông tin hồ sơ người dùng, bộ nhớ đệm, lưu trữ, v.v. Mặc dù có thể truyền tất cả thông tin đó xuống qua cây dưới dạng props của thành phần, nhưng làm như vậy nghĩa là mọi thành phần đều phải biết về tất cả trạng thái đó – ngay cả khi chúng chỉ chuyển tiếp nó xuống dưới cây.

Context là một tính năng cho phép chúng ta truyền giá trị xuống qua cây _một cách tự động_, mà các thành phần không cần phải biết gì cả. Điều này được thực hiện bằng cách tiếp cận Provider/Consumer:


- `<Provider>` thiết lập giá trị context trong một <abbr title="Cây Virtual DOM nằm trong <Provider>...</Provider>, bao gồm tất cả các con">cây con</abbr>
- `<Consumer>` nhận giá trị context được thiết lập bởi Provider cha gần nhất


Để bắt đầu, hãy xem một ví dụ đơn giản chỉ với một thành phần. Trong trường hợp này, chúng ta cung cấp một giá trị context "Username" _và_ tiêu thụ giá trị đó:

```jsx
import { createContext } from 'preact'

const Username = createContext()

export default function App() {
  return (
    // cung cấp giá trị username cho cây con:
    <Username.Provider value="Bob">
      <div>
        <p>
          <Username.Consumer>
            {username => (
              // truy cập username hiện tại từ context:
              <span>{username}</span>
            )}
          </Username.Consumer>
        </p>
      </div>
    </Username.Provider>
  )
}
```

Trong thực tế, context hiếm khi được cung cấp và tiêu thụ trong cùng một thành phần – trạng thái thành phần thường là giải pháp tốt nhất cho trường hợp đó.

### Sử dụng với hooks

API `<Consumer>` của context là đủ cho hầu hết các trường hợp, nhưng có thể hơi rườm rà vì nó dựa vào các hàm lồng nhau để lấy phạm vi. Thành phần hàm có thể chọn sử dụng hook `useContext()` của Preact, trả về giá trị của một `Context` tại vị trí thành phần đó trong cây Virtual DOM.

Dưới đây là ví dụ trước, lần này được tách thành hai thành phần và sử dụng `useContext()` để lấy giá trị hiện tại của context:

```jsx
import { createContext } from 'preact'
import { useContext } from 'preact/hooks'

const Username = createContext()

export default function App() {
  return (
    <Username.Provider value="Bob">
      <div>
        <p>
          <User />
        </p>
      </div>
    </Username.Provider>
  )
}

function User() {
  // truy cập username hiện tại từ context:
  const username = useContext(Username) // "Bob"
  return <span>{username}</span>
}
```

Nếu bạn tưởng tượng trường hợp `User` cần truy cập giá trị của nhiều Context, API `useContext()` đơn giản sẽ dễ theo dõi hơn nhiều.

### Sử dụng thực tế

Một ví dụ thực tế hơn về context là lưu trạng thái xác thực của ứng dụng (liệu người dùng đã đăng nhập hay chưa).

Để làm điều này, chúng ta có thể tạo một context để lưu thông tin, gọi là `AuthContext`. Giá trị cho AuthContext sẽ là một đối tượng với thuộc tính `user` chứa người dùng đã đăng nhập, cùng với phương thức `setUser` để thay đổi trạng thái đó.

```jsx
import { createContext } from 'preact'
import { useState, useMemo, useContext } from 'preact/hooks'

const AuthContext = createContext()

export default function App() {
  const [user, setUser] = useState(null)

  const auth = useMemo(() => {
    return { user, setUser }
  }, [user])

  return (
    <AuthContext.Provider value={auth}>
      <div class="app">
        {auth.user && <p>Chào mừng {auth.user.name}!</p>}
        <Login />
      </div>
    </AuthContext.Provider>
  )
}

function Login() {
  const { user, setUser } = useContext(AuthContext)

  if (user) return (
    <div class="logged-in">
      Đã đăng nhập với tên {user.name}.
      <button onClick={() => setUser(null)}>
        Đăng xuất
      </button>
    </div>
  )

  return (
    <div class="logged-out">
      <button onClick={() => setUser({ name: 'Bob' })}>
        Đăng nhập
      </button>
    </div>
  )
}
```

### Context lồng nhau

Context có một siêu năng lực ẩn rất hữu ích trong các ứng dụng lớn: các provider context có thể được lồng nhau để "ghi đè" giá trị của chúng trong một cây con Virtual DOM. Hãy tưởng tượng một ứng dụng email trên web, nơi các phần khác nhau của giao diện người dùng được hiển thị dựa trên đường dẫn URL:

> - `/inbox`: hiển thị hộp thư đến
> - `/inbox/compose`: hiển thị hộp thư đến và một tin nhắn mới
> - `/settings`: hiển thị cài đặt
> - `/settings/forwarding`: hiển thị cài đặt chuyển tiếp

Chúng ta có thể tạo một thành phần `<Route path="..">` để render một cây Virtual DOM chỉ khi đường dẫn hiện tại khớp với một đoạn đường dẫn nhất định. Để đơn giản hóa việc định nghĩa các Route lồng nhau, mỗi Route khớp sẽ ghi đè giá trị context "current path" trong cây con của nó để loại bỏ phần đường dẫn đã khớp.

```jsx
import { createContext } from 'preact'
import { useContext } from 'preact/hooks'

const Path = createContext(location.pathname)

function Route(props) {
  const path = useContext(Path) // đường dẫn hiện tại
  const isMatch = path.startsWith(props.path)
  const innerPath = path.substring(props.path.length)
  return isMatch && (
    <Path.Provider value={innerPath}>
      {props.children}
    </Path.Provider>
  )
}
```

Bây giờ chúng ta có thể sử dụng thành phần `Route` mới này để định nghĩa giao diện ứng dụng email. Lưu ý rằng thành phần `Inbox` không cần biết đường dẫn của chính nó để định nghĩa `<Route path="..">` cho các con của nó:

```jsx
export default function App() {
  return (
    <div class="app">
      <Route path="/inbox">
        <Inbox />
      </Route>
      <Route path="/settings">
        <Settings />
      </Route>
    </div>
  )
}

function Inbox() {
  return (
    <div class="inbox">
      <div class="messages"> ... </div>
      <Route path="/compose">
        <Compose />
      </Route>
    </div>
  )
}

function Settings() {
  return (
    <div class="settings">
      <h1>Cài đặt</h1>
      <Route path="/forwarding">
        <Forwarding />
      </Route>
    </div>
  )
}
```

### Giá trị mặc định của context

Context lồng nhau là một tính năng mạnh mẽ, và chúng ta thường sử dụng nó mà không nhận ra. Ví dụ, trong ví dụ minh họa đầu tiên của chương này, chúng ta dùng `<Provider value="Bob">` để định nghĩa giá trị context `Username` trong cây.

Tuy nhiên, điều này thực ra đã ghi đè giá trị mặc định của context `Username`. Mọi context đều có một giá trị mặc định, là giá trị được truyền làm đối số đầu tiên cho `createContext()`. Trong ví dụ, chúng ta không truyền đối số nào cho `createContext`, nên giá trị mặc định là `undefined`.

Dưới đây là ví dụ đầu tiên nếu dùng giá trị mặc định của context thay vì Provider:

```jsx
import { createContext } from 'preact'
import { useContext } from 'preact/hooks'

const Username = createContext('Bob')

export default function App() {
  const username = useContext(Username) // trả về "Bob"

  return <span>{username}</span>
}
```

## Thực hành!

Là một bài tập, hãy tạo một phiên bản _đồng bộ_ của bộ đếm mà chúng ta đã tạo ở chương trước. Để làm điều này, bạn sẽ muốn sử dụng kỹ thuật `useMemo()` từ ví dụ xác thực trong chương này. Ngoài ra, bạn cũng có thể định nghĩa _hai_ context: một để chia sẻ giá trị `count`, và một để chia sẻ hàm `increment` để cập nhật giá trị đó.

<solution>
  <h4>🎉 Chúc mừng!</h4>
  <p>Bạn đã học cách sử dụng context trong Preact.</p>
</solution>


```js:setup
var output = useRef();

function getCounts() {
  var counts = [];
  var text = output.current.innerText;
  var r = /Count:\s*([\w.-]*)/gi;
  while (t = r.exec(text)) {
    var num = Number(t[1]);
    counts.push(isNaN(num) ? t[1] : num);
  }
  return counts;
}

useResult(function (result) {
  output.current = result.output;

  if (getCounts().length !== 3) {
    console.warn('It looks like you haven\'t initialized the `count` value to 0.');
  }

  var timer;
  var count = 0;
  var options = require('preact').options;

  var oe = options.event;
  options.event = function(e) {
    if (e.currentTarget.localName !== 'button') return;
    clearTimeout(timer);
    timer = setTimeout(function() {
      var counts = getCounts();
      if (counts.length !== 3) {
        return console.warn('We seem to be missing one of the counters.');
      }
      if (counts[0] !== counts[2] || counts[0] !== counts[1]) {
        return console.warn('It looks like the counters aren\'t in sync.');
      }
      var solved = counts[0] === ++count;
      store.setState({ solved: solved });
    }, 10);
    if (oe) return oe.apply(this, arguments);
  }

  return function () {
    options.event = oe;
  };
}, []);
```


```jsx:repl-initial
import { render, createContext } from 'preact';
import { useState, useContext, useMemo } from 'preact/hooks';

const CounterContext = createContext(null);

function Counter() {
  return (
    <div style={{ background: '#eee', padding: '10px' }}>
      <p>Count: {'MISSING'}</p>
      <button>Add</button>
    </div>
  );
}

function App() {
  const [count, setCount] = useState(0);

  return (
    <div style={{ display: 'flex', gap: '20px' }}>
      <Counter />
      <Counter />
      <Counter />
    </div>
  )
}

render(<App />, document.getElementById("app"));
```

```jsx:repl-final
import { render, createContext } from 'preact';
import { useState, useContext, useMemo } from 'preact/hooks';

const CounterContext = createContext(null);

function Counter() {
  const { count, increment } = useContext(CounterContext);

  return (
    <div style={{ background: '#eee', padding: '10px' }}>
      <p>Count: {count}</p>
      <button onClick={increment}>Add</button>
    </div>
  );
}

function App() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
  }

  const counter = useMemo(() => {
    return { count, increment };
  }, [count]);

  return (
    <CounterContext.Provider value={counter}>
      <div style={{ display: 'flex', gap: '20px' }}>
        <Counter />
        <Counter />
        <Counter />
      </div>
    </CounterContext.Provider>
  )
}

render(<App />, document.getElementById("app"));
```
