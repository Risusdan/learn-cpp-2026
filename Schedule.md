# C++ Expert Learning Schedule

**Scope**: All required topics from the [Learning Path](LearningPath.md) (OPTIONAL topics excluded)

## Progress Tracker

| Week | Topic                                          | Status         |
| ---- | ---------------------------------------------- | -------------- |
| 1    | Introduction & Environment Setup               | ✅ Done        |
| 2    | Operators & Control Flow                       | ✅ Done        |
| 3    | Functions — Overloading & Operator Overloading | ✅ Done        |
| 4    | Lambdas & Static Polymorphism                  | ✅ Done        |
| 5    | Data Types & References / Memory Model         | ✅ Done        |
| 6    | Smart Pointers & Memory Safety                 | ✅ Done        |
| 7    | Structuring a Codebase                         | ✅ Done        |
| 8    | OOP & Dynamic Polymorphism                     | ✅ Done        |
| 9    | Multiple Inheritance & Rule of 0/5/3           | ✅ Done        |
| 10   | Exception Handling                             | ✅ Done        |
| 11   | auto & Type Casting                            | ✅ Done        |
| 12   | Undefined Behavior, ADL & Macros               | ✅ Done        |
| 13   | STL Containers, Iterators & iostream           | ✅ Done        |
| 14   | STL Algorithms, chrono & Multithreading        | ✅ Done        |
| 15   | Templates — Basics & Variadic Templates        | ✅ Done        |
| 16   | Template Specialization & Type Traits          | ✅ Done        |
| 17   | SFINAE, enable_if & Compile-Time Dispatch      | ✅ Done        |
| 18   | Idioms — RAII, Copy-and-Swap, Erase-Remove     | ✅ Done        |
| 19   | Pimpl, COW, CRTP & Standards Overview          | ✅ Done        |
| 20   | Tooling, Libraries & Capstone Project          | ✅ Done        |

---

## Week 1: Introduction & Environment Setup

*Review week — skim quickly if you already know this material.*

- [ ] What is C++? — compiled, statically typed, multi-paradigm language
- [ ] Why use C++ — performance-critical systems, zero-cost abstractions, deterministic destruction
- [ ] C vs C++ — key differences (type safety, RAII, namespaces, overloading, OOP)
- [ ] Installing C++ — compiler toolchain (clang++ or g++)
- [ ] Code Editors / IDEs — set up VS Code with clangd, or CLion / Xcode
- [ ] Running your First Program — compile, link, and execute from the command line

**Why It Matters**: C++ gives you direct hardware control *and* high-level abstractions — a combination almost no other language offers. Understanding *why* the language exists (filling the gap between C's raw power and higher-level languages' expressiveness) shapes how you think about every feature that follows.

---

## Week 2: Operators & Control Flow

*Review week — focus on anything that feels rusty.*

- [ ] Arithmetic Operators — `+`, `-`, `*`, `/`, `%`, and integer vs floating-point division
- [ ] Logical Operators — `&&`, `||`, `!`, and short-circuit evaluation
- [ ] Bitwise Operators — `&`, `|`, `^`, `~`, `<<`, `>>` and practical uses (flags, masks)
- [ ] `if` / `else` / `switch` / `goto` — branching logic (and why `goto` is almost never appropriate)
- [ ] `for` / `while` / `do while` loops — iteration patterns, range-based `for`

**Why It Matters**: Operators and control flow are the verbs of programming. The subtleties matter at the expert level — integer overflow is undefined behavior, short-circuit evaluation affects performance, and bitwise operations are essential in systems programming (network protocols, hardware registers, permission flags).

---

## Week 3: Functions — Overloading & Operator Overloading

- [ ] Function Overloading — same name, different parameter types/counts
- [ ] Why overloading matters — consistent interface, reduced cognitive load for callers
- [ ] Overload resolution rules — how the compiler picks the best match
- [ ] Operator Overloading — defining `+`, `<<`, `[]`, etc. for custom types
- [ ] When to overload operators — only when the meaning is intuitive and unsurprising
- [ ] Friend functions vs member functions for operator overloading

**Why It Matters**: Overloading lets you create types that feel native to the language. When `Vector3D` supports `+` and `*`, physics code reads like the math it represents. But the power cuts both ways — overloading an operator to do something surprising (like using `+` for string formatting) creates bugs that are nearly impossible to find. The guiding principle: **if the meaning isn't immediately obvious, use a named function instead**.

---

## Week 4: Lambdas & Static Polymorphism

