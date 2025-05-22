---
title: Chúc mừng!
prev: /tutorial/09-error-handling
solvable: false
---

# Chúc mừng!

Bạn đã hoàn thành hướng dẫn Preact!

Hãy thoải mái thử nghiệm thêm với mã ví dụ.

## Bước tiếp theo

- [Tìm hiểu thêm về class component](/guide/v10/components)
- [Tìm hiểu thêm về hooks](/guide/v10/hooks)
- [Tạo dự án của riêng bạn](https://vite.new/preact)

> **Chúng tôi muốn nhận phản hồi từ bạn!**
>
> Bạn có cảm thấy mình đã học được Preact không? Bạn có gặp khó khăn ở đâu không?
>
> Mọi phản hồi đều được hoan nghênh tại [thảo luận này](https://github.com/preactjs/preact-www/discussions/815).

```jsx:repl-initial
import { render } from 'preact';
import { useState, useEffect } from 'preact/hooks'

const getTodos = async () => {
  try {
    return JSON.parse(localStorage.todos)
  } catch (e) {
    return [
      { id: 1, text: 'learn Preact', done: true },
      { id: 2, text: 'make an awesome app', done: false },
    ]
  }
}

function ToDos() {
  const [todos, setTodos] = useState([])

  useEffect(() => {
    getTodos().then(todos => {
      setTodos(todos)
    })
  }, [])

  // every time todos changes...
  useEffect(() => {
    // ...save the list to localStorage:
    localStorage.todos = JSON.stringify(todos)
    // (try reloading the page to see saved todos!)
  }, [todos])

  function toggle(id) {
    setTodos(todos => {
      return todos.map(todo => {
        // replace the matching todo item with a version where done is toggled
        if (todo.id === id) {
          todo = { ...todo, done: !todo.done }
        }
        return todo
      })
    })
  }

  function addTodo(e) {
    e.preventDefault()
    const form = e.target
    const text = form.todo.value
    // pass a callback to the `todos` state setter to update its value in-place:
    setTodos(todos => {
      const id = todos.length + 1
      const newTodo = { id, text, done: false }
      return todos.concat(newTodo)
    })
    form.reset()
  }

  return (
    <div>
      <ul style={{ listStyle: 'none', padding: 0 }}>
        {todos.map(todo => (
          <li key={todo.id}>
            <label style={{ display: 'block' }}>
              <input type="checkbox" checked={todo.done} onClick={() => toggle(todo.id)} />
              {' ' + todo.text}
            </label>
          </li>
        ))}
      </ul>
      <form onSubmit={addTodo}>
        <input name="todo" placeholder="Add ToDo [enter]" />
      </form>
    </div>
  )
}

render(<ToDos />, document.getElementById("app"));
```
