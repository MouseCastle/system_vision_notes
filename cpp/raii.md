# Topic

## 1. One-line Definition

> `RAII` (Resource Acquisition Is Initialization) is a C++ idiom where **resource lifetime is tied to object lifetime**, so acquisition happens in construction and release happens in destruction.

## 2. Why It Matters

- **Who releases resources when control flow changes unexpectedly?** In real systems code, early returns, exceptions, and multiple exit paths make manual cleanup fragile. This is why **RAII matters**. RAII ensures that cleanup is **deterministic** and **automatic** because the destructor runs when the owning object leaves scope.

> If code ignores **RAII**, several problems can arise.
> - If cleanup is handled manually across many branches, **resource leaks** can occur.
> - If cleanup is duplicated in multiple places, **double release** bugs can occur.
> - If exceptions bypass manual cleanup paths, program state can become **inconsistent**.
> - If lifetime rules are implicit instead of encoded in types, APIs become harder to reason about.

## 3. Core Intuition

- RAII is not only a convenience pattern for memory. It is a **lifetime discipline**: a resource should have a concrete owner object, and that owner should acquire the resource during construction and release it during destruction. The key idea is that **scope controls cleanup**. If an object is alive, its resource is alive. When the object dies, the resource is released exactly once.

## 4. Key Rules

1. **Resources with acquisition/release responsibility must be bound to a clearly defined owner object**.
2. **An owner's lifetime must begin only after resource acquisition succeeds**.
3. **Resource release responsibility must be executed by the owner's destructor**.
4. **Destructor cleanup for RAII owners must not throw**.
5. **Transfer of RAII ownership must be explicit and observable in code**.
6. **Standard RAII owner types should be preferred before custom wrappers**.

## 5. Examples

1. `Resources with acquisition/release responsibility must be bound to a clearly defined owner object`

```cpp
// Non-RAII form: cleanup must be handled manually on each control path
std::FILE* f = std::fopen("data.bin", "rb");
if (!f) return;

if (!validate_header(f)) {
  std::fclose(f);
  return;
}

if (!load_payload(f)) {
  std::fclose(f);
  return;
}

if (!commit_result(f)) {
  // bug-prone: easy to forget fclose on one exit path
  return;
}

std::fclose(f);
```

```cpp
// Valid RAII form: resource responsibility is bound to an owner object
using FileOwner = std::unique_ptr<std::FILE, int(*)(std::FILE*)>;
FileOwner owner(std::fopen("data.bin", "rb"), &std::fclose);
if (!owner) return;

std::FILE* view = owner.get(); // non-owning access
process(view);
// fclose is executed by owner's destructor
```

- key point: manual management forces `std::fclose` handling across many branches, while the owner-object form centralizes release responsibility to one lifetime authority.

2. `An owner's lifetime must begin only after resource acquisition succeeds`

```cpp
class SocketHandle {
public:
  SocketHandle() : fd_(::socket(AF_INET, SOCK_STREAM, 0)) {
    if (fd_ < 0) throw std::runtime_error("socket failed");
  }

  ~SocketHandle() noexcept {
    if (fd_ >= 0) ::close(fd_);
  }

private:
  int fd_ = -1;
};
```

- key point: if acquisition fails, construction throws and no valid owner object exists.

3. `Resource release responsibility must be executed by the owner's destructor`

```cpp
class FileHandle {
public:
  explicit FileHandle(const char* path) : f_(std::fopen(path, "rb")) {
    if (!f_) throw std::runtime_error("fopen failed");
  }

  ~FileHandle() noexcept { release(); }

  FileHandle(const FileHandle&) = delete;
  FileHandle& operator=(const FileHandle&) = delete;

  std::FILE* get() const { return f_; }

private:
  void release() noexcept {
    if (f_) {
      std::fclose(f_);
      f_ = nullptr;
    }
  }

  std::FILE* f_ = nullptr;
};
```

- key point: `FileHandle` owns `f_`, and destructor cleanup is executed through one internal release path.

4. `Destructor cleanup for RAII owners must not throw`

```cpp
class Guard {
public:
  ~Guard() noexcept {
    // cleanup only; never throw here
  }
};
```

- key point: non-throwing cleanup protects stack unwinding and preserves deterministic teardown.

5. `Transfer of RAII ownership must be explicit and observable in code`

```cpp
std::unique_ptr<Foo> a = std::make_unique<Foo>();
std::unique_ptr<Foo> b = std::move(a); // explicit ownership transfer
```

- key point: `std::move(a)` makes ownership handoff explicit at the call site.

6. `Standard RAII owner types should be preferred before custom wrappers`

```cpp
void run() {
  std::lock_guard<std::mutex> lock(mtx);
  auto p = std::make_unique<Foo>();
  std::vector<int> data;
}
```

- key point: standard owner types encode proven lifetime behavior and reduce custom cleanup errors.

## 6. Related Concepts

- `Ownership semantics`: RAII is a concrete mechanism for enforcing ownership rules in code.
- `Object lifetime`: RAII directly maps resource lifetime to object lifetime.
- `Rule of 3 / 5 / 0`: custom RAII types often require explicit special member rules, while standard wrappers help you follow Rule of 0.
- `Smart pointers`: `std::unique_ptr` and `std::shared_ptr` are core RAII tools for memory ownership models.
- `Exception safety`: RAII is the default foundation for basic and strong exception safety in C++.
- `Move semantics`: move operations are the standard way to transfer RAII ownership.
- `Scope`: lexical scope provides deterministic release timing for RAII-managed resources.
- `Non-owning references`: raw pointers/references can observe resources, but RAII owners control lifetime.
