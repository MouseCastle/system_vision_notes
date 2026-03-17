# Topic

## 1. One-line Definition

> `Ownership semantics` defines **who owns a resource** (`ownership`), who is responsible for its lifetime, and the rules that govern **how that responsibility is expressed** (`semantics`) in code.

## 2. Why It Matters

- **Who should own a resource?** If a resource has no owner, **where does its lifetime begin, and who is responsible for its end**? This is why **ownership semantics matters**. In programming, ownership does not simply mean that someone "has" a resource. It means that **someone is responsible for that resource's lifetime**. Therefore, **`ownership semantics` makes it clear who creates a resource, who keeps it alive, who may transfer it, and who is ultimately responsible for destroying it**.

> If resources are managed **without clear ownership semantics**, several problems can arise.
> - If it is **unclear who should destroy a resource**, a **`memory leak`** may occur.
> - If **multiple parties assume they are the owner**, a **`double free`** may occur.
> - If a resource is **referenced after it has already been destroyed**, a **`dangling reference`** or **`use-after-free`** may occur.
> - If responsibility is **unclear across API boundaries** between functions or modules, the **stability and predictability** of the code can **degrade significantly**.

## 3. Core Intuition

- **Ownership semantics** may be easily **misunderstood as a matter of how a resource is accessed**, but this is not accurate. Access to a resource does not imply ownership, nor is ownership determined solely by the way the resource is used. **Access** is merely about **how a resource is utilized within its lifetime**. **The core of ownership semantics is** not access itself, but **the responsibility for managing the existence of that resource**. In this sense, **the owner is the entity that has the authority and obligation to create, maintain, and eventually destroy the resource**.

## 4. Key Rules

1. **Access does not imply ownership**.
2. **Every resource must have a clearly defined lifetime owner**.
3. **Destruction** responsibility **follows ownership**.
4. **Ownership transfer** must be **explicit**.
5. **Shared ownership** must be **intentional**.
6. **Non-owning references** are valid only while **the owner keeps the resource alive**.

## 5. Examples

1. `Access does not imply ownership`

```cpp
auto p = std::make_unique<Foo>();
Foo* raw = p.get();
```

- key point: `raw` can access the object, but `p` owns it.

2. `Every resource must have a clearly defined lifetime owner`

```cpp
auto p = std::make_unique<Foo>();
```

- key point: `p` is the clearly defined owner of the resource.

3. `Destruction responsibility follows ownership`

```cpp
void use(Foo* foo); // non-owning
```

- key point: `use` may access `foo`, but it must not destroy it.

4. `Ownership transfer must be explicit`

```cpp
void take(std::unique_ptr<Foo> foo);
take(std::move(p));
```

- key point: Ownership moves explicitly through `std::move`.

5. `Shared ownership must be intentional`

```cpp
auto a = std::make_shared<Foo>();
auto b = a;
```

- key point: Copying `shared_ptr` creates another owner, not just another observer.

6. `Non-owning references are valid only while the owner keeps the resource alive`

```cpp
Foo* raw;
{
  auto p = std::make_unique<Foo>();
  raw = p.get();
}
// raw is now dangling
```

- key point: A borrowed pointer is valid only while the owner is alive.


## 6. Related Concepts

- `RAII`: ownership is often implemented through **object lifetime**, so **resource release happens automatically** when the **owner goes out of scope**.
- `Object lifetime`: ownership semantics exists to make a resource's **lifetime explicit and manageable**.
- `Smart pointers`: `std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr` are **standard C++ tools** for expressing **different ownership models**.
- `Move semantics`: **unique ownership** is commonly **transferred through move operations** rather than copy operations.
- `Borrowing / non-owning references`: raw pointers, references, and views often represent **access without lifetime responsibility**.
- `Resource management`: ownership semantics is part of the broader problem of managing **memory, files, locks, sockets, and other resources safely**.
- `API design`: **function parameters and return types** often communicate whether a resource is **borrowed, transferred, or shared**.
- `Exception safety`: **clear ownership rules** make **cleanup and failure handling** much more predictable.