- [ ] Lambda expressions — syntax, captures (`[=]`, `[&]`, `[this]`, `[var]`)
- [ ] Why lambdas exist — inline behavior at the call site, replacing verbose functor classes
- [ ] Capture pitfalls — dangling references, unnecessary copies, mutable lambdas
- [ ] Generic lambdas (`auto` parameters) and immediately-invoked lambdas
- [ ] Static (compile-time) polymorphism via function overloading and templates
- [ ] Static vs dynamic polymorphism — when to prefer each

**Why It Matters**: Before C++11, passing custom behavior to an algorithm meant writing a named functor class — often 15+ lines for a one-line operation. Lambdas solve this by letting you define behavior *at the point of use*. Understanding captures is critical: a lambda that captures a local by reference will dangle if it outlives the scope — one of the most common sources of use-after-free bugs in modern C++.

---

## Week 5: Data Types & References / Memory Model

- [ ] Static Typing — types checked at compile time; why this catches bugs early
- [ ] Dynamic Typing — `std::any`, `std::variant` as type-safe alternatives to `void*`
- [ ] RTTI (Run-Time Type Information) — `typeid`, `dynamic_cast`, and the runtime cost
- [ ] When RTTI is appropriate vs when to redesign with virtual methods
- [ ] References (`&`) — aliases, not pointers; cannot be null, cannot be reseated
- [ ] The C++ Memory Model — stack vs heap, automatic vs dynamic storage duration
- [ ] Lifetime of Objects — construction, destruction, and scope-based determinism

**Why It Matters**: C++ is one of the few languages where you *choose* where an object lives (stack or heap) and *control* exactly when it's destroyed. This is the foundation of RAII and everything that makes C++ resource management superior to manual `malloc`/`free`. Understanding lifetimes isn't optional — it's the mental model that prevents use-after-free, double-free, and memory leaks.

---

## Week 6: Smart Pointers & Memory Safety

- [ ] Why smart pointers exist — `new`/`delete` is error-prone (leaks, double-free, exception-unsafe)
- [ ] `std::unique_ptr` — exclusive ownership, zero overhead, the default choice
- [ ] `std::shared_ptr` — shared ownership via reference counting, and its cost
- [ ] `std::weak_ptr` — breaking reference cycles, observer pattern without preventing destruction
- [ ] `std::make_unique` / `std::make_shared` — why factory functions over raw `new`
- [ ] Raw Pointers — still valid for non-owning observation; the modern role of raw `T*`
- [ ] `new` / `delete` operators — what they do under the hood (and why you almost never call them)
- [ ] Memory leakage — how it happens, how smart pointers prevent it

**Why It Matters**: Memory bugs are the #1 source of security vulnerabilities in C and C++ code (buffer overflows, use-after-free, double-free). Smart pointers encode *ownership semantics* directly in the type system: `unique_ptr` means "exactly one owner," `shared_ptr` means "multiple owners with automatic cleanup," and raw `T*` means "I'm just looking, I don't own this." When you express ownership through types, the compiler enforces correctness — no discipline required, no leaks possible.

---

## Week 7: Structuring a Codebase

- [ ] Header files (`.h` / `.hpp`) vs implementation files (`.cpp`) — the compilation model
- [ ] Why separate declaration from definition — faster compilation, encapsulation
- [ ] Forward Declaration — reducing compile-time dependencies
- [ ] Include guards (`#pragma once`, `#ifndef`) — preventing double inclusion
- [ ] Scope — block scope, function scope, file scope, class scope
- [ ] Namespaces — organizing code, avoiding name collisions
- [ ] Anonymous namespaces and `static` for internal linkage

**Why It Matters**: In small projects, everything in one file works fine. In production codebases with hundreds of files and millions of lines, the compilation model becomes a critical bottleneck. Understanding *why* C++ separates headers from implementations (it's inherited from C's compilation model — each `.cpp` is compiled independently, and the linker stitches them together) lets you make informed decisions about build times, dependency management, and API design.

---

## Week 8: OOP & Dynamic Polymorphism

- [ ] Object-Oriented Programming — encapsulation, inheritance, polymorphism in C++
- [ ] Dynamic Polymorphism — behavior selected at *runtime* via virtual functions
- [ ] Virtual Methods — the `virtual` keyword, `override`, `final`
- [ ] Virtual Tables (vtable) — how the compiler implements dynamic dispatch under the hood
- [ ] The cost of virtual calls — indirection, cache misses, and when it matters
- [ ] Pure virtual functions and abstract classes
- [ ] When to use dynamic polymorphism vs alternatives (templates, `std::variant`)

