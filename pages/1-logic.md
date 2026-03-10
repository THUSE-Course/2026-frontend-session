# TypeScript 语法

<!-- Step 1 控制在 10 分钟左右解决 -->

## 目标

- 快速了解 TypeScript 语法要点
- 重点掌握**接口**与**函数/回调模式**（后续 React 核心前置知识）

---

## 语法速览

TypeScript 的基础语法（变量、运算、控制流）和 C++/Java 非常相似，不再逐一展开：

```ts twoslash
// 变量声明：const 不可变，let 可变，类型可自动推断
const name: string = "Alice";
let count = 0;

// 运算符与 C++ 一致，但判等请用 === 和 !==（避免类型隐式转换）
1 === 1;   // true
1 === "1"; // false

// 控制流写法与 C++ 一致
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) console.log(i);
}

// 数组与对象
const arr = [1, 2, 3];
const obj = { foo: 1, bar: "hello" };
```
<br />

> 语法细节不必死记——写代码时随时让 AI 帮你查。

---

## 接口（Interface）

接口用于定义对象应具有的属性结构，是理解 React Props 的前置知识：

```ts twoslash
interface UserInfo {
    name: string;
    age: number;
    hobbies: string[];
}

const alice: UserInfo = {
    name: "Alice",
    age: 20,
    hobbies: ["coding", "gaming"],
};
```

访问不存在的属性会得到 `undefined`，对 `undefined` 调用方法会导致运行时报错——TypeScript 会帮你检测这类错误。

---

## 函数与箭头函数

```ts twoslash
// 经典写法
function sum(x: number, y: number): number {
    return x + y;
}

// 箭头函数（lambda），React 中大量使用
const sum2 = (x: number, y: number): number => x + y;

sum(1, 2);  // 3
sum2(2, 3); // 5
```

---

## 回调模式

### 为什么需要回调？

考虑一个场景：你希望在用户点击按钮时执行某段逻辑。但**你写代码的时候并不知道用户什么时候会点击**。

你不能写 `while (没点击) { 等着 }`，因为这会卡死整个页面。

解决方案：**把"要做的事"写成一个函数，交给浏览器，让它在事件发生时替你调用。**

```ts
// 你不是在"调用"这个函数，而是把它"交出去"
button.addEventListener("click", () => {
    console.log("用户点击了！");
});
// 代码执行到这里时，上面的函数还没有被调用
// 只有用户真的点击了按钮，浏览器才会"回头调用"它
```

这就是**回调（Callback）**——"我先描述好要做什么，你在合适的时候帮我执行"。

> 该页面由 AI 生成。

---

### 更多的例子

回调不仅仅用于事件处理，它是 JS/TS/React 中最核心的模式：

| 场景 | 回调的内容 | 调用者 | 调用时机 |
|------|--------------|---------|------------|
| `Array.map(fn)` | 对每个元素做什么 | 数组方法 | 遍历时立即调用 |
| `onClick={fn}` | 点击时做什么 | React/浏览器 | 用户点击时 |
| `useEffect(fn)` | 副作用逻辑 | React | 渲染完成后 |
| `Promise.then(fn)` | 拿到结果后做什么 | Promise | 异步完成时 |
| `setTimeout(fn, 1000)` | 延迟执行什么 | 浏览器 | 1 秒后 |

<br />

> 该页面由 AI 生成。

---

### 数组的回调方法

```ts twoslash
const arr = [1, 2, 3, 4, 5];

// forEach: 遍历
arr.forEach((val, ind) => {
    console.log(`Index ${ind}: ${val}`);
});

// map: 映射为新数组（React 渲染列表的核心方法）
arr.map(val => val * val); // [1, 4, 9, 16, 25]

// filter: 筛选为新数组
arr.filter(val => val % 2 === 0); // [2, 4]
```

<!--
这一页的数组方法大家不需要死记，用的时候查就行。
重点是理解它们的共同模式：你传一个函数进去，告诉它"对每个元素做什么"。

后面讲 React 的时候你会看到，渲染一个列表就是：
data.map(item => <Component key={item.id} data={item} />)
本质上就是"对每条数据，返回一个组件"——这就是回调。

再往后讲 useEffect 和异步的时候，你传给 useEffect 的函数、
传给 .then() 的函数，都是回调——只不过调用时机不同。
所以今天把这个模式理解透了，后面的内容会顺畅很多。
-->

---

## 学习资源

语法细节请按需查阅，或直接让 AI 解释：

- [TypeScript 官方文档](https://www.typescriptlang.org/docs/)
- [TypeScript 语言基础 - 计算机系科协技能引导文档](https://docs.net9.org/languages/typescript/)