# Topic

## 1. One-line Definition

> `Smart pointers` are C++ owner/observer types that make **ownership model**, **lifetime responsibility**, and **release behavior** explicit in code.

## 2. Why It Matters

- **Who owns this object, and when is it destroyed?** Raw pointers can represent access, but they do not encode ownership responsibility by themselves. This is why **smart pointers matter**. They express ownership semantics through types, so creation, transfer, sharing, and observation of lifetime are easier to reason about at API and call-site level.

> If smart pointer models are used incorrectly, several problems can arise.
> - If ownership is copied where transfer was intended, lifetime rules become ambiguous.
> - If shared ownership is used by default, object lifetime can become unintentionally long.
> - If `weak_ptr` checks are skipped, code may access already-destroyed objects.
> - If ownership model is inconsistent across module boundaries, cleanup behavior becomes unpredictable.

## 3. Core Intuition

- Smart pointers are not "automatic memory magic." They are **ownership contracts encoded in types**. `unique_ptr` means one owner, `shared_ptr` means reference-counted shared owners, and `weak_ptr` means non-owning observation of shared lifetime. The core idea is that **lifetime responsibility should be visible in type signatures, not hidden in comments or conventions**.

## 4. Key Rules

1. **Default heap ownership should be expressed with std::unique_ptr**.
2. **Ownership transfer must be explicit when moving std::unique_ptr**.
3. **std::shared_ptr should be used only when shared lifetime is actually required**.
4. **Observation of a std::shared_ptr-managed object without ownership should use std::weak_ptr**.
5. **std::weak_ptr must be validated through lock() before dereference**.
6. **Public APIs should expose ownership intent through smart pointer types**.

## 5. Examples

1. `Default heap ownership should be expressed with std::unique_ptr`

```cpp
auto job = std::make_unique<Job>();
run(*job);
```

- key point: `job` has one clear owner, and destruction is tied to that owner's lifetime.

2. `Ownership transfer must be explicit when moving std::unique_ptr`

```cpp
void enqueue(std::unique_ptr<Task> task);

auto task = std::make_unique<Task>();
enqueue(std::move(task));
```

- key point: `std::move` makes the ownership handoff explicit at the call site.

3. `std::shared_ptr should be used only when shared lifetime is actually required`

```cpp
// Fragile form: shared ownership with no real sharing requirement
std::shared_ptr<Buffer> a = std::make_shared<Buffer>();

// Better form: single-owner lifetime
std::unique_ptr<Buffer> b = std::make_unique<Buffer>();

// Valid shared-lifetime form: multiple owners must keep the object alive
auto model = std::make_shared<Model>();
renderer.set_model(model);
tracker.set_model(model);
```

- key point: shared ownership adds reference-counted lifetime coupling, so use it only when multiple owners are truly needed.

4. `Observation of a std::shared_ptr-managed object without ownership should use std::weak_ptr`

```cpp
struct SessionRegistry {
  std::shared_ptr<Session> owner;
  std::weak_ptr<Session> observer;
};
```

- key point: `observer` can reference the same object without extending its lifetime.

5. `std::weak_ptr must be validated through lock() before dereference`

```cpp
std::weak_ptr<Node> weak = get_node_observer();

if (auto strong = weak.lock()) {
  strong->tick();
}
```

- key point: `lock()` is the validity gate that prevents access to an already-destroyed object.

6. `Public APIs should expose ownership intent through smart pointer types`

```cpp
class Engine {
public:
  void set_source(std::shared_ptr<Source> source); // shared ownership
  void set_cache(std::unique_ptr<Cache> cache);    // transfer ownership
  void render(const Frame& frame);                 // non-owning use
};
```

- key point: API signatures make ownership model explicit, reducing hidden lifetime assumptions.

## 6. Failure Modes Without This Concept

1. `Ambiguous ownership with raw pointer handoff can cause leak or double delete`

```cpp
Widget* make_widget();
void set_widget(Widget* w);

Widget* w = make_widget();
set_widget(w); // ownership contract unclear
```

```cpp
std::unique_ptr<Widget> make_widget();
void set_widget(std::unique_ptr<Widget> w);

auto w = make_widget();
set_widget(std::move(w)); // ownership transfer is explicit
```

- key point: missing ownership type causes cleanup ambiguity; explicit owner transfer removes it.

2. `Default shared ownership can keep objects alive longer than intended`

```cpp
auto cache = std::make_shared<Cache>();
worker.attach(cache);
monitor.attach(cache);
// lifetime extends until every shared owner releases
```

```cpp
auto cache = std::make_unique<Cache>();
worker.attach(*cache); // borrow during controlled lifetime
```

- key point: unnecessary shared ownership delays release; single-owner lifetime keeps destruction predictable.

3. `Asynchronous observer with raw pointer can access destroyed object`

```cpp
Session* s = get_session_raw();
post_task([s] { s->flush(); }); // unsafe if session dies first
```

```cpp
std::weak_ptr<Session> s = get_session_observer();
post_task([s] {
  if (auto strong = s.lock()) strong->flush();
});
```

- key point: weak observation plus lock-based validation prevents stale-pointer access.