**Why It Matters**: Dynamic polymorphism lets you write code that operates on *interfaces* without knowing the concrete type — the foundation of plugin architectures, driver models, and extensible frameworks. But it's not free: each virtual call involves a vtable lookup (pointer indirection), which can hurt in tight loops. Understanding the mechanism (the vtable is just a hidden array of function pointers) demystifies the cost and helps you decide when virtual dispatch is the right tool.

---

## Week 9: Multiple Inheritance & Rule of 0/5/3

- [ ] Multiple Inheritance — inheriting from more than one base class
- [ ] Diamond Inheritance — the problem and how `virtual` base classes solve it
- [ ] Why diamond inheritance is dangerous — ambiguity, complexity, maintenance burden
- [ ] Rule of Zero — if you don't manage resources, don't write special members
- [ ] Rule of Five — if you write one of destructor/copy-ctor/copy-assign/move-ctor/move-assign, write all five
- [ ] Rule of Three — the pre-C++11 version (no move semantics)
- [ ] When to use which rule — and why Rule of Zero is the goal

**Why It Matters**: The Rule of Zero is the most important guideline here: if your class uses only RAII members (`std::string`, `std::vector`, smart pointers), the compiler-generated special members do the right thing automatically. You only need the Rule of Five when you *directly* manage a resource (raw memory, file handle, socket). The diamond problem teaches you why deep inheritance hierarchies are fragile — prefer composition over inheritance in most designs.

---

## Week 10: Exception Handling

- [ ] Exceptions — `throw`, `try`, `catch`, and the stack unwinding process
- [ ] Why exceptions over error codes — automatic propagation, separation of happy path from error path
- [ ] Exit Codes — `EXIT_SUCCESS`, `EXIT_FAILURE`, and signaling errors to the OS
- [ ] Access Violations — segfaults, null dereference (not catchable via exceptions)
- [ ] Exception Safety Guarantees — no-throw, strong, basic
- [ ] `noexcept` — promising the compiler a function won't throw, enabling optimizations
- [ ] When to use exceptions vs error codes vs `std::optional` / `std::expected`

**Why It Matters**: Exception safety is what separates hobby code from production code. The key insight is that exceptions can fire *anywhere* — between any two lines. If your function acquired a resource on line 3 and an exception fires on line 5, does the resource leak? The three exception safety guarantees (basic, strong, no-throw) are contracts about what invariants hold after an exception. RAII automatically provides the basic guarantee. The `noexcept` specifier isn't just documentation — `std::vector` uses it to decide whether to copy or move elements during reallocation.

---

## Week 11: auto & Type Casting

- [ ] `auto` — automatic type deduction, when it helps and when it hides intent
- [ ] `auto` with references and `const` — `const auto&`, `auto&&`
- [ ] Structured bindings — `auto [key, value] = pair;`
- [ ] `static_cast` — safe, checked conversions (int↔double, enum↔int, base↔derived)
- [ ] `const_cast` — removing/adding `const`; almost always a code smell
- [ ] `dynamic_cast` — runtime-checked downcast using RTTI; returns `nullptr` on failure
- [ ] `reinterpret_cast` — bit-level reinterpretation; dangerous, rarely needed
- [ ] Why C++ has four casts instead of C's one — each cast expresses a different *intent*

**Why It Matters**: C has one cast syntax: `(int)x`. It can do *anything* — convert types, strip const, reinterpret bits — and the code doesn't tell you which. C++ splits this into four named casts so that each one communicates intent to both the compiler and the reader. `static_cast` says "this conversion is safe"; `reinterpret_cast` says "I'm doing something dangerous and I know it." When you see `reinterpret_cast` in a code review, it's an instant red flag — and that's the point.

---

## Week 12: Undefined Behavior, ADL & Macros

- [ ] Undefined Behavior (UB) — what it is, why the compiler assumes it never happens
- [ ] Common UB sources — signed overflow, null dereference, out-of-bounds access, data races
- [ ] UB sanitizers — `-fsanitize=undefined,address` as your first line of defense
- [ ] Argument Dependent Lookup (ADL) — how `std::cout << x` works without `std::operator<<`
- [ ] Why ADL exists — enabling operator overloading for types in their own namespace
- [ ] ADL pitfalls — unexpected function resolution, `using` declarations
- [ ] Macros — `#define`, `#ifdef`, `#pragma`, and why modern C++ minimizes their use
- [ ] Macros vs `constexpr` / `inline` / templates — type-safe alternatives

