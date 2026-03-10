# 引言

<!-- Step 0 控制在 10 分钟左右解决 -->

- 使用基于 [TypeScript](https://www.typescriptlang.org) 语言的 [React](https://react.dev) ([Next.js](https://nextjs.org)) 框架实现 Web 前端

![Conway's Game of Life](https://upload.wikimedia.org/wikipedia/commons/e/e6/Conways_game_of_life_breeder_animation.gif)

---

## In the age of LLMs

- [THUInfo](https://github.com/thu-info-community/thu-info-app)，一个古法编程的项目
  - 新人接手项目门槛高（学习框架、理解已有代码大约花费一个月）
  - 花费过多时间解决编写重复代码或者脏代码（raw data parsing etc.）
- LLM/Coding agent/Vibe coding 已经深度改变了软件开发流程，小白也能够快速上手
- 但 Vibe coding 的效果**取决于你的知识水平**：
  - 零基础 → AI 生成的代码看不懂、不会 debug、不知道对不对
  - 懂概念但不熟语言/框架 → AI 补齐实现细节，用户把关方向和正确性
  - 完全熟练 → AI 加速开发，成为效率工具
- **记住，人类，也就是你，才是项目最后的把关**

<!--
我假设大家都没什么开发经验，那么大家应该都是没有体会过古法编程的。我，很不幸，体验过。

THUInfo，是我第一个项目，我大一暑假参与，2020 年。那个时候我只学了最基本的程序知识，然后和我一起开发的前辈，花了一个月教我什么是 Git 什么是 JS/TS 什么是 React，一个多月后，我才写出来第一个能看的东西。

然而，AI 的出现，让小白也能用几句话就能看到能动能用的东西。更别说对 senior 的研发者，我现在也在做商业开发，现在其实大概三四个开发能力很强的朋友在一起，烧 token 烧一周，差不多也就能烧出来一个很不错的可以去骗投资人的产品了。

但是 AI 也不是万灵的，你如果没有最基本的判定能力，面临 AI 花最多三四分钟喷出来的几百几千行代码，光是阅读就很痛苦。

当你在某个时间点 lose control 了，那只有上帝知道这份代码在干什么了。AI 不会懂的，他自己看不懂昨天自己写的代码的。
-->

---

## What should humans do?

- **顶层设计**
  - 需求拆解：自然语言描述总是模糊，需要有人懂怎么精确地告诉 AI 需要做什么（prompt engineering）
  - 架构决策：AI 不总是给出最契合现实的方案，面临高并发、高 IO、低资源等极端工况，依然要人类去分析和选择方案
- **底层兜底**：看懂代码在做什么、判断有没有 bug 和安全问题、debug
- AI 最擅长的是**中间层**——具体的编码实现

所以本课程的策略：
- 语法细节、终端命令、命令行参数 → 有个概念即可，具体交给 AI 查
- **概念理解 → 重点**（组件、状态、异步、网络……）
- 关于如何用好 AI 辅助编程（Vibe coding），我们最后会详细讲

<!--
以往小作业的目标是让大家掌握一些语法，掌握一些框架的知识，现在看起来完全落伍了。

之前还会有一些小作业思考题，那个时候 DeepSeek 之类的模型都会被骗，当然现在骗不掉了。
-->

---

# 前端开发的基础概念

## 编程语言

- JavaScript 最初是一种嵌入在 HTML 网页中运行的脚本语言，为网页赋予动态效果
- Node.js 是另外一种 JavaScript 语言运行环境，脱离浏览器，可以在服务器端运行 JavaScript 代码
  - 所以 JS 代码的两种常见运行环境为浏览器与 Node.js

类比一下 Python 可以直接在终端内 `python main.py` 运行，在 Node.js 上运行 JavaScript 也是直接在终端内 `node main.js`。

![JS demo](/0-demo.png)

---

## 编程语言

- 而 JavaScript 弱类型，不利于大型项目的维护，于是 TypeScript 诞生
  - TypeScript 是 JavaScript 的超集，提供了静态类型检查
  - TypeScript 代码通过 `tsc` 编译器编译为 JavaScript 代码，并在此时检查类型声明是否成立

<img src="/0-language.png" style="width: 80%;">

---
layout: image-right
image: data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMjYiIGhlaWdodD0iMTYwIiBmaWxsPSJub25lIj48cGF0aCBmaWxsPSIjRjlBRDAwIiBkPSJNMjIxLjQ3OCA0OS43NTZoLTQ4Ljc4Vi45NzZoNDguNzh2NDguNzhaTTE2Ny44MSA0OS43NTZoLTQ4Ljc4MVYuOTc2aDQ4Ljc4MXY0OC43OFpNMTE0LjE1MSA0OS43NTZoLTQ4Ljc4Vi45NzZoNDguNzh2NDguNzhaTTIyMS40NzggMTAzLjQxNWgtNDguNzhWNTQuNjM0aDQ4Ljc4djQ4Ljc4MVoiLz48cGF0aCBmaWxsPSIjNEU0RTRFIiBkPSJNMTY3LjgxIDEwMy40MTVoLTQ4Ljc4MVY1NC42MzRoNDguNzgxdjQ4Ljc4MVpNMTY3LjgxIDE1Ny4wNzNoLTQ4Ljc4MXYtNDguNzhoNDguNzgxdjQ4Ljc4Wk0yMjEuNDc4IDE1Ny4wNzNoLTQ4Ljc4di00OC43OGg0OC43OHY0OC43OFpNMTE0LjE1MSAxNTcuMDczaC00OC43OHYtNDguNzhoNDguNzh2NDguNzhaTTEzLjA0MSA2OC4zNWMxLjY0IDAgMy4xNTUuMjE4IDQuNTQ5LjY1NSAxLjQyLjQxIDIuNjM2IDEuMDUyIDMuNjQ2IDEuOTI2IDEuMDExLjg3NCAxLjgwMyAxLjk4IDIuMzc3IDMuMzE5LjU3NCAxLjMxLjg2IDIuODgyLjg2IDQuNzEyIDAgMS43NDgtLjI0NSAzLjI3OC0uNzM3IDQuNTktLjQ5MiAxLjMxLTEuMTg4IDIuNDE3LTIuMDkgMy4zMTgtLjkwMS44NzQtMS45OTQgMS41My0zLjI3OCAxLjk2Ny0xLjI1Ni40MzctMi42NjMuNjU2LTQuMjIuNjU2LTEuMTc1IDAtMi4yNjgtLjE3OC0zLjI3OC0uNTMzdjYuODAyYy0uMjc0LjA4Mi0uNzEuMTY0LTEuMzEyLjI0Ni0uNi4xMDktMS4yMTUuMTY0LTEuODQ0LjE2NC0uNiAwLTEuMTQ3LS4wNDEtMS42MzktLjEyM2EyLjc0MiAyLjc0MiAwIDAgMS0xLjE4OC0uNDkyYy0uMzI4LS4yNDYtLjU3NC0uNTg3LS43MzctMS4wMjQtLjE2NC0uNDEtLjI0Ni0uOTU3LS4yNDYtMS42NFY3My4yMjZjMC0uNzM3LjE1LTEuMzM4LjQ1LTEuODAzLjMyOC0uNDY0Ljc2NS0uODg4IDEuMzEyLTEuMjcuODQ2LS41NDYgMS44OTgtLjk4MyAzLjE1NS0xLjMxMSAxLjI1Ni0uMzI4IDIuNjYzLS40OTIgNC4yMi0uNDkyWm0uMDgyIDE1LjY1MmMyLjgxNCAwIDQuMjItMS42OCA0LjIyLTUuMDQgMC0xLjc0OS0uMzU0LTMuMDQ2LTEuMDY1LTMuODkzLS42ODMtLjg0Ny0xLjY4LTEuMjctMi45OS0xLjI3LS41MiAwLS45ODQuMDY4LTEuMzk0LjIwNS0uNDEuMTEtLjc2NS4yNDYtMS4wNjUuNDF2OS4wMTRjLjMyNy4xNjQuNjgyLjMgMS4wNjUuNDEuMzgyLjExLjc5Mi4xNjQgMS4yMy4xNjRabTI3LjM3LTcuNzQ1YzAtLjg0Ni0uMjQ2LTEuNDYxLS43MzctMS44NDQtLjQ2NS0uNDEtMS4xMDctLjYxNC0xLjkyNi0uNjE0LS41NDcgMC0xLjA5My4wNjgtMS42NC4yMDUtLjUxOC4xMzYtLjk2OS4zNDEtMS4zNTEuNjE0djE0LjIxOWMtLjI3My4wODItLjcxLjE2NC0xLjMxMi4yNDYtLjU3My4wODItMS4xNzQuMTIzLTEuODAzLjEyMy0uNiAwLTEuMTQ3LS4wNDEtMS42MzktLjEyM2EyLjc0IDIuNzQgMCAwIDEtMS4xODgtLjQ5MiAyLjU1IDIuNTUgMCAwIDEtLjc3OC0uOTgzYy0uMTY0LS40MzctLjI0Ni0uOTk3LS4yNDYtMS42OFY3My42MzVjMC0uNzM4LjE1LTEuMzM5LjQ1LTEuODAzLjMyOC0uNDY0Ljc2NS0uODg4IDEuMzEyLTEuMjcuOTI4LS42NTYgMi4wOS0xLjE4OCAzLjQ4My0xLjU5OCAxLjQyLS40MSAyLjk5LS42MTUgNC43MTItLjYxNSAzLjA4NyAwIDUuNDYzLjY4MyA3LjEzIDIuMDQ5IDEuNjY2IDEuMzM5IDIuNSAzLjIxIDIuNSA1LjYxNHYxMi44MjVjLS4yNzQuMDgyLS43MTEuMTY0LTEuMzEyLjI0Ni0uNTc0LjA4Mi0xLjE3NS4xMjMtMS44MDMuMTIzLS42MDEgMC0xLjE0Ny0uMDQxLTEuNjM5LS4xMjNhMi43NCAyLjc0IDAgMCAxLTEuMTg4LS40OTIgMi41NSAyLjU1IDAgMCAxLS43NzktLjk4M2MtLjE2NC0uNDM3LS4yNDYtLjk5Ny0uMjQ2LTEuNjh2LTkuNjdaTTYwLjkgNjguMzVjMS42MzkgMCAzLjE1NS4yMTkgNC41NDguNjU2IDEuNDIuNDEgMi42MzYgMS4wNTIgMy42NDcgMS45MjYgMS4wMS44NzQgMS44MDIgMS45OCAyLjM3NiAzLjMxOS41NzQgMS4zMS44NiAyLjg4Mi44NiA0LjcxMiAwIDEuNzQ4LS4yNDUgMy4yNzgtLjczNyA0LjU5LS40OTIgMS4zMS0xLjE4OCAyLjQxNy0yLjA5IDMuMzE4LS45MDEuODc0LTEuOTk0IDEuNTMtMy4yNzggMS45NjctMS4yNTYuNDM3LTIuNjYzLjY1Ni00LjIyLjY1Ni0xLjE3NSAwLTIuMjY3LS4xNzgtMy4yNzgtLjUzM3Y2LjgwMmMtLjI3My4wODItLjcxLjE2NC0xLjMxMi4yNDYtLjYuMTA5LTEuMjE1LjE2NC0xLjg0My4xNjQtLjYwMSAwLTEuMTQ4LS4wNDEtMS42NC0uMTIzYTIuNzQyIDIuNzQyIDAgMCAxLTEuMTg4LS40OTJjLS4zMjgtLjI0Ni0uNTczLS41ODctLjczNy0xLjAyNC0uMTY0LS40MS0uMjQ2LS45NTctLjI0Ni0xLjY0VjczLjIyNmMwLS43MzcuMTUtMS4zMzguNDUtMS44MDMuMzI4LS40NjQuNzY1LS44ODggMS4zMTItMS4yNy44NDctLjU0NiAxLjg5OC0uOTgzIDMuMTU1LTEuMzExIDEuMjU3LS4zMjggMi42NjMtLjQ5MiA0LjIyLS40OTJabS4wODEgMTUuNjUzYzIuODE0IDAgNC4yMi0xLjY4IDQuMjItNS4wNCAwLTEuNzQ5LS4zNTQtMy4wNDYtMS4wNjQtMy44OTMtLjY4My0uODQ3LTEuNjgtMS4yNy0yLjk5Mi0xLjI3LS41MTkgMC0uOTgzLjA2OC0xLjM5My4yMDUtLjQxLjExLS43NjUuMjQ2LTEuMDY1LjQxdjkuMDE0Yy4zMjguMTY0LjY4My4zIDEuMDY1LjQxLjM4My4xMS43OTIuMTY0IDEuMjMuMTY0Wm0yNC4yOTctMTUuNjUzYzEuMTIgMCAyLjIxMy4xNjQgMy4yNzguNDkyIDEuMDkzLjMgMi4wMzUuNzY1IDIuODI4IDEuMzkzLjgyLS41NDYgMS43MzQtLjk5NyAyLjc0NS0xLjM1MiAxLjAzOC0uMzU1IDIuMjgxLS41MzMgMy43MjktLjUzMyAxLjAzOCAwIDIuMDQ5LjEzNyAzLjAzMi40MSAxLjAxMS4yNzMgMS44OTkuNzEgMi42NjMgMS4zMTEuNzkzLjU3NCAxLjQyMSAxLjM1MiAxLjg4NSAyLjMzNi40NjUuOTU2LjY5NyAyLjEzLjY5NyAzLjUyNHYxMi45MDdjLS4yNzMuMDgyLS43MS4xNjQtMS4zMTEuMjQ2LS41NzQuMDgyLTEuMTc1LjEyMy0xLjgwMy4xMjNhMTAgMTAgMCAwIDEtMS42MzktLjEyMyAyLjc0IDIuNzQgMCAwIDEtMS4xODktLjQ5MiAyLjU1NCAyLjU1NCAwIDAgMS0uNzc4LS45ODNjLS4xNjQtLjQzNy0uMjQ2LS45OTctLjI0Ni0xLjY4di05Ljc5M2MwLS44Mi0uMjMyLTEuNDA3LS42OTctMS43NjItLjQ2NC0uMzgzLTEuMDkyLS41NzQtMS44ODQtLjU3NC0uMzgzIDAtLjc5My4wOTUtMS4yMy4yODctLjQzNy4xNjQtLjc2NC4zNDEtLjk4My41MzIuMDI3LjExLjA0LjIxOS4wNC4zMjh2MTMuODkxYy0uMy4wODItLjc1LjE2NC0xLjM1MS4yNDYtLjU3NC4wODItMS4xNjEuMTIzLTEuNzYyLjEyM3MtMS4xNDgtLjA0MS0xLjY0LS4xMjNhMi43NCAyLjc0IDAgMCAxLTEuMTg4LS40OTIgMi41NSAyLjU1IDAgMCAxLS43NzgtLjk4M2MtLjE2NC0uNDM3LS4yNDYtLjk5Ny0uMjQ2LTEuNjh2LTkuNzkzYzAtLjgyLS4yNi0xLjQwNy0uNzc5LTEuNzYyLS40OTEtLjM4My0xLjA5Mi0uNTc0LTEuODAyLS41NzQtLjQ5MiAwLS45MTUuMDgyLTEuMjcuMjQ2YTcuMTggNy4xOCAwIDAgMC0uOTAyLjQxdjE0LjM4MmMtLjI3My4wODItLjcxLjE2NC0xLjMxMS4yNDZhMTIuNzQgMTIuNzQgMCAwIDEtMS44MDMuMTIzYy0uNjAxIDAtMS4xNDgtLjA0MS0xLjY0LS4xMjNhMi43NCAyLjc0IDAgMCAxLTEuMTg4LS40OTIgMi41NSAyLjU1IDAgMCAxLS43NzgtLjk4M2MtLjE2NC0uNDM3LS4yNDYtLjk5Ny0uMjQ2LTEuNjhWNzMuNTUzYzAtLjczNy4xNS0xLjMyNS40NS0xLjc2Mi4zMjktLjQzNy43NjYtLjg0NyAxLjMxMi0xLjIzLjkyOS0uNjU1IDIuMDc2LTEuMTg4IDMuNDQyLTEuNTk3YTE1LjMxMiAxNS4zMTIgMCAwIDEgNC4zNDMtLjYxNVoiLz48L3N2Zz4K
backgroundSize: 40%
---

## 包管理工具

- JavaScript 有着庞大的生态系统，有大量的第三方库可供使用
- pnpm 是本课程推荐使用的包管理工具
  - npm 是 Node.js 默认包管理工具，yarn/pnpm 为基于此的优化版本
- corepack 则是用来管理这些包管理工具的工具

做个类比：

- npm/yarn/pnpm 之于 JavaScript，相当于 pip 之于 Python

---
layout: image-right
image: https://www.vectorlogo.zone/logos/reactjs/reactjs-ar21.svg
backgroundSize: 40%
---

## 前端框架

- 早期网页开发需要手写大量裸 HTML/JS 代码，混乱且难以维护
- 于是出现了前端框架，用于简化前端开发
  - React 是 Facebook 开发的一个用于构建用户界面的 JavaScript 库
  - 通过组件化的方式构建界面，提高代码复用性和可维护性

<br>

<carbon-document />[React 官方中文文档](https://react.docschina.org)

---
layout: image-right
image: https://cdn.brandfetch.io/id2alue-rx/theme/dark/logo.svg?c=1bxid64Mup7aczewSAYMX&t=1714556231750
backgroundSize: 40%
---

## 前端框架

- Next.js 是基于 React 的一个更高层的框架
  - 更为方便的路由配置
  - 更简单的 SSR（Server-Side Rendering/服务端渲染）
- 可通过 API 路由实现类似于 [Express](http://expressjs.com) 的后端

<br>

<carbon-document />[Next.js 官方中文文档](https://www.nextjs.cn)

---

## 从源码到页面：构建流水线

一个现代前端项目从代码到用户看到页面，经历以下阶段：

```
你写的 TSX 代码（React 组件）
  →  Next.js 框架处理路由、SSR 等上层逻辑
  →  tsc/SWC 编译 TS 为 JS
  →  打包工具（Vite/Webpack）整合资源
  →  输出 HTML/JS/CSS
  →  部署到服务器
  →  浏览器加载渲染
```

其中 **React** 负责"用组件描述 UI"，**Next.js** 在其之上负责路由、SSR、API 等工程化能力。

两种渲染模式：
- **CSR**（Client-Side Rendering）：浏览器下载空 HTML + JS，由 JS 在客户端渲染页面
- **SSR**（Server-Side Rendering）：服务器预先渲染好 HTML 返回，首屏更快、利于 SEO

Next.js 同时支持 CSR 和 SSR，本课程以 CSR 为主。

---

## pnpm 基本用法

```bash
pnpm install        # 安装本项目全部依赖
pnpm add foo        # 添加依赖 foo
pnpm add -D foo     # 添加开发依赖（如测试框架）
pnpm remove foo     # 移除依赖
pnpm dev            # 运行 package.json 中定义的 dev 脚本
```

项目根目录的 `package.json` 定义了项目元信息、依赖列表和可执行脚本。

> 更多命令细节不必记忆——直接问 AI 即可。
