# CLAUDE.md — learn-cpp-2026

## Project Structure

This is a self-study curriculum for learning C++ from fundamentals to expert level.

- `README.md` — Repository overview
- `LearningPath.md` — Full topic tree with OPTIONAL markers
- `Schedule.md` — Week-by-week plan with checkboxes and "Why It Matters" context
- `weeks/week-NN/topic-name.md` — Individual lesson files (generated over time)

## How to Generate Lessons

1. **Always read `Schedule.md` first** to determine what topics to cover for the requested week.
2. Only cover topics listed in the schedule. Respect OPTIONAL markers in `LearningPath.md` — do not include OPTIONAL topics unless explicitly asked.
3. Create lesson files at `weeks/week-NN/topic-name.md` (e.g., `weeks/week-06/smart-pointers.md`).
4. One file per major topic grouping within a week. If a week covers multiple distinct topics, split them into separate files.

## Lesson File Template

Every lesson file must follow this exact structure:

```markdown
# Topic Name

> One-sentence summary of what this topic is and why it matters.

## Core Concepts

[Explanation broken into logical subsections with ### headings as needed.
For every concept, cover three layers:
1. WHAT — what is this thing?
2. HOW — how does it work / how do you use it?
3. WHY — why does it exist? Why this approach over alternatives? Why does it matter?
The "why" is the most important layer — it builds lasting intuition.
Build intuition first, then add precision. Use analogies for abstract ideas.
Keep paragraphs short (3-5 sentences max).
Use Mermaid diagrams (```mermaid blocks) when visual representation helps —
e.g., memory layouts, class hierarchies, object lifetime diagrams, compilation pipeline.]

## Code Examples

[Annotated, idiomatic, production-quality code. Show how a professional
would actually write this — proper naming, error handling where appropriate,
clean structure. Each example should be self-contained and compilable.]

## Common Pitfalls

[Bad vs good code comparisons. Each pitfall gets:
- What the mistake is
- Why it's wrong
- The correct approach with code]

## Key Takeaways

- Bullet list of 3-5 most important points
- What to remember, what to internalize
```

## Writing Style

- **Audience**: Self-learner studying independently — no instructor, no classroom.
- **Tone**: Concise, expert, opinionated. Write like a senior engineer mentoring a colleague, not a textbook.
- **Structure**: Build intuition first, then add precision. Use analogies for abstract ideas.
- **Why-first**: For every concept, always explain *why* it exists, *why* this approach, and *why* it matters. The "why" is more important than the "what."
- **Paragraphs**: Keep short — 3-5 sentences max. Dense walls of text kill learning.
- **No exercises**: Do not include practice problems, homework, or challenges.
- **No external resources**: Do not include "Further Reading" sections or links to external material.

## Code Example Standards

- Write **idiomatic, production-quality code** — the kind a senior engineer would write at work.
- Show professional coding habits: meaningful names, const-correctness, proper error handling, clean structure.
- Every code block must be **self-contained and compilable** (include necessary `#include` headers and `main()` where appropriate).
- Use detailed inline comments to explain *why*, not just *what*.
- When showing a pattern or technique, show the **realistic use case**, not a toy example.

## Common Pitfalls Format

Each pitfall must include:

1. A brief description of the mistake
2. A `// BAD` code block showing the wrong way
3. An explanation of *why* it's wrong
4. A `// GOOD` code block showing the correct approach

```cpp
// BAD — [description of what's wrong]
[bad code]

// GOOD — [description of the fix]
[good code]
```

---

## Repo-Specific: C++

### Language & Standard

- **Language**: C++ (all code examples in C++)
- **Target standard**: **C++17** — this is the baseline. C++20 is not yet widely adopted in production codebases, so do not use C++20 features (concepts, ranges, coroutines, modules, `std::format`) in code examples. Mention C++20 only in prose as a brief forward-looking note when relevant (e.g., "C++20 Concepts will eventually replace SFINAE for this use case").
- **Compiler assumption**: clang++ or g++ with `-std=c++17`

### Coding Conventions

- Use `snake_case` for variables and functions, `PascalCase` for types/classes, `UPPER_SNAKE_CASE` for constants
- Always use `const` and `constexpr` where possible — const-correctness is non-negotiable
- Prefer `auto` when the type is obvious from the right-hand side; spell out the type when it aids readability
- Use `std::string_view` over `const std::string&` for read-only string parameters
- Use structured bindings (`auto [key, value] = ...`) where they improve readability
- Use `enum class` over plain `enum` — always
- Prefer `using` aliases over `typedef`

### Memory & Resource Management

- **RAII everywhere** — resources must be tied to object lifetimes
- Use `std::unique_ptr` as the default smart pointer; `std::shared_ptr` only when shared ownership is genuinely needed
- Use `std::make_unique` / `std::make_shared` — never raw `new`/`delete` in application code
- Raw pointers (`T*`) are for non-owning observation only — document this in comments
- Prefer value semantics (pass by value, return by value) — the compiler will elide copies

### Modern C++ Idioms

- Prefer `std::optional` over sentinel values, `std::variant` over type-unsafe unions
- Use `std::array` over C-style arrays, `std::string_view` over `const char*`
- Use range-based `for` loops — never index-based loops unless you need the index
- Use `[[nodiscard]]` on functions where ignoring the return value is likely a bug
- Use `noexcept` where appropriate (move constructors, swap, destructors)
- Prefer `if constexpr` over SFINAE when both work

### C++-Specific Teaching Notes

- Always explain the **compilation model** implications (header vs source, ODR, linkage)
- When discussing any feature, mention what C++ standard introduced it (C++11, C++14, C++17, C++20)
- Compare with C where relevant — explain what problem the C++ feature solves that C couldn't
- For memory-related topics, explain what happens **under the hood** (stack vs heap, vtable layout, move semantics as pointer swap)
- Use Mermaid diagrams for: memory layouts, object lifetime diagrams, class hierarchies, compilation pipeline stages