**Why It Matters**: Undefined behavior is the most counterintuitive concept in C++. When you write `int x = INT_MAX + 1;`, the compiler doesn't wrap around — it's allowed to assume that *never happens*, and it optimizes your code based on that assumption. This can cause the compiler to *remove* your null checks, *reorder* your statements, or produce completely unexpected behavior. UB is the source of the most baffling bugs in C++, and understanding it transforms how you write code.

---

## Week 13: STL Containers, Iterators & iostream

- [ ] STL Containers overview — sequence (vector, deque, list), associative (map, set), unordered
- [ ] Choosing the right container — vector as default, when to use map vs unordered_map
- [ ] Container performance characteristics — O(1) vs O(log n) vs O(n) for common operations
- [ ] Iterators — the glue between containers and algorithms
- [ ] Iterator categories — input, output, forward, bidirectional, random-access
- [ ] `iostream` — `std::cin`, `std::cout`, `std::cerr`, stream states, formatting

**Why It Matters**: The STL's design philosophy is *separation of concerns*: containers store data, algorithms transform data, and iterators connect them. This means `std::sort` works on *any* random-access container — vector, deque, or even a raw array — without knowing which one. Understanding this architecture lets you write generic code that works with any container, and it's the foundation for understanding ranges (C++20) and views.

---

## Week 14: STL Algorithms, chrono & Multithreading

- [ ] STL Algorithms — `find`, `transform`, `accumulate`, `remove_if`, `sort`, `partition`
- [ ] Algorithm composability — combining algorithms for complex transformations
- [ ] `<chrono>` — `system_clock`, `steady_clock`, durations, time points
- [ ] Why `<chrono>` over `time()` — type-safe time representation, no unit confusion
- [ ] Multithreading basics — `std::thread`, `std::mutex`, `std::lock_guard`
- [ ] `std::async` and `std::future` — task-based concurrency
- [ ] Data races — why shared mutable state is dangerous, and how mutexes prevent it

**Why It Matters**: Concurrency bugs are the hardest category of bugs to find because they're non-deterministic — the program might work 999 times and crash on the 1000th. A data race occurs when two threads access the same memory, at least one writes, and there's no synchronization. The result is undefined behavior — not "might give wrong answer" but "compiler is free to do absolutely anything." `std::mutex` and `std::lock_guard` (RAII for locks) are the fundamental tools for correctness.

---

## Week 15: Templates — Basics & Variadic Templates

- [ ] Function templates — writing type-generic functions
- [ ] Class templates — writing type-generic data structures
- [ ] Template argument deduction — how the compiler infers types
- [ ] Non-type template parameters — `template<std::size_t N>`
- [ ] Variadic templates — `template<typename... Args>` for any number of arguments
- [ ] Fold expressions (C++17) — `(args + ...)` to reduce parameter packs
- [ ] `sizeof...` — querying the size of a parameter pack at compile time

**Why It Matters**: Templates are C++'s mechanism for *zero-cost generics*. Unlike Java generics (type erasure) or Go interfaces (dynamic dispatch), C++ templates generate specialized code for each type at compile time — there's no runtime overhead. Variadic templates replaced the old C-style variadic functions (`printf`-style `...`) with type-safe, recursion-based pack expansion. Fold expressions (C++17) then simplified variadic templates from recursive patterns to one-liners.

---

## Week 16: Template Specialization & Type Traits

- [ ] Full template specialization — providing a custom implementation for a specific type
- [ ] Partial template specialization — specializing on a pattern (e.g., all pointer types)
- [ ] Why specialization matters — optimizing for specific types, handling edge cases
- [ ] Type traits (`<type_traits>`) — compile-time type introspection
- [ ] `std::is_integral`, `std::is_floating_point`, `std::is_same`, `std::is_pointer`
- [ ] `std::remove_const`, `std::remove_reference`, `std::decay`
- [ ] Writing custom type traits

**Why It Matters**: Template specialization is how the STL achieves both generality and performance. `std::vector<bool>` is a specialization that packs bits — different implementation, same interface. Type traits let you ask questions about types at compile time ("Is this an integer? Is it const? Is it a pointer?") and make different decisions based on the answers. This is the foundation of metaprogramming — writing code that reasons about code.

---

## Week 17: SFINAE, enable_if & Compile-Time Dispatch

