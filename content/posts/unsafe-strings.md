---
date: '2025-05-16T06:13:00-05:00'
draft: false
title: 'Unsafe Strings'
tags: ["c++", "programming", "memory safety"]
---
What does the below C++ program do?

```cpp
#include <iostream>
#include <memory>
#include <string>

void someFunction(
  const std::string& name
) {
  static const std::string* pCachedName = nullptr;

  if (pCachedName == nullptr) {
    pCachedName = &name;
    std::cout << "in someFunction set pCachedName to value: " << *pCachedName << std::endl;
  } else {
    std::cout << "in someFunction pCachedName has value: " << *pCachedName << std::endl;
  }

}

int main() {
  std::shared_ptr<std::string> name;
  name.reset(new std::string("alice"));

  std::cout << "before someFunction name = '" << *name << "'" << std::endl;

  someFunction(*name);

  name.reset(new std::string("bob"));

  someFunction(*name);

  std::cout << "after someFunction name = '" << *name << "'" << std::endl;

  return 0;
}
```

`name` is declared in `main` as a `std::shared_ptr` to a string.  We pass `name` as a constant reference to `someFunction` assuming we do not need to worry about it being mutated or stored.

In `someFunction` (for crazy-fast performance?) we have a `static` pointer `pCachedString`.  On the first call to `someFunction` we store the pointer to `name`.

After this main calls `name.reset()` which deletes the previous name pointer.  At this point the `pCachedString` is invalid and points to a deleted object.

The second call to `someFunction` tries to use the invalid pointer.  This is undefined behavior, all bets are off as to what will happen.  

A modern c++ compiler is happy to compile the above code without warnings, I used g++ 13.3.0.  Even with `-Wall -Wextra` options no warnings are produced:

```sh
$ g++ -Wall -Wextra test.cpp

$ ./a.out
before someFunction name = 'alice'
in someFunction set pCachedName to value: alice

Segmentation fault (core dumped)
```

When we run the code we get a core dump due to use of deleted value in second call to `someFunction`.

This is a major weakness of C++, we can store references or pointers anywhere.  Hope springs eternal that these objects are not deleted and then used again.

Scenarios like above are a major reason garbage collection exists.  Languages having a GC will automatically keep reference counts for all objects and only delete unreachable objects.  GC has it's own costs of performance and complexity, but it is unlikely our programs will core dump.

We need a [memory safe language](https://en.wikipedia.org/wiki/Memory_safety).