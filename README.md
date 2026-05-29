# C++ — Things Worth Writing About

A personal scratchpad of interesting C++ changes, features, and moments that are genuinely worth talking about. Stuff I want to turn into blog posts someday.

---

## 🪵 Logging Is *Finally* Good in C++ (C++23 `std::print` & `std::println`)

For **decades**, C++ developers had two bad options for output: `printf` (fast but type-unsafe, zero extensibility) and `std::cout` (type-safe but painfully verbose with `<<` chaining). The community basically accepted this and moved on.

Then `{fmt}` happened. Victor Zverovich's library showed what modern, type-safe, fast formatting *could* look like. It was so good that it got standardized:

- **C++20** → `std::format` landed. Python-style `{}` placeholders, fully type-safe, extensible for custom types. No more `<<` soup.
- **C++23** → `std::print` and `std::println` arrived. You can now just write `std::println("Hello, {}!", name);` and it *works*. Clean. Simple. Done.

It took C++ roughly **40 years** to get a proper print function. But honestly? It was worth the wait — the design is genuinely excellent.

---

## 🔥 Modules Might Actually Kill `#include` (C++20)

The `#include` system is arguably the single worst thing about C++. Textual inclusion, header guards, include order dependencies, insane compile times — it's a mess inherited from C in the 1970s.

C++20 introduced **modules** (`import std;`, `export module mylib;`). In theory, this fixes everything: proper encapsulation, dramatically faster builds, no more header/source split if you don't want it.

In practice? Compiler support is *still* catching up in 2026. MSVC is ahead, GCC and Clang are getting there. Build system support (CMake etc.) is the real bottleneck. But when this fully lands, it'll be the biggest quality-of-life improvement in C++ history.

---

## ⚡ `constexpr` Has Gone Absolutely Wild

`constexpr` started in C++11 as "you can compute simple stuff at compile time." Look at where it is now:

- **C++14** → Loops and local variables in constexpr functions
- **C++17** → `if constexpr` for compile-time branching (killed a ton of SFINAE hacks)
- **C++20** → `constexpr std::vector`, `constexpr std::string`, `consteval` (forced compile-time), `constinit`
- **C++23** → Even more STL constexpr support

You can now write a JSON parser that runs entirely at compile time. That's not a joke. The line between "runtime code" and "compile-time metaprogramming" is basically gone.

---

## 🦀 C++ Is Taking Memory Safety Seriously (Finally)

Rust's popularity forced a conversation the C++ world had been avoiding. The US government literally published guidance saying "stop using memory-unsafe languages." That's a wake-up call.

What's happening now:
- **Profiles** (Bjarne Stroustrup's proposal) — Compiler-enforced subsets of C++ that guarantee safety properties (lifetime safety, type safety, etc.) without breaking existing code
- **`std::expected`** (C++23) — A proper Result type, so you can stop using exceptions or error codes for recoverable errors
- **Lifetime annotations** — Various proposals exploring Rust-like borrow checking adapted for C++
- **Sean Baxter's Circle compiler** — A C++ compiler fork with first-class Rust-style safety. Wild experiment, but it shows what's possible.

C++ isn't going to become Rust. But it's clearly evolving to close the gap.

---

## 🧵 Coroutines Exist But Nobody Agrees on How to Use Them (C++20)

C++20 added coroutines (`co_await`, `co_yield`, `co_return`). The feature itself is powerful — stackless coroutines with compiler-generated state machines.

The problem? The standard only provides the *machinery*, not ready-to-use types. You need to write your own `Task<T>`, `Generator<T>`, etc. or pull in a library. It's like shipping an engine without a car.

Libraries filling the gap:
- **cppcoro** (Lewis Baker) — The de facto community library
- **libunifex** (Facebook) — Senders/receivers based async
- **stdexec** — The proposed `std::execution` framework (targeting C++26)

C++26's `std::execution` might finally give us a batteries-included async story. Until then, coroutines are powerful but require investment to use.

---

## 📦 Pattern Matching Is Coming (Maybe C++26/29)

`std::visit` with `std::variant` is functional but ugly. The pattern matching proposal (`inspect`) would let you write:

```cpp
inspect (v) {
    <int> i    => std::println("int: {}", i);
    <string> s => std::println("string: {}", s);
    __         => std::println("something else");
};
```

This has been in the works for years. If it lands, it'll make working with variants, polymorphism, and structured data *dramatically* cleaner. It's one of the most wanted features in the community.

---

## 🧹 C++23's Small but Brilliant Additions

Sometimes the best features aren't the flashy ones:

- **`std::mdspan`** — Multidimensional array view. NumPy-style indexing for C++. Scientists and game devs rejoice.
- **`std::flat_map` / `std::flat_set`** — Cache-friendly sorted containers backed by contiguous memory. Drop-in replacement for `std::map` when you want performance.
- **`std::generator`** — *Finally* a standard coroutine generator type. `co_yield` just works out of the box.
- **Deducing `this`** — Eliminates the need to write const and non-const overloads. One of those "why didn't we have this before" features.
- **`std::stacktrace`** — Print a stack trace without platform-specific hacks. Debugging just got easier.
- **`#warning`** — Standardized compiler warnings. Yes, it took until 2023.

---

## 🗓️ What's On the Horizon (C++26 and Beyond)

- **`std::execution`** — A complete async/parallel framework. The big one for C++26.
- **Reflection** — Compile-time introspection of types. Would enable automatic serialization, ORM, debug printing, etc. This alone could eliminate massive amounts of boilerplate.
- **Contracts** — Pre/post conditions and assertions, checked at compile or runtime. Was in C++20, got pulled, might return in C++26.
- **`std::hive`** — A colony/bucket-array container for stable-address, cache-friendly storage with fast iteration and O(1) insert/erase. Great for game entities, ECS, etc.
- **Linear algebra (`std::linalg`)** — BLAS-style linear algebra in the standard library.

---

*This repo exists to collect interesting C++ developments for future blog posts. No code, just notes on things worth writing about.*
