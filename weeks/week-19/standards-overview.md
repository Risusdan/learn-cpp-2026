# C++ Standards Overview: C++11 through C++23

> Every three years, C++ evolves — understanding which standard introduced which feature tells you what you can use in your target compiler and why the language looks the way it does today.

## Table of Contents
- [Core Concepts](#core-concepts)
- [Code Examples](#code-examples)
- [Common Pitfalls](#common-pitfalls)
- [Key Takeaways](#key-takeaways)
- [Exercises](#exercises)

## Core Concepts

### Why Standards Matter

#### What

The C++ standard is a formal specification maintained by the ISO C++ committee (WG21). New standards are released on a roughly three-year cadence: C++11, C++14, C++17, C++20, C++23. Each standard adds language features, library additions, and sometimes deprecates or removes old features.

#### How

When you compile with `-std=c++17`, you're telling the compiler which standard to enforce. Features from later standards won't compile. Features from earlier standards are always available (the standard is backward-compatible). You can check your standard level at compile time with the `__cplusplus` macro: `201703L` for C++17, `202002L` for C++20, etc.

#### Why It Matters

In production, you don't always get to use the latest standard. Your project might target C++17 because that's what your CI pipeline, embedded toolchain, or customers' compilers support. Knowing *which* standard introduced *which* feature lets you:

1. **Write portable code** — don't accidentally use C++20 features in a C++17 codebase.
2. **Read legacy code** — understand why old code uses verbose patterns that modern standards simplify.
3. **Make informed upgrade decisions** — know exactly what you gain by moving from C++14 to C++17.

The standards build on each other. C++11 was a revolution (move semantics, lambdas, smart pointers). C++14 was a bug-fix release. C++17 brought practical quality-of-life improvements. C++20 introduced major features (Concepts, Ranges, Coroutines). C++23 polished what C++20 started.

### C++11 — The Modern C++ Revolution

#### What

C++11 (originally called C++0x) was the most transformative update in C++ history. It redefined how C++ is written. Before C++11, C++ was essentially "C with classes." After C++11, it became a genuinely modern language with expressive abstractions that carry zero runtime cost.

#### How

The headline features:

**Move semantics and rvalue references (`&&`)** solved the performance problem of returning large objects by value. Before C++11, returning a `std::vector` from a function meant copying the entire vector — a deep copy of every element. Move semantics let the compiler "steal" the internal resources (the heap-allocated buffer) from a temporary object instead of copying them. This turned expensive copies into cheap pointer swaps.

**Lambdas** (`[captures](params){ body }`) replaced verbose functor classes with inline closures. Before C++11, passing custom behavior to `std::sort` required defining a separate named class with `operator()`. Lambdas let you write the comparison logic at the call site.

**`auto` type deduction** lets the compiler infer variable types from their initializer. This eliminated verbose repetition like `std::map<std::string, std::vector<int>>::iterator it = m.begin()` — now just `auto it = m.begin()`.

**Smart pointers** (`std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`) moved memory management from convention ("remember to call delete") to the type system. `unique_ptr` has zero overhead compared to raw `new`/`delete`. These were covered in depth in Week 6.

**Range-based `for` loops** (`for (const auto& x : container)`) eliminated index-based iteration for the common "visit every element" pattern.

**`nullptr`** replaced the ambiguous `NULL` macro (which was just `0`) with a proper null pointer literal of type `std::nullptr_t`. This fixed overload resolution bugs where `NULL` could match an `int` parameter.

**`constexpr`** enabled compile-time computation — functions and variables that the compiler evaluates during compilation rather than at runtime.

**Variadic templates** (`template<typename... Args>`) allowed templates to accept any number of type parameters. This is the foundation for `std::tuple`, `std::make_unique`, and perfect forwarding with `std::forward`.

**`std::thread`** and `<mutex>`, `<condition_variable>`, `<atomic>` brought standardized multithreading to the language. Before C++11, threading was platform-specific (pthreads on Unix, Win32 threads on Windows).

#### Why It Matters

C++11 is the dividing line between "old C++" and "modern C++." Virtually every C++ style guide today assumes C++11 as the minimum. If you encounter pre-C++11 code, you'll see raw `new`/`delete`, verbose iterator loops, `NULL` macros, functor classes instead of lambdas, and `auto_ptr` (which was a broken precursor to `unique_ptr`, deprecated in C++11 and removed in C++17).

### C++14 — The Bug-Fix Release

#### What

C++14 was a minor update that polished C++11's rough edges. It added no revolutionary features but made existing ones significantly easier to use.

#### How

**`std::make_unique`** was the most-requested missing piece from C++11. C++11 added `make_shared` but forgot `make_unique`, so everyone had to write `std::unique_ptr<T>(new T(...))` instead of the cleaner `std::make_unique<T>(...)`.

**Generic lambdas** (`[](auto x, auto y) { return x + y; }`) let lambda parameters use `auto`, making them function templates without the verbosity of template syntax.

**Return type deduction** for normal functions — `auto f() { return 42; }` — the compiler deduces the return type from the `return` statement.

**Relaxed `constexpr`** — C++11's `constexpr` functions could only contain a single `return` statement. C++14 relaxed this to allow local variables, loops, and `if` statements in `constexpr` functions, making compile-time computation practical.

**Binary literals** (`0b1010`) and digit separators (`1'000'000`) improved readability for hardware-level code.

#### Why It Matters

C++14 is the "you should be using at least this" baseline for any greenfield project. It fixed enough C++11 papercuts that there is no reason to target C++11 over C++14 unless your compiler is truly ancient.

### C++17 — Practical Quality of Life

#### What

C++17 focused on making everyday C++ code cleaner and more expressive. It didn't redefine the language like C++11, but the cumulative effect of its features significantly reduces boilerplate.

#### How

**Structured bindings** (`auto [key, value] = *map.begin()`) let you unpack pairs, tuples, and struct members directly into named variables. Before this, you wrote `it->first` and `it->second` everywhere.

**`if constexpr`** enables compile-time branching inside templates. The "else" branch is *discarded* entirely — not just not executed, but not even compiled. This replaces many SFINAE patterns with straightforward `if`/`else` logic.

```cpp
template <typename T>
std::string stringify(const T& value) {
    if constexpr (std::is_arithmetic_v<T>) {
        return std::to_string(value);
    } else {
        return std::string(value);
    }
}
```

**`std::optional<T>`** represents a value that might not exist — a type-safe replacement for sentinel values like `-1`, `""`, or `nullptr`. It's the C++ equivalent of Rust's `Option<T>` or Haskell's `Maybe`.

**`std::variant<Types...>`** is a type-safe tagged union — it holds exactly one value from a fixed set of types. Combined with `std::visit`, it enables a form of pattern matching. It replaces the old pattern of `union` plus a manual discriminator.

**`std::string_view`** is a non-owning reference to a string — a pointer and a length. It's the efficient way to pass read-only strings without copying. It replaces both `const std::string&` and `const char*` in most parameter lists.

**`std::filesystem`** (from Boost.Filesystem) provides cross-platform file and directory operations — paths, directory iteration, file status, permissions.

**`[[nodiscard]]`** attribute causes a compiler warning if the return value of a function is ignored. Essential for functions like `empty()` where ignoring the result is almost certainly a bug (did you mean `clear()`?).

**`std::any`** is a type-erased container that can hold any single value. It's the type-safe replacement for `void*`. You extract the value with `std::any_cast`, which throws `std::bad_any_cast` on type mismatch.

**Fold expressions** simplify variadic template code by "folding" a parameter pack with an operator:

```cpp
template <typename... Args>
auto sum(Args... args) {
    return (args + ...);  // fold expression: a + b + c + ...
}
```

**Class template argument deduction (CTAD)** lets the compiler deduce template parameters from constructor arguments: `std::pair p{1, "hello"}` instead of `std::pair<int, const char*>{1, "hello"}`.

**Inline variables** (`static inline int count = 0;`) can be defined in headers without violating the One Definition Rule. This simplifies header-only libraries.

#### Why It Matters

C++17 is the sweet spot for production code today. It's widely supported by all major compilers (GCC 7+, Clang 5+, MSVC 2017+), offers major productivity improvements over C++14, and avoids the still-maturing ecosystem of C++20. If you're starting a new project and need broad compiler compatibility, C++17 is the pragmatic choice.

### C++20 — The Next Big Leap

#### What

C++20 is the second revolutionary standard after C++11. It adds four "big four" features that fundamentally change how C++ is written: Concepts, Ranges, Coroutines, and Modules. As of 2026, compiler support for C++20 is nearly complete, but Modules support still varies significantly across compilers and build systems.

#### How

**Concepts** constrain template parameters with readable, declarative syntax. Instead of cryptic SFINAE errors that span 200 lines, a concept violation gives you a clear one-line message: "type `Foo` does not satisfy concept `Sortable`."

Before Concepts:
```
// SFINAE: "enable this function only if T has operator<"
template <typename T, typename = std::enable_if_t</* complex trait */>>
void sort(T& container);
```

With Concepts:
```
template <std::sortable_range T>
void sort(T& container);
```

**Ranges** reimagine the STL around composable, lazy operations on sequences. Instead of `std::transform(v.begin(), v.end(), ...)`, you write `v | std::views::transform(fn) | std::views::filter(pred)`. The pipe syntax composes operations without creating intermediate containers.

**Coroutines** (`co_await`, `co_yield`, `co_return`) enable asynchronous programming and lazy generators as language-level features. A coroutine can suspend and resume execution, making async I/O code look sequential. The C++20 standard defines the *mechanism* but not the *library* — you need a coroutine library (like cppcoro) or your own coroutine types to use them practically.

**Modules** (`import std;`) replace the textual `#include` preprocessor model with a proper module system. Modules are compiled once and imported by name, dramatically reducing compilation times and eliminating header-related problems (include order dependencies, macro leakage, ODR violations from headers). In practice, module support is still uneven across compilers and build systems as of 2026.

**`std::format`** brings Python-style string formatting to C++: `std::format("Hello, {}! You are {} years old.", name, age)`. It's type-safe (unlike `printf`) and extensible (you can add formatters for custom types).

**Three-way comparison (`<=>`)** (the "spaceship operator") generates all six comparison operators (`<`, `>`, `<=`, `>=`, `==`, `!=`) from a single declaration.

#### Why It Matters

C++20 is where C++ catches up with ergonomic features that languages like Rust and Kotlin have had for years. Concepts eliminate the single biggest pain point in template programming (error messages). Ranges make functional-style composition natural. Modules will eventually solve the build-time problems that have plagued C++ since its inception. However, full adoption is still in progress — many production codebases remain on C++17 while tooling catches up.

### C++23 — Polishing C++20

#### What

C++23 is to C++20 what C++14 was to C++11: a refinement release that completes unfinished work and adds practical utilities. It shipped in 2023, and compiler support is rapidly improving.

#### How

**`std::expected<T, E>`** is a type that holds either a success value of type `T` or an error value of type `E`. It's the functional error-handling type — the C++ equivalent of Rust's `Result<T, E>`. Unlike exceptions, `expected` makes the error path explicit in the return type, and unlike error codes, it forces the caller to handle the error (you can't accidentally ignore it).

**`std::print` and `std::println`** bring formatted output that's both type-safe and fast. They combine the best of `printf` (compact syntax) and `iostream` (type safety): `std::println("x = {}, y = {}", x, y)`. They use `std::format` under the hood.

**Deducing `this`** allows member functions to explicitly name the `this` parameter and deduce its type. This eliminates the need to write `const` and non-`const` overloads of the same method, and enables CRTP-like patterns without templates:

```
struct Widget {
    template <typename Self>
    auto& name(this Self&& self) { return self.name_; }
    // One function handles: Widget&, const Widget&, Widget&&
};
```

**`std::flat_map` and `std::flat_set`** are associative containers backed by sorted vectors instead of balanced trees. They have better cache locality and lower memory overhead than `std::map` and `std::set` for small-to-medium collections. The trade-off: insertion and deletion are O(n) instead of O(log n), so they're best for read-heavy workloads with infrequent modifications.

**`std::generator`** is the standard library's coroutine-based lazy sequence generator. It provides the `co_yield` functionality that C++20 defined the mechanism for but didn't ship a ready-to-use type.

**`std::ranges::to`** converts a range pipeline into a concrete container: `auto vec = some_range | std::ranges::to<std::vector>()`. This was the most-requested missing piece from C++20's Ranges.

#### Why It Matters

C++23 fills the gaps that made C++20 feel incomplete. `std::expected` finally gives C++ a standard error-handling type that works with the type system rather than against it. `std::print` replaces `iostream` for output in new code. Deducing `this` is one of the most elegant additions — it eliminates an entire category of code duplication (const/non-const overloads) and opens the door to simpler CRTP-free mixin patterns.

## Code Examples

### C++11 Essentials — Move Semantics, Lambdas, Smart Pointers

```cpp
#include <algorithm>
#include <iostream>
#include <memory>
#include <string>
#include <vector>

// A resource-owning class that demonstrates move semantics.
// Moving "steals" the internal resources; copying is expensive.
class DataBuffer {
public:
    explicit DataBuffer(std::size_t size)
        : data_{std::make_unique<double[]>(size)}
        , size_{size}
    {
        std::fill(data_.get(), data_.get() + size, 0.0);
        std::cout << "  Constructed buffer of size " << size_ << '\n';
    }

    // Move constructor — steals the pointer, no allocation
    DataBuffer(DataBuffer&& other) noexcept
        : data_{std::move(other.data_)}
        , size_{other.size_}
    {
        other.size_ = 0;  // leave the source in a valid empty state
        std::cout << "  Moved buffer of size " << size_ << '\n';
    }

    // Move assignment
    DataBuffer& operator=(DataBuffer&& other) noexcept {
        if (this != &other) {
            data_ = std::move(other.data_);
            size_ = other.size_;
            other.size_ = 0;
        }
        return *this;
    }

    // Copy is expensive — deep copy of the heap-allocated array
    DataBuffer(const DataBuffer& other)
        : data_{std::make_unique<double[]>(other.size_)}
        , size_{other.size_}
    {
        std::copy(other.data_.get(), other.data_.get() + size_, data_.get());
        std::cout << "  Copied buffer of size " << size_ << " (expensive!)\n";
    }

    std::size_t size() const { return size_; }

    double& operator[](std::size_t i) { return data_[i]; }
    const double& operator[](std::size_t i) const { return data_[i]; }

private:
    std::unique_ptr<double[]> data_;
    std::size_t size_;
};

// Factory function returning by value — triggers move (or copy elision)
DataBuffer create_buffer(std::size_t size) {
    DataBuffer buf{size};
    buf[0] = 42.0;
    return buf;  // move semantics (or NRVO) — no deep copy
}

int main() {
    // --- Move semantics (C++11) ---
    std::cout << "=== Move Semantics ===\n";
    auto buf = create_buffer(1000);  // no copy — moved or elided
    std::cout << "buf[0] = " << buf[0] << ", size = " << buf.size() << "\n\n";

    // --- Lambdas (C++11) ---
    std::cout << "=== Lambdas ===\n";
    std::vector<int> numbers = {5, 2, 8, 1, 9, 3};

    // Sort with a lambda — inline comparator, no need for a functor class
    std::sort(numbers.begin(), numbers.end(),
              [](int a, int b) { return a > b; });  // descending

    // Range-based for loop (C++11) with const reference
    std::cout << "Sorted descending: ";
    for (const auto& n : numbers) {
        std::cout << n << ' ';
    }
    std::cout << '\n';

    // Lambda capturing by reference — modifies external state
    int even_count = 0;
    std::for_each(numbers.begin(), numbers.end(),
                  [&even_count](int n) {
                      if (n % 2 == 0) ++even_count;
                  });
    std::cout << "Even numbers: " << even_count << "\n\n";

    // --- Smart pointers (C++11) ---
    std::cout << "=== Smart Pointers ===\n";
    auto ptr = std::make_unique<std::string>("hello from unique_ptr");
    std::cout << *ptr << '\n';
    // ptr is automatically deleted when main() returns

    // --- nullptr (C++11) ---
    int* raw = nullptr;  // unambiguous null — not 0, not NULL
    if (raw == nullptr) {
        std::cout << "raw is null (using nullptr, not NULL)\n";
    }

    return 0;
}
```

### C++14 Additions — make_unique, Generic Lambdas, Relaxed constexpr

```cpp
#include <algorithm>
#include <iostream>
#include <memory>
#include <string>
#include <vector>

// --- Relaxed constexpr (C++14) ---
// C++11 constexpr functions could only have a single return statement.
// C++14 allows loops, local variables, and if statements.
constexpr int fibonacci(int n) {
    if (n <= 1) return n;
    int a = 0, b = 1;
    for (int i = 2; i <= n; ++i) {
        const int temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}

// Return type deduction (C++14) — the compiler infers the return type
auto make_greeting(std::string_view name) {
    return "Hello, " + std::string{name} + "!";
}

int main() {
    // --- std::make_unique (C++14) ---
    // C++11 had make_shared but forgot make_unique — fixed in C++14
    auto widget = std::make_unique<std::string>("widget data");
    std::cout << "make_unique: " << *widget << '\n';

    // --- Generic lambdas (C++14) ---
    // Lambda parameters can use 'auto', making the lambda a template
    auto print_any = [](const auto& value) {
        std::cout << "Value: " << value << '\n';
    };
    print_any(42);               // auto = int
    print_any(3.14);             // auto = double
    print_any("hello");          // auto = const char*
    print_any(std::string{"world"});  // auto = std::string

    // Generic lambda for comparison — works with any comparable type
    auto greater = [](const auto& a, const auto& b) { return a > b; };
    std::vector<double> vals = {3.1, 1.4, 2.7, 0.5};
    std::sort(vals.begin(), vals.end(), greater);

    std::cout << "Sorted: ";
    for (const auto& v : vals) std::cout << v << ' ';
    std::cout << '\n';

    // --- constexpr with loops (C++14) ---
    constexpr int fib10 = fibonacci(10);  // computed at compile time
    static_assert(fib10 == 55, "fibonacci(10) must be 55");
    std::cout << "fibonacci(10) = " << fib10 << " (compile-time)\n";

    // --- Return type deduction (C++14) ---
    std::cout << make_greeting("C++14") << '\n';

    // --- Binary literals and digit separators (C++14) ---
    constexpr int flags = 0b1010'0101;       // binary literal with separator
    constexpr int million = 1'000'000;       // decimal with separators
    constexpr int hex_color = 0xFF'AA'33;    // hex with separators
    std::cout << "flags = " << flags << ", million = " << million
              << ", color = " << hex_color << '\n';

    return 0;
}
```

### C++17 Features — Structured Bindings, if constexpr, optional, variant

```cpp
#include <algorithm>
#include <cmath>
#include <iostream>
#include <map>
#include <optional>
#include <string>
#include <string_view>
#include <variant>
#include <vector>

// --- if constexpr (C++17) ---
// Compile-time branching: the discarded branch is NOT compiled at all.
// This replaces many SFINAE patterns with simple if/else logic.
template <typename T>
std::string to_string_safe(const T& value) {
    if constexpr (std::is_arithmetic_v<T>) {
        return std::to_string(value);
    } else if constexpr (std::is_same_v<T, std::string>) {
        return value;
    } else if constexpr (std::is_same_v<T, const char*> ||
                         std::is_same_v<T, std::string_view>) {
        return std::string{value};
    } else {
        static_assert(sizeof(T) == 0, "Unsupported type for to_string_safe");
    }
}

// --- std::optional (C++17) ---
// Represents a value that might not exist — replaces sentinel values like -1
std::optional<double> safe_sqrt(double x) {
    if (x < 0.0) {
        return std::nullopt;  // no value — negative input
    }
    return std::sqrt(x);
}

// --- std::variant (C++17) ---
// Type-safe tagged union — holds one of several types, tracks which is active
using JsonValue = std::variant<int, double, std::string, bool>;

void print_json(const JsonValue& value) {
    // std::visit applies a visitor to the active alternative
    std::visit([](const auto& v) {
        using T = std::decay_t<decltype(v)>;
        if constexpr (std::is_same_v<T, bool>) {
            std::cout << (v ? "true" : "false");
        } else if constexpr (std::is_same_v<T, std::string>) {
            std::cout << '"' << v << '"';
        } else {
            std::cout << v;
        }
    }, value);
    std::cout << '\n';
}

// --- Fold expressions (C++17) ---
// Fold a parameter pack with an operator — replaces recursive template tricks
template <typename... Args>
auto sum(Args... args) {
    return (args + ...);  // unary right fold: a + (b + (c + ...))
}

template <typename... Args>
void print_all(const Args&... args) {
    // Binary left fold with comma operator — prints each argument
    ((std::cout << args << ' '), ...);
    std::cout << '\n';
}

int main() {
    // --- Structured bindings (C++17) ---
    // Unpack pairs, tuples, and structs into named variables
    std::map<std::string, int> scores = {
        {"Alice", 95}, {"Bob", 87}, {"Charlie", 92}
    };

    std::cout << "=== Structured Bindings ===\n";
    for (const auto& [name, score] : scores) {
        // 'name' is const std::string&, 'score' is const int&
        std::cout << name << ": " << score << '\n';
    }

    // Structured binding with insert result
    auto [iter, inserted] = scores.insert({"Dave", 88});
    std::cout << "Inserted Dave: " << std::boolalpha << inserted << "\n\n";

    // --- if constexpr ---
    std::cout << "=== if constexpr ===\n";
    std::cout << to_string_safe(42) << '\n';
    std::cout << to_string_safe(3.14) << '\n';
    std::cout << to_string_safe(std::string{"hello"}) << '\n';
    std::cout << to_string_safe("world") << "\n\n";

    // --- std::optional ---
    std::cout << "=== std::optional ===\n";
    if (auto result = safe_sqrt(16.0); result.has_value()) {
        // 'if' with initializer (C++17) — result is scoped to this if/else
        std::cout << "sqrt(16) = " << *result << '\n';
    }
    if (auto result = safe_sqrt(-1.0); !result) {
        std::cout << "sqrt(-1) = no value (negative input)\n";
    }

    // value_or provides a default when the optional is empty
    const auto val = safe_sqrt(-4.0).value_or(0.0);
    std::cout << "sqrt(-4) with default: " << val << "\n\n";

    // --- std::variant ---
    std::cout << "=== std::variant ===\n";
    std::vector<JsonValue> json_data = {42, 3.14, std::string{"hello"}, true};
    for (const auto& v : json_data) {
        std::cout << "  ";
        print_json(v);
    }
    std::cout << '\n';

    // --- Fold expressions ---
    std::cout << "=== Fold Expressions ===\n";
    std::cout << "sum(1,2,3,4,5) = " << sum(1, 2, 3, 4, 5) << '\n';
    std::cout << "sum(1.1, 2.2, 3.3) = " << sum(1.1, 2.2, 3.3) << '\n';
    std::cout << "print_all: ";
    print_all(1, "hello", 3.14, true);

    // --- [[nodiscard]] (C++17) ---
    // When applied to a function, the compiler warns if the return value is ignored.
    // Standard library functions like std::vector::empty() use this.
    // If you write: scores.empty();  // compiler warns: ignoring nodiscard

    // --- Class template argument deduction (CTAD) (C++17) ---
    std::pair p{42, "hello"};  // deduced as std::pair<int, const char*>
    std::cout << "\nCTAD pair: {" << p.first << ", " << p.second << "}\n";

    return 0;
}
```

### C++17 — string_view and inline Variables

```cpp
#include <iostream>
#include <string>
#include <string_view>

// --- std::string_view (C++17) ---
// A non-owning view into a string. Zero-copy. Replaces both
// "const std::string&" and "const char*" in most parameter positions.

// This function works with std::string, const char*, string literals,
// and substrings — all without copying a single byte.
bool starts_with_hello(std::string_view sv) {
    // string_view operations: substr, find, starts_with (C++20), etc.
    return sv.size() >= 5 && sv.substr(0, 5) == "hello";
}

// Count occurrences of a character — operates on any string-like thing
std::size_t count_char(std::string_view sv, char c) {
    std::size_t count = 0;
    for (const char ch : sv) {
        if (ch == c) ++count;
    }
    return count;
}

// --- Inline variables (C++17) ---
// Can be defined in headers without violating ODR.
// Useful for constants in header-only libraries.
struct Config {
    static inline const std::string DEFAULT_HOST = "localhost";
    static inline constexpr int DEFAULT_PORT = 8080;
    static inline constexpr int MAX_CONNECTIONS = 1024;
};

int main() {
    // string_view from a string literal — no allocation
    std::cout << "=== std::string_view ===\n";
    std::cout << starts_with_hello("hello world") << '\n';  // 1 (true)
    std::cout << starts_with_hello("goodbye") << '\n';       // 0 (false)

    // string_view from a std::string — no copy
    const std::string greeting = "hello, C++17";
    std::cout << starts_with_hello(greeting) << '\n';  // 1

    // string_view from a substring — still no copy, just pointer + length
    std::string_view sub = std::string_view{greeting}.substr(7);  // "C++17"
    std::cout << "Substring: " << sub << '\n';

    std::cout << "Count 'l' in greeting: "
              << count_char(greeting, 'l') << '\n';  // 2

    // Inline variables
    std::cout << "\n=== Inline Variables ===\n";
    std::cout << "Host: " << Config::DEFAULT_HOST << '\n';
    std::cout << "Port: " << Config::DEFAULT_PORT << '\n';
    std::cout << "Max connections: " << Config::MAX_CONNECTIONS << '\n';

    return 0;
}
```

### C++11 Variadic Templates — the Foundation for Modern APIs

```cpp
#include <iostream>
#include <memory>
#include <string>
#include <sstream>

// --- Variadic templates (C++11) ---
// Templates that accept any number of type parameters.
// The parameter pack "..." expands recursively or with fold expressions (C++17).

// Base case: no arguments left
void log_impl(std::ostringstream& oss) {
    // Nothing to do — all arguments have been consumed
}

// Recursive case: process the first argument, recurse on the rest
template <typename T, typename... Rest>
void log_impl(std::ostringstream& oss, const T& first, const Rest&... rest) {
    oss << first;
    if constexpr (sizeof...(rest) > 0) {
        oss << " | ";
    }
    log_impl(oss, rest...);  // unpack rest into separate arguments
}

// Public API: accepts any number of arguments of any type
template <typename... Args>
void log(const Args&... args) {
    std::ostringstream oss;
    oss << "[LOG] ";
    log_impl(oss, args...);
    std::cout << oss.str() << '\n';
}

// --- Perfect forwarding (C++11) ---
// std::forward preserves the value category (lvalue vs rvalue) of arguments.
// This is how std::make_unique is implemented.
template <typename T, typename... Args>
std::unique_ptr<T> my_make_unique(Args&&... args) {
    // std::forward<Args>(args)... forwards each argument with its original
    // value category — lvalues stay lvalues, rvalues stay rvalues.
    return std::unique_ptr<T>(new T(std::forward<Args>(args)...));
}

struct Sensor {
    std::string name;
    int id;
    double threshold;

    Sensor(std::string n, int i, double t)
        : name{std::move(n)}, id{i}, threshold{t}
    {
        std::cout << "Sensor constructed: " << name << '\n';
    }
};

int main() {
    // Variadic log function — any number of any types
    log("Server started", "port", 8080);
    log("Request from", "192.168.1.1", "took", 42, "ms");
    log("Single argument");
    log(1, 2.0, "three", '4');

    // Perfect forwarding — arguments are forwarded to Sensor's constructor
    auto sensor = my_make_unique<Sensor>("thermocouple", 7, 98.6);
    std::cout << "Sensor: " << sensor->name << " #" << sensor->id << '\n';

    return 0;
}
```

## Common Pitfalls

### Dangling `string_view` from a temporary

```cpp
// BAD — string_view pointing to a destroyed temporary
#include <iostream>
#include <string>
#include <string_view>

std::string_view get_name() {
    std::string name = "temporary";
    return name;  // DANGLING: string_view points to 'name', which is destroyed
}

int main() {
    std::string_view sv = get_name();
    std::cout << sv << '\n';  // UNDEFINED BEHAVIOR — dangling reference
    return 0;
}
```

`string_view` is non-owning — it doesn't keep the underlying string alive. When the temporary `std::string` is destroyed at the end of `get_name()`, the `string_view` becomes a dangling pointer. This is the most common `string_view` bug.

```cpp
// GOOD — return std::string when the data must outlive the function
#include <iostream>
#include <string>
#include <string_view>

std::string get_name() {
    std::string name = "temporary";
    return name;  // return by value — move semantics, no copy
}

// string_view is safe when it refers to data that outlives it
void print_name(std::string_view name) {
    std::cout << name << '\n';  // safe: caller owns the string
}

int main() {
    const std::string name = get_name();
    print_name(name);  // string_view borrows from 'name', which is alive
    return 0;
}
```

### Using `std::optional` without checking

```cpp
// BAD — accessing an empty optional
#include <optional>
#include <iostream>

std::optional<int> find_value() {
    return std::nullopt;
}

int main() {
    auto result = find_value();
    int value = *result;  // UNDEFINED BEHAVIOR — dereferencing empty optional
    std::cout << value << '\n';
    return 0;
}
```

Dereferencing an empty `optional` is undefined behavior — it doesn't throw, it doesn't return zero, it's UB. Always check `has_value()` or use `value_or()`.

```cpp
// GOOD — always check before accessing
#include <optional>
#include <iostream>

std::optional<int> find_value() {
    return std::nullopt;
}

int main() {
    auto result = find_value();

    // Option 1: check with has_value() or bool conversion
    if (result) {
        std::cout << "Found: " << *result << '\n';
    } else {
        std::cout << "Not found\n";
    }

    // Option 2: provide a default with value_or()
    int value = result.value_or(-1);
    std::cout << "Value (with default): " << value << '\n';

    // Option 3: use .value() which throws std::bad_optional_access if empty
    try {
        int v = result.value();
        std::cout << v << '\n';
    } catch (const std::bad_optional_access&) {
        std::cout << "Caught bad_optional_access\n";
    }

    return 0;
}
```

### Confusing `if constexpr` with regular `if`

```cpp
// BAD — using regular 'if' where 'if constexpr' is needed
#include <iostream>
#include <string>
#include <type_traits>

template <typename T>
std::string to_string_broken(const T& value) {
    // Regular 'if' — BOTH branches must compile for every T
    if (std::is_arithmetic_v<T>) {
        return std::to_string(value);  // ERROR when T = std::string
    } else {
        return value;                  // ERROR when T = int
    }
}
```

With regular `if`, both branches are compiled even if the condition is known at compile time. When `T = std::string`, `std::to_string(value)` won't compile because `to_string` doesn't accept `std::string`. With `if constexpr`, the compiler *discards* the false branch entirely.

```cpp
// GOOD — if constexpr discards the false branch at compile time
#include <iostream>
#include <string>
#include <type_traits>

template <typename T>
std::string to_string_fixed(const T& value) {
    if constexpr (std::is_arithmetic_v<T>) {
        return std::to_string(value);  // only compiled when T is arithmetic
    } else {
        return value;                  // only compiled when T is not arithmetic
    }
}

int main() {
    std::cout << to_string_fixed(42) << '\n';
    std::cout << to_string_fixed(std::string{"hello"}) << '\n';
    return 0;
}
```

### Misunderstanding `std::variant` and `std::get`

```cpp
// BAD — using std::get with the wrong type throws at runtime
#include <iostream>
#include <string>
#include <variant>

int main() {
    std::variant<int, std::string> v = std::string{"hello"};

    // This THROWS std::bad_variant_access because v holds a string, not an int
    int value = std::get<int>(v);  // runtime exception!
    std::cout << value << '\n';
    return 0;
}
```

`std::get<T>` throws `std::bad_variant_access` if the variant doesn't currently hold type `T`. Use `std::get_if` (which returns a pointer, `nullptr` on mismatch) or `std::visit` (which is always type-safe).

```cpp
// GOOD — use std::get_if or std::visit
#include <iostream>
#include <string>
#include <variant>

int main() {
    std::variant<int, std::string> v = std::string{"hello"};

    // Option 1: std::get_if returns nullptr if the type doesn't match
    if (const auto* str_ptr = std::get_if<std::string>(&v)) {
        std::cout << "String: " << *str_ptr << '\n';
    } else {
        std::cout << "Not a string\n";
    }

    // Option 2: std::visit — always handles all alternatives
    std::visit([](const auto& val) {
        std::cout << "Visit: " << val << '\n';
    }, v);

    // Option 3: check the index before getting
    if (v.index() == 1) {  // 1 = std::string (0-indexed in the type list)
        std::cout << "By index: " << std::get<1>(v) << '\n';
    }

    return 0;
}
```

## Key Takeaways

- **C++11 is the baseline** for modern C++. Move semantics, lambdas, smart pointers, `auto`, range-based `for`, `nullptr`, and `constexpr` are non-negotiable in any modern codebase. If you're writing pre-C++11 patterns (raw `new`/`delete`, `NULL`, verbose iterators), stop.
- **C++17 is the pragmatic production standard** as of 2026. Structured bindings, `if constexpr`, `std::optional`, `std::variant`, `std::string_view`, fold expressions, and CTAD collectively eliminate massive amounts of boilerplate. It's widely supported and stable.
- **C++20 introduces paradigm shifts** — Concepts, Ranges, Coroutines, and Modules — but full toolchain support is still maturing. Use C++20 features when your entire toolchain supports them; otherwise, mention them as forward-looking in C++17 codebases.
- **C++23 fills C++20's gaps**: `std::expected` for error handling, `std::print` for output, deducing `this` for reducing code duplication, and `std::flat_map` for cache-friendly associative containers.
- **Know your target standard.** Before using any feature, verify it's available in your project's `-std=c++NN` setting. Using a C++20 feature in a C++17 codebase is a build failure, not a style issue.

## Exercises

1. **Standard identification.** For each of the following features, name the C++ standard that introduced it: (a) `auto` type deduction, (b) `std::make_unique`, (c) structured bindings, (d) Concepts, (e) `std::expected`, (f) generic lambdas, (g) fold expressions, (h) `std::string_view`.

2. **Migration exercise.** Rewrite the following pre-C++11 code using modern C++ features. Identify every feature you use and which standard introduced it:
   ```cpp
   #include <vector>
   #include <algorithm>
   #include <iostream>
   using namespace std;
   struct Compare {
       bool operator()(int a, int b) const { return a > b; }
   };
   int main() {
       vector<int>* v = new vector<int>();
       v->push_back(5); v->push_back(2); v->push_back(8);
       sort(v->begin(), v->end(), Compare());
       for (vector<int>::iterator it = v->begin(); it != v->end(); ++it) {
           cout << *it << " ";
       }
       cout << endl;
       delete v;
       return 0;
   }
   ```

3. **`if constexpr` vs SFINAE.** Write a function template `serialize(const T& value)` that returns a `std::string`. If `T` is arithmetic, return `std::to_string(value)`. If `T` is a `std::string`, return the value directly. If `T` is a `std::vector<int>`, return a comma-separated list of elements. Implement it twice: once using SFINAE (`std::enable_if`), and once using `if constexpr`. Compare the readability and code complexity.

4. **`std::variant` state machine.** Model a simple connection state machine using `std::variant`. The states are: `Disconnected` (no data), `Connecting` (holds a hostname string), `Connected` (holds an IP string and a port int), `Error` (holds an error message string). Write a `describe_state` function using `std::visit` that prints a human-readable description of each state. Show how to transition between states by assigning different alternatives to the variant.

5. **Feature trade-offs.** You are choosing between C++17 and C++20 for a new embedded firmware project. The compiler (arm-none-eabi-g++ 12) supports C++20, but the rest of your team has never used Concepts or Ranges. Write a brief argument (3-5 sentences) for each side. Which would you choose, and why?

---
up:: [Schedule](../../Schedule.md)
#type/learning #source/self-study #status/evergreen
