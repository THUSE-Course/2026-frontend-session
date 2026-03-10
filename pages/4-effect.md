# 副作用与异步

<!-- Step 4 控制在 25 分钟讲解 + 15 分钟实践左右解决 -->

## 目标

- 理解什么是副作用，掌握 `useEffect` Hook
- 理解异步模型，掌握 Promise 和 async/await

---

## 组件的主作用是什么？

回顾一下我们已经学过的 React 组件——本质上就是一个**纯函数**：

```tsx
const Square = (props: { color: string }) => {
    return <div style={{ backgroundColor: props.color }} />;
};
```

给定相同的 props 和 state，永远返回相同的 JSX。这就是组件的**主作用**：描述"UI 长什么样"。

但现实中，组件往往需要做一些**超出"计算 UI"之外的事**。

---

## 什么是副作用？

**副作用（Side Effect）**= 不属于"根据输入计算输出"的一切操作：

- 发网络请求获取数据
- 设定/清理计时器
- 读写 localStorage
- 手动操作 DOM

这些事情在改变外部世界，不是纯函数该做的。但组件又**确实需要做**。

问题是：**你不能在组件函数体里直接做这些事。** 为什么？

<!--
"副作用"这个词来自函数式编程。不是说它"不好"，
而是说它不是组件的主要职责（渲染 UI），是"附带要做的事"。
React 的态度是：主作用和副作用要分开管理。
所以这个 Hook 叫 useEffect——use (side) effect。
-->

---

## 为什么不能直接写在组件里？

```tsx
const DemoComponent = () => {
    // 直接在组件函数体里 fetch
    fetch("/api/data")
        .then(res => res.json())
        .then(data => setState(data));

    return <div>...</div>;
};
```

组件函数**每次状态变化都会重新执行**：

```
fetch → 拿到数据 → setState → 触发重渲染
  → 组件函数再次执行 → 又 fetch → 又 setState → ……无限循环
```

所以我们需要一种机制：**告诉 React "这个操作只在特定条件下执行"**。

---

## `useEffect` Hook

`useEffect` = **use (side) effect**，让你把副作用从渲染中隔离出来。

执行时序：

```
组件函数执行 → 计算 JSX → React 更新 DOM → 浏览器绘制页面 → useEffect 回调执行
```

**先让用户看到界面，再去做附带的事。** useEffect 天然不阻塞渲染。

每次新副作用执行前，React 会先**清除上一次的副作用**，组件销毁时也会清除。

---

## `useEffect` 的代码表现

```tsx twoslash
import React, { useEffect } from "react";
const depList: any[] = [];
// ---cut---
useEffect(() => {
    // 在这里写你需要执行的副作用
    // 例如获取数据、设定计时器等
    
    return () => {
      	// 在这里写副作用的清除，不需要清除的副作用可以不写返回值
        // 比如设定计时器之后需要回收计时器
        // 下一次渲染的时候上一次渲染所定义的副作用会被这个函数清除
        // 组件本身销毁的时候也会执行
    };
}, depList /* depList 用于控制上述副作用何时触发 */);
```

| 要素 | 对应代码 | 回答的问题 |
|------|---------|-----------|
| 副作用内容 | 第一个参数（函数体） | 做什么？ |
| 清理方法 | return 的函数 | 怎么收尾？ |
| 触发时机 | 第二个参数（依赖列表） | 什么时候做？ |

---

## 依赖列表

`useEffect` 的第二个参数控制副作用何时被触发：

| depList | 触发时机 | 类比 |
|---------|---------|------|
| `undefined`（不传） | 每次渲染后都触发 | "每次都做" |
| `[]`（空数组） | 只在首次渲染后触发 | "只做一次" |
| `[a, b]` | 首次渲染以及 `a` 或 `b` 变化时触发 | "盯着这几个值，变了就做" |

---

## 代码框架里的例子

观察下述例子：

```tsx twoslash
import React, { useEffect, useRef } from "react";
const timerRef = useRef<number | undefined>(undefined);
// ---cut---
useEffect(() => () => {
    clearInterval(timerRef.current);
}, []);
```

逐个拆解这个 Hook：

- 调用时机。由于第二个参数是空数组，所以这个副作用只会在第一次渲染时触发，清除函数也只会在组件销毁时触发
- 副作用的具体内容。空
- 副作用的清理方法。清理计时器

所以说这个副作用的含义是，在组件销毁时清理掉没有因用户手动点击而清除的计时器。

---

## 代码框架里的例子

观察下述例子：

```tsx {*} twoslash
import { useRouter } from "next/router";
import { useEffect } from "react";
const request = () => {};
// ---cut---
const router = useRouter();
const query = router.query;

useEffect(() => {
    if (!router.isReady) {
        return;
    }

    // ...

    request();
}, [router, query]);
```

