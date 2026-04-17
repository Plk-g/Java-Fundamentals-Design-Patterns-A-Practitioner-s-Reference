# ☕ Java Fundamentals — A Practitioner's Reference

> A concept-by-concept exploration of core Java — from JVM basics and mathematical algorithms to generic type systems, custom data structures, and production-style concurrent pipelines.

[![Java](https://img.shields.io/badge/Java-17%2B-orange?style=flat-square&logo=openjdk)](https://openjdk.org/)
[![Eclipse](https://img.shields.io/badge/IDE-Eclipse-2C2255?style=flat-square&logo=eclipseide)](https://www.eclipse.org/)
[![Modules](https://img.shields.io/badge/Modules-8-blue?style=flat-square)]()

---

## Overview

This repository is a structured reference for Java fundamentals, organized by concept rather than chronology. Each module tackles a distinct area of the language with working implementations — the kind of problems that reveal *why* Java is designed the way it is, not just how to use it.

**Concepts covered:** JVM model · numerical algorithms · OOP & encapsulation · inheritance & polymorphism · generics & type bounds · Java Swing GUI · Collections framework · multithreaded pipeline design

---

## 📚 Modules

### 01 · Java Basics & Numerical Programming
**`/Assignment1`**

Entry point into the JVM. Covers the compilation pipeline (`javac` → bytecode → runtime), command-line I/O via `Scanner` and program arguments, and applies those tools immediately to a real physics problem: computing blackbody radiation intensity using Planck's Law.

> *If you can compute astrophysics in 30 lines, you understand the basics.*

Key concepts: `main(String[] args)` · `System.out` · `Scanner` · static methods · `Math` library

---

### 02 · Algorithms with Loops & Strings
**`/Assignment02_LoopsArraysStrings`**

Three implementations that show how loops become algorithms:

- **Leibniz Series** — estimates π using the alternating harmonic series, iterating until within `0.00001` of `Math.PI`. A clean example of numerical convergence.
- **Spiral Matrix Traversal** — reads an `m×n` matrix in spiral order using four boundary pointers (`top`, `bottom`, `left`, `right`). A classic interview problem with elegant loop logic.
- **Acronym Generator** — parses phrases into initialisms using `String.split()` and `StringBuilder`, demonstrating idiomatic Java string handling.

Key concepts: `while` / `for` loops · boundary pointer technique · `StringBuilder` · `String.split()` · `Character` utilities

---

### 03 · Object-Oriented Design
**`/Assignment03_ObjectsAndClasses`**

Two models that demonstrate what encapsulation really means in practice:

- **ComplexNumber** — a fully immutable value type supporting `+`, `-`, `×`, `÷`, and modulus over complex numbers. Handles edge cases like division by zero. Implements `toString()` with correct sign formatting.
- **Gradebook** — manages a fixed-capacity roster of `Student` objects, supporting lookup by ID, top-student ranking by average grade, and formatted display. Shows how to design a class around *invariants* (capacity > 0, no null slots).

Key concepts: encapsulation · immutability · `this` · constructor validation · `@Override` · `equals()` / `hashCode()`

---

### 04 · Inheritance & Polymorphism
**`/Assignment4_InheritanceAndArrayLists`**

Models a workplace hierarchy where `Intern extends Employee`. The base `Employee` class uses a static counter for auto-incrementing IDs — a pattern that enforces uniqueness without external tooling. `Intern` overrides `work()` and `toString()`, calling `super` to reuse parent logic cleanly.

Demonstrates why polymorphism matters: a `List<Employee>` can hold `Intern` objects and dispatch the right `work()` behavior at runtime without any casting.

Key concepts: `extends` · `super(...)` · method overriding · static fields · `ArrayList<T>` · polymorphic dispatch · `Objects.hash()`

---

### 05 · Abstractions, Interfaces & Generics
**`/Assignment5_AbstractsInterfacesGenerics`**

Two implementations that build up Java's type system:

- **Task / Person hierarchy** — `Task` is an abstract class with a concrete `getTime()` and abstract `performTask()`. Subclasses `DevelopModule`, `DocumentModule`, and `TestModule` specialize behavior. `Person` subclasses (`Developer`, `TechWriter`, `Tester`) manage a `jobQueue` of tasks — showing how abstract types enable extensible design.
- **Generic Triplet** — `Triplet<A, B, C>` where all three type parameters are bounded by `Comparable`. Implements `Comparable<Triplet<A,B,C>>` by comparing 3D magnitude (`√(x²+y²+z²)`), making triplets sortable when they hold `Number` types.

Key concepts: `abstract` class · `interface` · bounded type parameters (`<T extends Comparable<T>>`) · `implements Comparable<T>` · `instanceof` with numeric dispatch

---

### 06 · GUI Application — Contacts Manager
**`/Assignment6_ContactsManager`**

A fully functional desktop application built with Java Swing. Loads contacts from a CSV file and presents them in a sortable `JTable`. Users can add, edit, delete, and search contacts — all without touching the underlying data file until explicitly saved.

The architecture separates the data model from the UI layer, applying a lightweight MVC approach: the table model reacts to data changes rather than the UI directly manipulating state.

Key concepts: `JFrame` · `JTable` / `DefaultTableModel` · event listeners · `ActionListener` · CSV file I/O · MVC separation

---

### 07 · The Collections Framework
**`/Assignment07_Collections`**

Three implementations that go beyond `ArrayList`:

- **DuplicateWords** — uses a `HashSet` to find repeated words in *Little Women* (the full text is included). Shows constant-time membership testing vs. linear scan.
- **URL Access Log** — uses `LinkedHashMap<String, Deque<String>>` to track the 5 most recent URLs per user from a server log CSV. Combines insertion-order maps with bounded deques for a sliding-window pattern.
- **CustomLinkedList\<T\>** — a generic singly linked list that **fully implements `java.util.Queue<T>`**, including `offer`, `poll`, `peek`, `iterator`, and all `Collection` methods. Written from scratch with proper head/tail pointer management and a custom `Iterator` that throws `ConcurrentModificationException` on structural changes.

Key concepts: `HashMap` · `HashSet` · `LinkedHashMap` · `ArrayDeque` · implementing `Queue<T>` · generic `Iterator` · `ConcurrentModificationException`

---

### 08 · Concurrent Programming — Multithreaded Pipeline
**`/Assignment8_PipelineThreads`**

The most architecturally complex module. Implements a production-style data processing pipeline with four stages running on separate threads:

```
CSV File → [Reader] → Queue₁ → [Validator × 2] → Queue₂ → [Aggregator]
                                       ↓
                                  [ErrorWriter]
```

- **Reader** — parses a CSV of financial transactions into typed `Tx` records and feeds them into a bounded `LinkedBlockingQueue` (capacity 256), providing natural backpressure.
- **Validator** (×2 worker threads) — validates each transaction (non-null IDs, timestamp range 2000–2100, amount within bounds) and routes valid records to the aggregator queue or malformed ones to the error queue.
- **Aggregator** — consumes validated transactions and computes running totals.
- **ErrorWriter** — drains the error queue to `errors.csv` concurrently.

Shutdown is coordinated via a **poison pill** pattern: `Reader` sends one `Stop` sentinel per validator thread; validators propagate it downstream; all stages terminate cleanly. Duplicate suppression ensures exactly-once aggregation.

Key concepts: `BlockingQueue` · `LinkedBlockingQueue` · `Runnable` · `Thread.join()` · poison pill pattern · backpressure · producer-consumer · exactly-once semantics

---

## 🛠 Tech Stack

| Tool | Role |
|------|------|
| Java 17+ | Language & JVM runtime |
| Eclipse IDE | Development environment |
| Java Swing | Desktop GUI (Module 06) |
| `java.util.concurrent` | Thread-safe queues & pipeline (Module 08) |

---

## 🚀 Running a Module

```bash
# Clone
git clone https://github.com/Plk-g/Java101.git
cd Java101

# Example: run the Leibniz π estimator
cd Assignment02_LoopsArraysStrings/PartI
javac Leibniz.java && java Leibniz

# Example: run the multithreaded pipeline
cd Assignment8_PipelineThreads/src
javac *.java
java PipelineApp ../transactions1.csv
```

> The pipeline module outputs aggregated results to stdout and malformed rows to `errors.csv` in the working directory.

---

## 🗺 Recommended Reading Order

```
01 Basics → 02 Algorithms → 03 OOP Design → 04 Inheritance
                                                    ↓
07 Collections ← 06 GUI App ← 05 Generics & Interfaces
      ↓
08 Concurrent Pipeline
```

Each module is self-contained, but concepts compound — generics (05) appear inside the custom linked list (07), which informs the queue types used in the pipeline (08).

---

*Built by [Palak Gupta](https://github.com/Plk-g) — feedback and questions welcome via issues.*