4. `Manual cleanup across multiple branches can miss release on one path`

```cpp
Packet* p = new Packet();
if (!decode(p)) { delete p; return; }
if (!validate(p)) { delete p; return; }
if (!commit(p)) return; // forgotten delete
delete p;
```

```cpp
auto p = std::make_unique<Packet>();
if (!decode(p.get())) return;
if (!validate(p.get())) return;
if (!commit(p.get())) return;
```

- key point: manual ownership duplicates cleanup logic; owner-object lifetime removes branch-dependent leaks.

5. `Shared ownership cycles can prevent destruction`

```cpp
struct Node {
  std::shared_ptr<Node> next;
  std::shared_ptr<Node> prev;
};
```

```cpp
struct Node {
  std::shared_ptr<Node> next;
  std::weak_ptr<Node> prev;
};
```

- key point: strong cycles keep reference count above zero; weak links break the cycle.

6. `Weak observer dereference without lock validation can fail under race`

```cpp
std::weak_ptr<Job> w = get_job();
auto* raw = w.lock().get(); // temporary shared_ptr destroyed immediately
raw->run();                 // unsafe
```

```cpp
std::weak_ptr<Job> w = get_job();
if (auto s = w.lock()) s->run();
```

- key point: validation and use must happen under the same strong-owner scope.

7. `Mixed ownership conventions across modules can break lifetime assumptions`

```cpp
// Module A assumes transfer
void set_buffer(Buffer* b);

// Module B assumes borrow
Buffer* b = new Buffer();
set_buffer(b);
delete b;
```

```cpp
void set_buffer(std::unique_ptr<Buffer> b); // transfer is explicit
```

- key point: ownership contracts in raw-pointer APIs are ambiguous; smart pointer types align cross-module behavior.

8. `Using shared ownership in high-frequency data paths can increase latency variance`

```cpp
for (;;) {
  std::shared_ptr<Frame> f = next_frame_shared();
  process(*f);
}
```

```cpp
for (;;) {
  std::unique_ptr<Frame> f = next_frame_unique();
  process(*f);
}
```

- key point: reference-count traffic in tight loops can add avoidable overhead when single ownership is enough.

## 7. Operational Guidelines

1. `Use unique ownership in hot paths unless multi-owner lifetime is mandatory`

```cpp
// Frame pipeline hot path
std::unique_ptr<Frame> frame = pool.acquire();
process(*frame);
pool.release(std::move(frame));
```

- key point: unique ownership minimizes reference-count overhead in high-frequency loops.

2. `Use weak observation at delayed execution boundaries`

```cpp
std::weak_ptr<Session> weak = session;
timer.after(1000, [weak] {
  if (auto s = weak.lock()) s->send_heartbeat();
});
```

- key point: delayed callbacks should validate liveness at execution time.

3. `Do not introduce shared ownership when shutdown order is already deterministic`

```cpp
class App {
  std::unique_ptr<Renderer> renderer;
  std::unique_ptr<Tracker> tracker;
};
```

- key point: when lifecycle is centrally controlled, shared ownership adds complexity without safety gain.

4. `Capture weak_ptr in asynchronous callbacks and promote at use time`

```cpp
std::weak_ptr<Client> weak = client;
executor.post([weak] {
  if (auto c = weak.lock()) c->on_ready();
});
```

- key point: this prevents callbacks from extending lifetime unnecessarily while keeping execution safe.

5. `Use shared ownership only for truly concurrent owners of the same lifetime`

```cpp
auto model = std::make_shared<Model>();
renderer.set_model(model);
tracker.set_model(model);
```

- key point: shared ownership is justified when multiple subsystems must co-own the same object.

6. `Represent borrow-only APIs with references or raw pointers, not shared ownership`

```cpp
void run_stage(Frame& frame);   // borrow
void inspect(const Frame* frame); // borrow
```

- key point: borrow signatures avoid accidental lifetime extension by callers.

7. `Break potential ownership cycles at design time`

```cpp
struct Parent { std::shared_ptr<class Child> child; };
struct Child  { std::weak_ptr<Parent> parent; };
```

- key point: cycle prevention should be structural, not a late debugging fix.

8. `Profile before replacing ownership models for performance reasons`

```cpp
// Measure lock()/refcount overhead before redesigning ownership graph
```

- key point: ownership changes affect safety and complexity, so performance-driven changes should be evidence-based.

## 8. Related Concepts

- `Ownership semantics`: smart pointers are concrete tools for expressing ownership models in code.
- `Object lifetime`: smart pointer choice directly affects when an object is kept alive and destroyed.
- `RAII`: smart pointers apply RAII to dynamic memory and related ownership boundaries.
- `Move semantics`: `std::unique_ptr` transfer is performed through move operations.
- `Exception safety`: smart pointers reduce leak risk on exceptional control paths.
- `API design`: parameter and return types communicate borrow/transfer/share intent.
- `Rule of 3 / 5 / 0`: smart pointers help many classes stay in Rule-of-0 territory.
- `Cycle management`: `weak_ptr` is used to break `shared_ptr` reference cycles.
