# Topic

## 1. One-line Definition

> `Object lifetime` defines **when an object begins to exist**, **remains valid**, and **ceases to exist** in a program.

## 2. Why It Matters

- **When does an object actually exist?** A program may have memory, a name, or a pointer to something, but that does not always mean that a valid object is currently alive there. This is why **object lifetime matters**. In C++, correctness depends not only on **what data is stored**, but also on **whether the object being accessed is still alive**.

> If code ignores **object lifetime**, several problems can arise.
> - If code **uses an object before its lifetime begins**, the program may access an invalid or uninitialized state.
> - If code **continues to use an object after its lifetime ends**, a **dangling reference** or **use-after-free** may occur.
> - If code **confuses allocated memory with a live object**, construction and destruction rules may be violated.
> - If object lifetime is **unclear across scopes or API boundaries**, the program becomes harder to reason about and easier to break.

## 3. Core Intuition

- **Object lifetime** is not the same thing as **having memory** or **having access**. An object becomes meaningful only when its **lifetime has begun**, and it remains safe to use only while that lifetime is still active. The core idea is that **valid access depends on the object being alive**, not merely on the existence of a pointer, reference, or memory region. In this sense, object lifetime is about **the temporal validity of an object in a program**.

## 4. Key Rules

1. **Allocated memory does not automatically mean a live object exists there**.
2. **An object may be used only after its lifetime begins**.
3. **An object may not be used after its lifetime ends**.
4. **References and pointers do not extend an object's lifetime by themselves**.
5. **Object lifetime is often tied to scope, ownership, and destruction rules**.
6. **Correct C++ code must reason about both memory and lifetime**.

## 5. Examples

1. `Allocated memory does not automatically mean a live object exists there`

```cpp
void* raw = operator new(sizeof(Foo)); // memory only
Foo* foo = static_cast<Foo*>(raw);
```

- key point: **memory has been allocated**, but a valid `Foo` object **has not been constructed yet**.

2. `An object may be used only after its lifetime begins`

```cpp
Foo foo; // constructor runs here
foo.work();
```

- key point: `foo` becomes valid to use **after construction** begins its lifetime.

3. `An object may not be used after its lifetime ends`

```cpp
Foo* ptr;
{
  Foo foo;
  ptr = &foo;
}
// ptr now points to a dead object
```

- key point: the pointer still exists, but the object it referred to **no longer does**.

4. `References and pointers do not extend an object's lifetime by themselves`

```cpp
const Foo* ptr = nullptr;
{
  Foo foo;
  ptr = &foo;
}
// ptr now points to a dead object

const Foo& ref = Foo{};
// this temporary lives as long as ref, due to a special language rule
```

- key point: a pointer or reference does **not automatically keep an object alive**. The first case shows that simply storing an address does **not extend lifetime**. The second case is a **special lifetime-extension rule for temporaries bound directly to const references**, which means lifetime extension must be **explicitly defined by the language**, not assumed in general.

5. `Object lifetime is often tied to scope, ownership, and destruction rules`

```cpp
auto foo = std::make_unique<Foo>();
Foo* raw = foo.get();
```

- key point: `raw` can access the object, but the object's lifetime is still controlled by the **owner** `foo`.

6. `Correct C++ code must reason about both memory and lifetime`

```cpp
std::vector<Foo> v;
v.push_back(Foo{});
Foo* p = &v[0];
v.push_back(Foo{}); // reallocation may happen
```

- key point: if the vector grows without enough reserved capacity, **reallocation may move its elements to a new memory region**. In that case, `p` may no longer point to a **still-valid object**, even though the vector itself is still alive.

## 6. Related Concepts

- `Ownership semantics`: ownership determines **who is responsible for an object's lifetime**.
- `RAII`: C++ often manages lifetime by tying **resource cleanup to object destruction**.
- `Construction and destruction`: an object's lifetime is closely related to **when constructors and destructors run**.
- `Scope`: many local objects begin and end their lifetime according to **scope boundaries**.
- `Move semantics`: moving an object may change **which object remains valid** and what state the moved-from object is left in.
- `Dangling pointers and references`: these are direct consequences of **accessing an object after its lifetime has ended**.
- `Placement new`: this separates **raw memory allocation** from **object lifetime start**.
- `Smart pointers`: these help manage **ownership**, but they do not change the fact that code must still reason correctly about lifetime.
