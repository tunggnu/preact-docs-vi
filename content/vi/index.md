---
title: Preact
description: 'Giải pháp thay thế React nhanh chỉ 3kB với cùng API hiện đại.'
---

<jumbotron>
    <h1>
        <logo height="1.5em" title="Preact" text="true" inverted="true">Preact</logo>
    </h1>
    <p class="tagline">Giải pháp thay thế React nhanh chỉ 3kB với cùng API hiện đại</p>
    <p class="intro-buttons">
        <a href="/guide/v10/getting-started" class="btn primary">Bắt đầu</a>
        <a href="/guide/v10/switching-to-preact" class="btn secondary">Chuyển sang Preact</a>
    </p>
</jumbotron>

```jsx
function Counter() {
  const [value, setValue] = useState(0);

  return (
    <>
      <div>Bộ đếm: {value}</div>
      <button onClick={() => setValue(value + 1)}>Tăng</button>
      <button onClick={() => setValue(value - 1)}>Giảm</button>
    </>
  )
}
```

<section class="sponsors">
  <p>Tự hào được <a href="https://opencollective.com/preact">tài trợ bởi:</a></p>
  <sponsors></sponsors>
</section>

<section class="home-top">
    <h2>Một kiểu thư viện khác biệt</h2>
</section>

<section class="home-section">
  <img src="/home/metal.svg" alt="kim loại" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Gần gũi hơn với DOM</h3>
    <p>
      Preact cung cấp lớp trừu tượng Virtual DOM mỏng nhất có thể trên nền DOM.
      Nó xây dựng dựa trên các tính năng nền tảng ổn định, đăng ký các trình xử lý sự kiện thực sự và hoạt động tốt với các thư viện khác.
    </p>
    <p>
      Preact có thể được sử dụng trực tiếp trên trình duyệt mà không cần bước chuyển đổi nào.
    </p>
  </div>
</section>

<section class="home-section">
  <img src="/home/size.svg" alt="kích thước" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Kích thước nhỏ</h3>
    <p>
      Hầu hết các framework UI đều đủ lớn để chiếm phần lớn dung lượng JavaScript của ứng dụng.
      Preact thì khác: nó nhỏ đến mức <em>mã của bạn</em> mới là phần lớn nhất của ứng dụng.
    </p>
    <p>
      Điều đó có nghĩa là ít JavaScript hơn để tải xuống, phân tích và thực thi – dành nhiều thời gian hơn cho mã của bạn, để bạn có thể xây dựng trải nghiệm theo ý mình mà không phải vật lộn để kiểm soát framework.
    </p>
  </div>
</section>

<section class="home-section">
  <img src="/home/performance.svg" alt="hiệu năng" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Hiệu năng lớn</h3>
    <p>
      Preact rất nhanh, không chỉ vì kích thước nhỏ. Đây là một trong những thư viện Virtual DOM nhanh nhất hiện nay, nhờ vào thuật toán diff đơn giản và dễ dự đoán.
    </p>
    <p>
      Chúng tôi tự động gom nhóm các cập nhật và tối ưu Preact đến mức tối đa về hiệu năng. Chúng tôi hợp tác chặt chẽ với các kỹ sư trình duyệt để khai thác tối đa hiệu suất từ Preact.
    </p>
  </div>
</section>

<section class="home-section">
  <img src="/home/portable.svg" alt="di động" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Di động &amp; nhúng dễ dàng</h3>
    <p>
      Dấu chân nhỏ bé của Preact cho phép bạn mang mô hình Component Virtual DOM mạnh mẽ đến những nơi mà trước đây không thể.
    </p>
    <p>
      Sử dụng Preact để xây dựng một phần của ứng dụng mà không cần tích hợp phức tạp. Nhúng Preact vào một widget và áp dụng các công cụ, kỹ thuật như khi xây dựng một ứng dụng đầy đủ.
    </p>
  </div>
</section>

<section class="home-section">
  <img src="/home/productive.svg" alt="năng suất" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Năng suất tức thì</h3>
    <p>
      Nhẹ nhàng sẽ thú vị hơn nhiều khi bạn không phải đánh đổi năng suất để đạt được điều đó. Preact giúp bạn làm việc hiệu quả ngay lập tức. Nó thậm chí còn có một số tính năng bổ sung:
    </p>
    <ul>
      <li><code>props</code>, <code>state</code> và <code>context</code> được truyền vào <code>render()</code></li>
      <li>Sử dụng các thuộc tính HTML chuẩn như <code>class</code> và <code>for</code></li>
    </ul>
  </div>
