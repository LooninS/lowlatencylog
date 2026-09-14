+++
title = "sh++ devlog #1: Why I'm writing a shell, and my first REPL"
date = 2025-08-30
draft = true
tags = ["cpp", "linux", "shell", "devlog", "systems"]
+++

I've been diving into how software works at a low level, so I started building a simple shell in C++ (called **sh++**) to learn by doing. This devlog will track my progress and what I understand while implementing different parts of a shell.

## What's a shell?

A shell is an interface between the OS and the user, allowing users to execute commands and manage system resources.

## How does a shell work?

Modern shells like `bash` and `zsh` are packed with features, which can be overwhelming. I'll start by implementing the most essential features one at a time.

Before writing code, I stripped the shell down to its basics:

- Printing a prompt
- Handling a few builtin commands (like `exit`)
- Running external commands from `$PATH`
- (Later) redirection, pipes, job control, etc.

This list will grow as I remember more things to implement.

## Making sh++

### REPL

The first step is to make a loop that reads input, evaluates it, and prints output. This is known as a **REPL** (Read–Eval–Print Loop).

```cpp
int main() {
    while (true) {
        std::cout << "> " << std::flush;
        std::string line;
        if (!std::getline(std::cin, line))
            break;

        if (line == "exit")
            break;

        // TODO: evaluate 'line'
    }

    return 0;
}
```

This keeps printing the prompt until we explicitly break out of the loop. The simplest way to exit is with the `exit` command.

The current check isn't perfect: I'm not handling arguments like `exit 1` or printing errors, but it's a good start.

## Building

### Requirements

- C++ compiler with C++17 or later support (GCC/Clang)
- CMake 3.16+
- POSIX environment (Linux, macOS, WSL, etc.)

On Arch-based systems:

```bash
sudo pacman -S base-devel cmake
```

On Debian/Ubuntu:

```bash
sudo apt-get update
sudo apt-get install build-essential cmake
```

### Build commands

From the project root:

```bash
cmake -B build -S .
cmake --build ./build
```

This produces the `shell` binary in `build/`.

### Helper script

```bash
#!/bin/sh

set -e

cd "$(dirname "$0")"
cmake -B build -S .
cmake --build ./build
exec ./build/shell "$@"
```

This simple script wraps the CMake build so you don't have to type the commands every time.

### Project structure

```text
.
├── CMakeLists.txt
├── sh++.sh
└── src
    └── main.cpp
```

This might feel like extra work now, but as the project grows, manually compiling each time will become painful.

Here's `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 3.13)

project(sh++)

file(GLOB_RECURSE SOURCE_FILES src/*.cpp src/*.hpp)

set(CMAKE_CXX_STANDARD 23) # Enable the C++23 standard

add_executable(shell ${SOURCE_FILES})
```

Now, if we run the script, we should see our prompt.

In the next post, I'll implement a few more builtin commands and show how to run external commands from `$PATH`.

---

