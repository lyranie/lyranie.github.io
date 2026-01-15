---
layout: doc
---

#### [View project on GitHub](https://github.com/lyranie/c3-build-file)

## Usage example

### minimal setup

```c3build
compiler(VERSION MIN 0.7.9)
project(example VERSION 0.1.0)

add("src/main.c3") -- assuming that this file exists

program(example)
```

### this projects setup

```c3build
compiler(VERSION MIN 0.7.1 MAX 0.7.9)
project(c3build VERSION 0.1.0)

task("install")
    print("Installing...")
    remove(DIR "$(HOME)/$(PROJECT)")
    create(DIR "$(HOME)/$(PROJECT)")
    copy(FILE "build/$(PROJECT)" "$(HOME)/$(PROJECT)/$(PROJECT)")
    ifdef(OS_LINUX)
        cmd("chmod +x $(HOME)/$(PROJECT)/$(PROJECT)")
    endif()
    ifdef(OS_DARWIN)
        cmd("chmod +x $(HOME)/$(PROJECT)/$(PROJECT)")
    endif()
endtask()

add("src/")

program(c3build)
```

## Functions

### `compiler()`

`compiler()` defines the compiler version

```c3build
compiler(VERSION 0.7.5)
compiler(VERSION MIN 0.7.5)
compiler(VERSION MAX 0.7.9)
compiler(VERSION MIN 0.7.5 MAX 0.7.9)
```

### `project()`

`project()` defines the basic project info and is required for the script to function.

```c3build
project(example VERSION 0.1.0)
```

### `require()`

`require()` exits the build file if the requested file could not be found.

```c3build
require(LIB "LLVM") -- creates a variable `lib_llvm`
require(EXE "git")  -- creates a variable `exe_git`
```

### `find()`

unlike `require()`, `find()` does not exit the script if the file cannot be found.

```c3build
find(LIB "LLVM") -- creates a variable `lib_llvm`
find(EXE "git")  -- creates a variable `exe_git`
```

### `ifdef()`

`ifdef()` provides a body whose statements will be run if the variable is defined.

```c3build
find(EXE "git")

ifdef(exe_git)
    ...
endif()
```

### `option()`

`option()` lets you add compile options to the command.

```c3build
option("--trust=full")
option("-D EXAMPLE")
```

### `task()`

`task()` lets you define a task that can be run by the script.

```c3build
task("example")
    ...
endtask()
```

It can then be run like this

```shell
c3build example
```

You can also chain multiple tasks

```shell
c3build clean install example
```

### `cmd()`

`cmd()` lets you execute terminal commands.

```c3build
cmd("chmod +x ./program")
```

### `program()`

`program()` is what executes the compile command so it should always be at the end of the file.

```c3build
project(example VERSION 0.1.0)
program(example) -- name must match with project name
```
you can also tell the compiler what to link with and it should be done this way
```c3build
...
require(LIB "LLVM")

program(example LINK lib_llvm) -- name must match with project name
```

### `create()`

`create()` allows you to create files and folders.

```c3build
create(DIR "$(HOME)/example")
create(FILE "$(HOME)/example/test.txt")
```

### `copy()`

`copy()` allows you to copy files and folders.

```c3build
copy(DIR "./example" "$(HOME)/example")
copy(FILE "./test.txt" "$(HOME)/example/test.txt")
```

### `print()`

`print()` allows you to print to the console.

```c3build
print("example message")
```

### `remove()`

`remove()` allows you to delete files and folders.

```c3build
remove(DIR "$(HOME)/example")
remove(FILE "$(HOME)/example/test.txt")
```

### `add()`

`add()` lets you add files to the compile command.

```c3build
add("src/main.c3"
    "src/other.c3")
add("lib/") -- you can also add directories
```

## Variables

when using `require()` or `find()` a variable gets created. The variable can be used to check if the executable or
library was found and if so, it returns the path to the file. A variable can either be prefixed by `lib_` or `exe_`
depending on the first parameter (`LIB` or `EXE`). Variables can be used in strings for the functions `option`, `cmd`,
`create`, `copy`, `print` and `remove` with the following syntax: `$(VAR_NAME)`

## Builtins
`HOME` - points to the home directory<br>
`PROJECT` - points to the project name<br>
`VERSION` - points to the project version<br>
`OS_WINDOWS` - can only be used for `ifdef` and will result in empty string if used otherwise<br>
`OS_DARWIN` - can only be used for `ifdef` and will result in empty string if used otherwise<br>
`OS_LINUX` - can only be used for `ifdef` and will result in empty string if used otherwise<br>

## Installing
As of now c3build does not offer any prebuilt binaries and must be built from source.
```shell
git clone https://github.com/lyranie/c3-build-file.git
cd c3-build-file
c3c build
```
and that's it!