- [ ] SFINAE — "Substitution Failure Is Not An Error"; the compiler silently discards bad overloads
- [ ] Why SFINAE exists — enables conditional overloads based on type properties
- [ ] `std::enable_if` — the classic SFINAE tool for constraining templates
- [ ] `if constexpr` (C++17) — compile-time branching that eliminates the discarded branch entirely
- [ ] `if constexpr` vs SFINAE — simpler syntax, fewer gotchas, preferred in modern C++
- [ ] Concepts preview (C++20) — the clean replacement for SFINAE/enable_if

**Why It Matters**: SFINAE is the most powerful (and historically most confusing) technique in template metaprogramming. The idea is simple: when the compiler tries to instantiate a template and hits a type error *in the function signature*, it doesn't fail — it just removes that overload from consideration. `enable_if` leverages this to say "this function only exists for integral types." C++17's `if constexpr` and C++20's Concepts dramatically simplify the same patterns, but understanding SFINAE is essential for reading existing codebases and libraries.

---

## Week 18: Idioms — RAII, Copy-and-Swap, Erase-Remove

- [ ] RAII (Resource Acquisition Is Initialization) — binding resource lifetime to object scope
- [ ] Why RAII is C++'s most important idiom — deterministic cleanup without `finally` blocks
- [ ] Copy-and-Swap idiom — exception-safe assignment using swap
- [ ] Erase-Remove idiom — the correct way to remove elements from STL containers
- [ ] Why `remove` doesn't actually remove — it only moves unwanted elements to the end
- [ ] Non-Copyable classes — `= delete` on copy operations
- [ ] Non-Moveable classes — `= delete` on move operations
- [ ] When to make a type non-copyable or non-moveable

**Why It Matters**: RAII is the single most important C++ idiom. In Java or Python, resources (files, locks, sockets) are cleaned up by a garbage collector *eventually* — or require explicit `try/finally` blocks. In C++, destructors run deterministically when scope ends, so a `lock_guard` *always* releases its mutex, a `unique_ptr` *always* frees its memory, and a `fstream` *always* closes its file. No discipline required, no cleanup code to forget. Every C++ expert thinks in terms of RAII.

---

## Week 19: Pimpl, COW, CRTP & Standards Overview

- [ ] Pimpl (Pointer to Implementation) — hiding implementation details behind an opaque pointer
- [ ] Why Pimpl exists — reducing compile-time dependencies, stabilizing ABI
- [ ] Copy on Write (COW) — sharing data until mutation, then copying
- [ ] When COW helps — large immutable objects shared across many owners
- [ ] CRTP (Curiously Recurring Template Pattern) — `class Derived : Base<Derived>`
- [ ] Why CRTP — static polymorphism without virtual call overhead
- [ ] C++11/14 highlights — move semantics, lambdas, `auto`, smart pointers, variadic templates
- [ ] C++17 highlights — `if constexpr`, structured bindings, `std::optional`, `std::variant`, fold expressions
- [ ] C++20 highlights — Concepts, Ranges, Coroutines, Modules, `std::format`
- [ ] C++23 highlights — `std::expected`, `std::print`, deducing `this`, `std::flat_map`

**Why It Matters**: Pimpl is the only reliable way to maintain a stable ABI (Application Binary Interface) in C++ — essential for shared libraries where recompiling all clients isn't an option. CRTP gives you the performance of static dispatch with the extensibility of inheritance — the STL uses it (`std::enable_shared_from_this`), and it's the backbone of many high-performance libraries. Understanding which C++ standard introduced which feature helps you write portable code for your target compiler version.

---

## Week 20: Tooling, Libraries & Capstone Project

- [ ] Understanding Debugger Messages — reading stack traces, core dumps, sanitizer output
- [ ] Debugging Symbols — `-g` flag, debug vs release builds
- [ ] Compiler Stages — preprocessing → compilation → assembly → linking
- [ ] Why understanding the build pipeline matters — diagnosing linker errors, ODR violations
- [ ] CMake — `CMakeLists.txt`, targets, libraries, `find_package`
- [ ] Licensing — MIT, Apache 2.0, GPL, LGPL — what you can and can't do
- [ ] Library Inclusion — static vs dynamic linking, `#include` paths
- [ ] Boost — the "STL incubator"; `boost::asio`, `boost::filesystem` (now `std::filesystem`)
- [ ] gtest / gmock — unit testing and mocking framework
- [ ] **Capstone Project** — tie everything together

**Why It Matters**: Knowing the language isn't enough — you need to know the ecosystem. CMake is the de facto build system for C++ (every major project uses it). Understanding compiler stages explains *why* you get cryptic linker errors ("undefined reference") and how to fix them. gtest is the industry-standard testing framework. This week turns knowledge into practical skill.

---