---

## 代码框架里的例子

逐个拆解这个 Hook：

- 调用时机。在 `router, query` 发生改变时调用，由于这两者仅会在组件首次渲染后发生一次改变，所以该副作用调用两次，第一次是首次渲染，第二次是路由参数准备完毕。其中第一次调用会被 `router.isReady` 拦截
- 副作用的具体内容。根据 `id` 请求数据
- 副作用的清理方法。空

所以说这个副作用的含义是，等待路由参数准备完毕后，根据路由参数请求数据。

---

## 异步

useEffect 最常见的应用就是写网络请求，一旦说到网络，就必须要提到异步。

---

## 单线程与事件循环

JS 是**单线程**语言，如果网络请求需要 2 秒，同步等待 = 页面冻结 2 秒。

解决方案：**事件循环（Event Loop）**——遇到耗时操作，交给后台，先继续执行后面的代码。

```
JS 线程:  [执行代码] → [遇到 fetch] → [继续执行后续代码] → [栈空闲] → [执行回调]
                          │                                       ↑
                          ↓                                       │
后台:                    [发起网络请求 ·················· 完成] → 回调入队
```

> 核心思想：**不等结果，先去做别的事；结果回来了再处理。**

<!--
大家可能习惯了 C/C++ 或 Python 里同步阻塞的编程模型——调用一个函数，等它返回，拿到结果，继续。
但 JS 是单线程的，如果你同步等一个网络请求，整个页面就卡死了。

所以 JS 的设计是：遇到耗时操作，交给浏览器后台去做，我先继续执行后面的代码。
等后台做完了，把回调函数丢进一个队列里排队。
当前代码全部执行完、调用栈空了，JS 引擎才从队列里取出回调来执行。
这个不断循环"执行代码→检查队列→执行回调"的过程，就叫事件循环。

理解了这个模型，你就能理解为什么 fetch 不能直接拿到返回值，
为什么要用 .then() 或 await 来"等"结果。
-->

---

## Promise——对"未来值"的抽象

异步操作返回的不是结果本身，而是一个 **Promise**（承诺）——代表"这个值将来会有"。

Promise 有三种状态：

| 状态 | 含义 |
|------|------|
| **Pending** | 进行中，还没有结果 |
| **Fulfilled** | 成功，拿到了值 |
| **Rejected** | 失败，拿到了错误 |

```ts
const promise = fetch("/api/data"); // 立刻返回一个 Promise，此时状态为 Pending
// 网络请求在后台进行……
// 请求成功 → Fulfilled，请求失败 → Rejected
```

问题是：Promise 里的值你**现在拿不到**，因为它还没回来。那怎么办？

---

## `.then()` 和 `.catch()` ——注册回调

`.then()` 的本质就是回调：**告诉 Promise "你完成之后帮我做这件事"**。

```ts
fetch("/api/data")
    .then((response) => {
        // 这个函数在请求成功后被调用，response 就是拿到的结果
        return response.json(); // 解析 JSON，又返回一个新的 Promise
    })
    .then((data) => {
        // 上一步 .json() 解析完成后被调用
        console.log(data);
    })
    .catch((err) => {
        // 任何一步出错都会跳到这里
        console.error(err);
    });

// 代码执行到这里时，上面的请求还没完成！.then() 只是"预约"了回调，不会阻塞
```

`.then()` 链可以理解为：**第一步做完把结果传给第二步，第二步做完传给第三步……任何一步出错就跳到 `.catch()`。**

<!--
这里可以类比一下现实生活：
你去餐厅点餐，服务员给你一个取餐号（Promise）。
你不用站在柜台前等，可以先去找座位、玩手机（继续执行后续代码）。
等餐做好了，叫你的号（Fulfilled），你去取餐（.then 回调被执行）。
如果后厨发现食材没了，会通知你（Rejected），你去处理（.catch 回调被执行）。
-->

---

## `async/await` ——让异步代码看起来像同步

`.then()` 链一长就不好读。`async/await` 是 Promise 的**语法糖**，让你用同步的写法做异步的事：

````md magic-move
```js
// then 链写法
fetch("/api/data")
    .then((response) => response.json())
    .then((data) => {
        console.log(data);
    })
    .catch((err) => {
        console.error(err);
    });
```

```js
// async/await 等价写法
const response = await fetch("/api/data");
const data = await response.json();
console.log(data);
```
````

- `async`：标记一个函数为异步函数，它的返回值自动包装为 Promise
- `await`：**暂停当前异步函数**，等 Promise 完成后拿到值，再继续往下执行
- `await` 只能在 `async` 函数内部使用

> `async/await` 和 `.then()` 做的是同一件事——只是写法不同。选哪个看团队习惯。
