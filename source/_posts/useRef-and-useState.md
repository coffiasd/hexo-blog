---
title: useRef and useState
date: 2022-11-14 15:20:56
tags:
  - React
categories:
  - React
---

## Add a ref to your project

```
import {useRef} from 'react';
const ref = useRef(0);

//the ref'll be like this
{
  current: 0 // The value you passed to useRef
}
```

## Ref 使用示例

```
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

这个用法看起来和 useState 差不多，使用 useState 也可以达到相同的效果,下面这个例子是 useState 的实现:

```
import { useState } from 'react';

export default function Counter() {
  let [ref,setRef] = useState(1);

  function handleClick() {
    setRef(ref+1);
    alert('You clicked ' + ref + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

那为什么还要多加一个 ref 呢？实际上是因为两者之间还是存在一些不同

## useRef 和 useState 区别

| #                |                      refs | state            |
| :--------------- | ------------------------: | ---------------- |
| 初始化后返回值   | { current: initialValue } | 返回值和设置函数 |
| 是否触发重新渲染 |                        否 | 是               |
| 值的改变         |      可在渲染过程外改变值 | 只能通过设置函数 |
| 读写时间         |        不能在渲染期间读写 | 任何时间都能读写 |

基于上诉的区别我们再看下例子:

```
import { useRef } from 'react';

export default function Counter() {
  let countRef = useRef(0);

  function handleClick() {
    // This doesn't re-render the component!
    countRef.current = countRef.current + 1;
  }

  return (
    <button onClick={handleClick}>
      You clicked {countRef.current} times
    </button>
  );
}
```

通过运行脚本我们会发现及时触发了点击事件并且改变了 countRef 的值，但是实际页面上没有任何的变化

## useRef 的使用场景

- 存储 timeout ID
- 存储 DOM element
  我们再通过一个示例看下 useRef 的使用

```
import { useState } from 'react';

export default function Chat() {
  const [text, setText] = useState('');
  const [isSending, setIsSending] = useState(false);
  let timeoutID = null;

  function handleSend() {
    setIsSending(true);
    timeoutID = setTimeout(() => {
      alert('Sent!');
      setIsSending(false);
    }, 3000);
  }

  function handleUndo() {
    setIsSending(false);
    clearTimeout(timeoutID);
  }

  return (
    <>
      <input
        disabled={isSending}
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button
        disabled={isSending}
        onClick={handleSend}>
        {isSending ? 'Sending...' : 'Send'}
      </button>
      {isSending &&
        <button onClick={handleUndo}>
          Undo
        </button>
      }
    </>
  );
}
```

通过运行我们会发现及时我们触发了 Undo 事件取消了 timeoutID,我们还是会再页面上收到 alert 信息？
这是因为我们页面在 re-render 的时候其实是读不到 timeoutID 这个本地变量的.通过 console.log 可以看到这个 undo 里面的 timeoutID 的值是 null。
我们只需要进行一些修改:

```
const timeoutRef = useRef(null);

timeoutRef.current = ....

clearTimeout(timeoutRef.current);
```
