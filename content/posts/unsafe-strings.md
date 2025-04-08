---
date: '2025-04-06T16:39:58-05:00'
draft: false
title: 'Unsafe Strings'
tags: ["c++", "programming"]
---
What does the below C++ program do?

```cpp
#include <iostream>
#include <string>

void someFunction(const std::string& name) {
  const_cast<std::string&>(name) = "test";
}

int main() {
  const std::string name = "immutable";

  std::cout << "before someFunction name = '" << name << "'" << std::endl;

  someFunction(name);

  std::cout << "after someFunction name = '" << name << "'" << std::endl;

  return 0;
}
```

`name` is declared in `main` as a const variable.  We pass `name` as a constant reference to `someFunction` assuming it cannot be mutated.

But in `someFunction` we can cast to a mutable reference and write to it.

At best C++ can only give opt-in immutability, and we can easily defeat this with a cast.

We need a "safer" programming language.  More on this later.

Output from above is:

```
before someFunction name = 'immutable'
after someFunction name = 'test'
```