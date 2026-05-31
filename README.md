# C++ — Things Worth Writing About

A personal scratchpad of interesting C++ changes, features, and moments that are genuinely worth talking about. Stuff I want to turn into blog posts someday.

---

## 🖨️ Logging Is *Finally* Good in C++ (C++23 `std::print` & `std::println`)

If you've written C++ for even a week, you've used `cout`. It works. But it's ugly. And there's something better now.
Let me explain the whole story in 2 minutes.

### The Old Days: `printf`

`printf` came from C. You've probably seen it:

```cpp
printf("Hello, %s! You are %d years old.", name, age);
```

Looks clean right? The problem — if you write `%d` but pass a string, your program crashes or does something weird. The compiler won't even warn you. That's called **undefined behavior** and it's scary.

### Then Came `cout`

`cout` fixed the safety problem. But look at what it did to our code:

```cpp
cout << "Hello, " << name << "! You are " << age << " years old." << endl;
```

Bro. That's just painful to read and write. Every time you add something you need another `<<`. Imagine doing this for 5 variables.

### C++20 Fixed It: `std::format`

C++20 gave us `std::format`. Think Python's f-strings but for C++:

```cpp
string msg = std::format("Hello, {}! You are {} years old.", name, age);
```

Just use `{}` as a placeholder. Type safe. Clean. No `<<` anywhere.

### C++23 Made It Even Better: `std::println`

C++23 said why even store it in a string first. Just print directly:

```cpp
std::println("Hello, {}!", name);
```

One line. Done. That's it.

### So What Should You Use in CP Right Now?

Honestly — `cout` still works fine for competitive programming and most judges support it. But if your compiler supports C++20 or C++23, `std::format` and `std::println` are genuinely nicer to write especially for debugging output.

Try this next time you want to debug multiple variables:

```cpp
std::println("a={} b={} sum={}", a, b, a+b);
```

Way cleaner than:

```cpp
cout << "a=" << a << " b=" << b << " sum=" << a+b << endl;
```

> C++ took 40 years to get a proper print function. But hey, better late than never.

---

## 📦 Modules Might Actually Kill `#include` (C++20)

### The Worst Thing About C++ Is Finally Getting Fixed

Every C++ programmer has seen this at the top of their file:

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
```

You don't think about it. You just type it. But have you ever wondered *why* we do this and why it makes compilation so slow?

### What `#include` Actually Does

Here's the dirty secret. `#include` doesn't *import* anything. It literally **copy-pastes the entire header file** into your code.

**Every. Single. Time. You. Compile.**

So when you write `#include <iostream>`, the compiler is pasting thousands of lines of code into your file before it even looks at your actual code. Do this with 10 headers across 50 files and you understand why C++ projects take forever to compile.

This system was designed in the 1970s. For C. We just never got rid of it.

### The Header Guard Nightmare

Ever seen this?

```cpp
#ifndef MYFILE_H
#define MYFILE_H

// your code

#endif
```

This exists purely because of `#include`. Without it, if two files both include the same header, your code breaks. So every header needs this ugly boilerplate just to protect itself from being copy-pasted twice.

It's a hack on top of a hack.

### C++20 Introduced Modules

Instead of `#include`, you now write:

```cpp
import std;
```

That's it. No copy-pasting. No header guards. No include order drama. The compiler reads the module once, compiles it once, and reuses it everywhere.

The difference in compile time is **massive** on large projects.

You can also create your own modules:

```cpp
export module mylib;

export int add(int a, int b) {
    return a + b;
}
```

And use it like:

```cpp
import mylib;
```

Clean. Modern. Actually makes sense.

### The Catch

It's 2026 and compiler support is still catching up. MSVC handles it best right now. GCC and Clang are getting there. The real problem is build systems like CMake — they weren't designed for this and full support is still a work in progress.

So for competitive programming — stick with `#include` for now. It just works everywhere.

> But for real projects? Modules are the future. And the future is almost here.

---

## ⚡ `constexpr` Has Gone Absolutely Wild

### From Simple Constants to Compile-Time Superpowers

You've probably seen `constexpr` in C++ code and thought "okay it's just a constant." That's what I thought too.

**It's so much more than that now.**

### What Does Compile Time Even Mean?

Quick background first.

Your program has two moments in its life. **Compile time** is when your code is being turned into an executable. **Runtime** is when that executable is actually running.

Normally all your logic — loops, calculations, function calls — happens at runtime. The user runs your program, stuff executes.

`constexpr` says: *"hey compiler, figure this out NOW during compilation so we don't have to do it when the program runs."*

Result? Faster programs. Zero runtime cost for that computation.

### C++11 — Baby Steps

When `constexpr` first arrived you could only do really simple stuff:

```cpp
constexpr int square(int x) {
    return x * x;  // that's it, one line only
}
```

Useful but very limited. No loops. No local variables. Just simple expressions.

### C++14 — Now We're Talking

C++14 unlocked loops and local variables inside `constexpr` functions:

```cpp
constexpr int factorial(int n) {
    int result = 1;
    for (int i = 2; i <= n; i++)
        result *= i;
    return result;
}

constexpr int val = factorial(5);  // computed at compile time, result is 120
```

