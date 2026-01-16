# React, but with Reasons

A personal portfolio documenting how I think about React, trade-offs, and frontend architecture.

[🇺🇸 English](https://HTHstudy.github.io/react-but-with-reasons/en)
| [🇰🇷 한국어](https://HTHstudy.github.io/react-but-with-reasons/ko)

---

## What is this?

This is **not a handbook** and **not a list of best practices**.

This repository is a curated collection of notes about **how I reason about frontend systems**, especially when working with React:
- how I frame problems,
- how I choose between alternatives,
- where abstractions help or hurt,
- and how I keep complexity under control as systems grow.

Rather than presenting “the right way”,  
this portfolio focuses on **decision-making and trade-offs**.

---

## What you’ll find here

### 1) Mental Model
How I conceptualize frontend systems before writing code.

- Separation of concerns as a practical tool
- UI logic vs domain logic
- Headless UI and boundary-driven design
- Accessibility as a design constraint, not an afterthought
- Service abstraction and responsibility boundaries

---

### 2) Decisions over Patterns
Why patterns are outcomes, not starting points.

- Controlled vs uncontrolled components
- State management decision guide
- Why React Context is not global state
- Using Context as scoped dependency injection (DI)
- Choosing the right primitive for the problem

---

### 3) Anti-patterns I’ve seen in the wild
Common mistakes that emerge when boundaries blur.

- Callback ref misuse and hidden coupling
- Implicit state and flag-driven logic
- When powerful abstractions become liabilities
- React StrictMode realities and their implications

These are not “don’ts”, but observations about **when costs start to outweigh benefits**.

---

### 4) Case Studies
Real-world refactoring stories (anonymized).

- Before / After comparisons
- What changed, why it changed
- What improved — and what trade-offs remained

(This section grows over time.)

---

## How to read this repository

- If you want a high-level view: start with **Mental Model**
- If you care about React-specific decisions: go to **Decisions over Patterns**
- If you enjoy learning from failures: read **Anti-patterns**
- If you prefer concrete examples: check **Case Studies**

Each document is written to stand on its own,  
but together they represent how I approach frontend engineering.

---

## Why this exists

Frontend complexity rarely comes from tools themselves.  
It usually comes from **unclear boundaries, premature abstractions, and unexamined decisions**.

This repository exists to make those decisions explicit.

---

## License

MIT
