---
theme: default
background: ./assets/background.jpg
class: "text-center"
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
css: unocss
---

# AFFiNE 背后的通用协作框架

BlockSuite 与 CRDT 技术速览

王译锋 @ Toeverything

---

# 关于我

- 2016 年毕业于中国科学技术大学
- 美团学城知识库编辑器作者，开源富文本框架 Slate（20k+ star）核心贡献者
- 稿定科技图形编辑器核心研发，主导实现 3D 文字与实时协作能力，在国内超越 Canva
- 主导 Web PSD 编辑器 Photopea 项目国内独家合作
- 个人前端技术专栏有数百万访问量，译著《JavaScript the First 20 Years》
- 目前服务于 Toeverything，创建并持续维护 BlockSuite 项目

---

# TLDR

- 业界已有协作算法、UI 框架与编辑器框架，但仍缺失通用的协作应用技术栈，使 SaaS 开发成本高昂
- BlockSuite 有望填补这一空白，其对 block 通用的调度、编排与协作能力为其首创
- BlockSuite 的进化源于 CRDT 技术的突破，其背后可代表协作应用的范式变更

---

# BlockSuite 的技术突破点

- 📝 **Block-based 架构**：拆分文档 UI 为 block 容器，兼顾高稳定性与低开发成本
- 🧬 **CRDT 驱动**：自带实时协作、时间旅行、可插拔后端等诸多优势，且对业务逻辑无侵入
- 🎨 **框架无关的渲染层**：支持切换多种渲染器，并以开放的 Web Component 作为集成接口

---

# Block-based 架构

「为什么都说富文本编辑器是天坑？」

- 传统 Web-based 编辑器基于单个 `contenteditable` 容器实现，开发成本较低但：
  - 可控性差：需在单个 `contenteditable` 内维护复杂嵌套内容，难适配浏览器原生行为
  - 能力受限：需使用有别于主流前端框架的视图层，从未被视为通用的应用开发解决方案
- 传统 Office-like 编辑器自行实现文字排版，高度可控但：
  - 开发成本极高
  - 与前端生态完全隔离
- BlockSuite 实现了 block-based 架构，使得：
  - 100 个文本 block 内具备 100 个独立的 `contenteditable`，其中文本不再存在复杂嵌套
  - 图片等非文本 block 不再放置在 `contenteditable` 中，无兼容问题且可用任意前端框架实现
  - 易于集成至常规前端项目中：当前自研 Virgo 富文本组件已落地，可在流行前端框架中开箱即用

未来的协作应用应可按需在 UI 局部嵌入 block，而非仅使用单个大块的富文本编辑内容区域

---

# CRDT 驱动：基础概念

CRDT 的使用方式与 OT 有何不同？

``` ts
// BlockSuite 使用 Yjs 作为底层 CRDT 库
import * as Y from 'yjs'

const doc = new Y.Doc()

// CRDT 支持表达嵌套的 Map 结构，亦支持 Array
const yRoot = doc.getMap('root')
const yPoint = new Y.Map()
yPoint.set('x', 0)
yPoint.set('y', 0)
yRoot.set('point', yPoint)

// CRDT 也支持富文本
const yName = new Y.Text()
yName.insert(0, 'Kevin')
yRoot.set('name', yName)

// 可编码出 Uint8Array
Y.encodeStateAsUpdate(doc)
```

<img border="rounded" class="absolute bottom-40 right-30 w-50%" src="/assets/crdt-example.png">

---

# CRDT 驱动：类 Git 的心智模型

Git 和 CRDT 都会记录历史变更，且完全去中心化，本地可用

CRDT 应用生命周期：

1. 获得初始的 CRDT binary，类似 `git clone`
2. 进行本地的 `YMap.set`/`YArray.push` 等更新，类似 `git commit`
3. 分发更新，类似 (`git push`)

区别：

1. 无需手动 `git commit`，更新 CRDT model 会立刻记录改动
2. 不再存在 `git merge` 冲突，基于算法保证分布式合并的最终一致性
3. 无需手动的 `git pull` 与 `git push`

CRDT 序列化格式参见 [y-protocols](https://github.com/yjs/y-protocols)，相当于协作应用的 JSON

---

# CRDT 驱动：无侵入的协作状态管理

- 以 CRDT model（而非经典 UI 组件 model）作为应用 single source of truth
- 本地更新时优先更新 CRDT model，而非在两份 model 之间双向同步
- 来自 CRDT 的 `YEvent` 可在来自多种数据源的 YModel 更新时触发，应用全局 UI 均可基于该事件更新
- 无需在业务逻辑中区分本地更新与远程更新！
- 参见 BlockSuite 中的 `handleYEvents` 实现

<img border="rounded" class="mx-auto w-70%" src="/assets/event-flow.png">

---

# CRDT 驱动：基于 provider 的可插拔持久化

对 CRDT model 的更新可编码为 `Uint8Array` 二进制 buffer，便于网络分发与跨平台兼容

<img border="rounded" class="mx-auto mt-20px w-50%" src="/assets/providers.png">

---

# 框架无关的渲染层

- BlockSuite 的 store 层不依赖 DOM 与 UI 框架，可在不同渲染层中使用
  - AFFiNE 文档模式 block 内容基于 DOM 渲染
  - AFFiNE 白板模式笔刷内容基于 canvas 渲染
  - 两模式间共享同一棵 block tree，历史记录互通
- BlockSuite 使用 Web Component 实现 block，可作为标准化集成接口

---

# 总结

- Block-based 架构是建模下一代 SaaS 协作应用的通用基建，CRDT 则是该架构的基石
- 从自研 OT 到接入 CRDT 到使用 BlockSuite，研发成本均有数量级差距，其中有巨大商业化潜力
