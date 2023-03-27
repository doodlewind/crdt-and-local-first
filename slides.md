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

# CRDT and Local-first Architecture

A step forward from PWA

Yifeng Wang @ Toeverything

---

# About Me

- 🧑‍💻 Co-founder <a href="https://github.com/toeverything">@Toeverything</a>, currently building AFFiNE & BlockSuite
- 👨‍🎨 Spent years working on text, graphics, and collaboration
- 🤐 Former tech blogger behind the Great Firewall
- 📜 Amateur JavaScript historian, translated *JavaScript the First 20 Years*
- 🌈 #OpenSourceEnthusiast, find me on GitHub [@doodlewind](https://github.com/doodlewind)

---

# Outline

- 🚩 **Challenge**: Limitations with existing data storage and sharing approaches
- 💾 **Local-first software**: Prioritizing the usage of local resources over remote servers
- 🔗 **CRDT**: The technology that enables local-first architecture
- 💡 **AFFiNE**: A concrete example of a local-first application in practice

---

# The Challenge

A quick recap of web apps

<div grid="~ cols-2 gap-4">

<div>
Web app comes with a lot of benefits...

- ✅ Easy to distribute
- ✅ Easy to cross-platform
- ✅ Easy to collaborate

But also some tradeoffs...

- 😐 Network latency
- 😐 Connectivity requirement
- 😐 Data control and ownership
</div>

<div grid="~ cols-1 gap-20px">
<img v-click border="rounded" class="mx-auto" src="/assets/world-ping-times.png">
<img v-click border="rounded" class="w-70%" src="/assets/figma-offline.png">
</div>

</div>

<div grid="~ cols-2 gap-20px" class="mt-20px">
</div>

---

# Progressive Improvement: The Role of PWA

A quick recap of PWA

- 📥 App shell architecture for optimal page init performance
- 🔄 Service worker for offline caching and content serving
- 💾 Local storage for offline data persistence
- 📶 Background data synchronization when back online

<p v-click font-bold>Do we truly need the caching mindset and a central server?</p>

---

# The Alternative: Local-first Architecture

Local state as single source of truth

- **Complete offline functionality**: Fully usable without internet access
- **Reduced latency**: Faster data access by eliminating server roundtrips
- **Data ownership**: Users have full control over their data and storage
- **Enhanced privacy**: Data stays on the user's device, reducing exposure to third parties
- **Simplified DX**: Reduced server complexity by offloading data management to clients

<p v-click font-bold>But when it comes to collaboration...</p>

---

# CRDT: Prerequisite for local-first collaboration

Conflict-free replicated data type - What it is?

``` ts
import * as Y from 'yjs'

// Model states can be hosted in a CRDT container
const doc = new Y.Doc()

// Different top-level YModel instances can be created
const yRoot = doc.getMap('root')

// Using class constructors
const yPoint = new Y.Map()
yPoint.set('x', 0)
yPoint.set('y', 0)

// Composing nested structure
yRoot.set('point', yPoint)

// And essential rich text support
const yName = new Y.Text()
yName.insert(0, 'Kevin')
yRoot.set('name', yName)
```

<img border="rounded" class="absolute bottom-20 right-30 w-50%" src="/assets/crdt-example.png">

---

# CRDT: Prerequisite for local-first collaboration

Conflict-free replicated data type - How it works?

- Recalling the classical Redux way: defining serializable actions -> *event sourcing*!
- Similar in command driven editors: defining `add_element`, `change_element`, `remove_element`...
- Working with two kinds of data: **model** and **operation** (*commands*, *actions*...).
- So when it comes to handling conflicts:
  - Transforming **operations** - OT
  - Making **models** conflict-free - CRDT
- To make this happen, operation-based CRDTs essentially <u>record all history operations</u>
- Every operation contains `clientID` and logical timestamp, making it decentralized and deterministic

---

# CRDT: Git That Doesn't Conflict

Reusing the Git mental model to understand CRDT

Lifecycle of a CRDT-based application:

1. Duplicate the "repository" (`git clone`)
2. Make changes locally (`git commit`)
3. Push changes to the "remote" (`git push`)

Differences:

1. No need for manual `git commit`
2. No conflict on `git merge`
3. No need for manual `git pull` and `git push`

---

# CRDT-driven: State Management in Local-first Apps

We just not distinguish between local and remote anymore!

- TODO

---


# Also Better DX: Provider-based persistence

- TODO `YEvent`
- Making all model APIs synchronous

---

# FAQ of CRDT

- Mathematical correctness is more important than intention keeping
- TODO

---

# How AFFiNE Works

- TODO

---