The compiler calculates `factorial(5)` while compiling. By the time your program runs, `val` is already `120` baked into the binary.

### C++17 — `if constexpr` Changed Everything

This one is huge especially for templates:

```cpp
template<typename T>
void print(T val) {
    if constexpr (std::is_integral_v<T>)
        std::println("Integer: {}", val);
    else
        std::println("Other: {}", val);
}
```

The compiler picks the right branch at compile time and **throws away the other one completely**. Before this, people wrote horrible unreadable template tricks to do the same thing. `if constexpr` killed all of that.

### C++20 — Okay This Is Getting Wild

C++20 made `std::vector` and `std::string` `constexpr`. Meaning you can use them at compile time:

```cpp
constexpr auto get_data() {
    std::vector<int> v = {1, 2, 3, 4, 5};
    return v.size();
}

constexpr auto size = get_data();  // compiler figures this out
```

Also added `consteval` — if `constexpr` is *"try to run at compile time,"* `consteval` is *"I am FORCING you to run at compile time or I won't compile"*:

```cpp
consteval int must_be_compile_time(int x) {
    return x * x;
}
```

Call this with a runtime value and it's a compile error. Strict but useful.

### Where This Is Going

People have written **JSON parsers that run entirely at compile time**. The parser reads your JSON string during compilation and produces the data structure before your program even starts.

That's not a joke. That's real C++23 code people are writing today.

> The line between "code that runs" and "code the compiler runs" is basically disappearing.

For CP you probably won't use advanced `constexpr` much. But understanding it makes you a better C++ programmer — and honestly it's just cool to know your language can do this.

---

## 🛡️ C++ Is Taking Memory Safety Seriously (Finally)

### C++ Has a Memory Problem — And It's Finally Doing Something About It

Let me tell you something that might surprise you.

A lot of real world security vulnerabilities — the kind that leak your passwords and crash systems — come from **memory bugs** in C and C++ code. Buffer overflows. Use-after-free. Dangling pointers.

These bugs are so common that the **US government** literally published official guidance in 2024 saying *"stop writing new code in memory-unsafe languages."*

That's a wake-up call when your language is on the list.

### What Is a Memory Safety Bug?

Quick example. In C++ you can do this:

```cpp
int* ptr = new int(42);
delete ptr;
cout << *ptr;  // using memory after freeing it
```

This compiles. No error. But at runtime your program might crash, print garbage, or worse — silently corrupt data. The compiler just lets you do it.

**Rust** was built specifically to make this impossible. The compiler physically won't let you write code like this. If it compiles in Rust, it's memory safe. Guaranteed.

C++ can't make that guarantee. And for decades it just accepted that as a tradeoff for flexibility and performance.

### Then Rust Got Popular

Rust proved that you can have memory safety without sacrificing performance. Suddenly the C++ world couldn't ignore the conversation anymore.

So what is C++ doing about it?

### Profiles — Safety Without Rewriting Everything

Bjarne Stroustrup — the guy who invented C++ — proposed something called **Profiles**.

The idea is simple. Instead of forcing everyone to rewrite their code, you opt into a safety profile. The compiler then enforces rules within that profile — no dangling pointers, no type confusion, no lifetime errors.

Your existing code still works. You just add a layer of enforced safety on top.

It's not as strict as Rust. But it's practical for the millions of lines of C++ that already exist.

### `std::expected` — Safer Error Handling (C++23)

Another memory safety related problem — exceptions and error codes are both messy ways to handle errors.

C++23 introduced `std::expected`:

```cpp
std::expected<int, string> divide(int a, int b) {
    if (b == 0)
        return std::unexpected("division by zero");
    return a / b;
}

auto result = divide(10, 0);
if (!result)
    std::println("Error: {}", result.error());
```

Your function either returns a value or an error. The caller is **forced** to handle both. No crashes from unhandled exceptions. No ignoring error codes.

> This pattern is directly inspired by Rust's `Result` type.

### Will C++ Become Rust?

**No. And it doesn't need to.**

C++ has decades of existing code, libraries, and expertise behind it. Rewriting everything in Rust isn't realistic.

But C++ is clearly learning from Rust and closing the gap. Profiles, lifetime annotations, `std::expected` — these are all moving in the same direction.

The goal isn't to become Rust. It's to give C++ developers the tools to write safer code without abandoning the language entirely.

> For CP none of this matters much — your programs run once and memory safety bugs rarely show up in contest problems. But if you ever write production C++, this stuff will matter **a lot**.

---

## 💡 What Is This Repo?

This repo exists to collect interesting C++ developments for future blog posts. No code, just notes on things worth writing about.

---

## 📄 License

[![CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-nd/4.0/)

© 2026 [harsh-dev-ux](https://github.com/harsh-dev-ux). This work is licensed under a [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License](https://creativecommons.org/licenses/by-nc-nd/4.0/).

**In simple terms:** You can read and share this content with credit, but you cannot copy it, modify it, or repost it elsewhere without permission.