</section>

<section class="home-section">
  <img src="/home/compatible.svg" alt="tương thích" loading="lazy" decoding="async" width="54" height="54">

  <div>
    <h3>Tương thích hệ sinh thái</h3>
    <p>
      Các Component Virtual DOM giúp dễ dàng chia sẻ những thứ có thể tái sử dụng – từ nút bấm đến các provider dữ liệu.
      Thiết kế của Preact cho phép bạn sử dụng liền mạch hàng ngàn Component có sẵn trong hệ sinh thái React.
    </p>
    <p>
      Chỉ cần thêm một alias <a href="/guide/v10/switching-to-preact#how-to-alias-preact-compat">preact/compat</a> đơn giản vào bundler của bạn để có một lớp tương thích,
      cho phép sử dụng ngay cả những component React phức tạp nhất trong ứng dụng của bạn.
    </p>
  </div>
</section>

<section class="home-top">
    <h2>Xem thử ngay!</h2>
</section>

<section class="home-split">
    <div>
        <h3>Danh sách việc cần làm</h3>
        <pre><code class="language-jsx">
// --repl
export default class TodoList extends Component {
    state = { todos: [], text: '' };
    setText = e =&gt; {
        this.setState({ text: e.currentTarget.value });
    };
    addTodo = () =&gt; {
        let { todos, text } = this.state;
        todos = todos.concat({ text });
        this.setState({ todos, text: '' });
    };
    render({ }, { todos, text }) {
        return (
            &lt;form onSubmit={this.addTodo} action="javascript:"&gt;
                &lt;label&gt;
                  &lt;span&gt;Thêm việc&lt;/span&gt;
                  &lt;input value={text} onInput={this.setText} /&gt;
                &lt;/label&gt;
                &lt;button type="submit"&gt;Thêm&lt;/button&gt;
                &lt;ul&gt;
                    { todos.map( todo =&gt; (
                        &lt;li&gt;{todo.text}&lt;/li&gt;
                    )) }
                &lt;/ul&gt;
            &lt;/form&gt;
        );
    }
}
// --repl-after
render(&lt;TodoList /&gt;, document.getElementById("app"));
        </code></pre>
    </div>
    <div>
        <h3>Ví dụ đang chạy</h3>
        <pre repl="false"><code class="language-jsx">
import TodoList from './todo-list';<br>
render(&lt;TodoList /&gt;, document.body);
        </code></pre>
        <div class="home-demo">
            <todo-list></todo-list>
        </div>
    </div>
</section>

<section class="home-split">
    <div>
        <h3>Lấy số sao GitHub</h3>
        <pre><code class="language-jsx">
// --repl
export default class Stars extends Component {
    async componentDidMount() {
        let stars = await githubStars(this.props.repo);
        this.setState({ stars });
    }
    render({ repo }, { stars=0 }) {
        let url = `https://github.com/${repo}`;
        return (
            &lt;a href={url} class="stars"&gt;
                ⭐️ {stars} Sao
            &lt;/a&gt;
        );
    }
}
// --repl-after
render(&lt;Stars repo="preactjs/preact" /&gt;, document.getElementById("app"));
        </code></pre>
    </div>
    <div>
        <h3>Ví dụ đang chạy</h3>
        <pre repl="false"><code class="language-jsx">
import Stars from './stars';<br>
render(
    &lt;Stars repo="preactjs/preact" /&gt;,
    document.body
);
        </code></pre>
        <div class="home-demo">
            <github-stars simple="true" user="preactjs" repo="preact"></github-stars>
        </div>
    </div>
</section>

<section class="home-top">
    <h2>Sẵn sàng bắt đầu?</h2>
</section>

<section style="text-align:center;">
    <p>
        Chúng tôi có các hướng dẫn riêng dựa trên việc bạn đã có kinh nghiệm với React hay chưa.
        <br>
        Hãy chọn hướng dẫn phù hợp nhất với bạn!
    </p>
    <p>
        <a href="/guide/v10/getting-started" class="btn primary">Bắt đầu</a>
        <a href="/guide/v10/switching-to-preact" class="btn secondary">Chuyển sang Preact</a>
    </p>
</section>
