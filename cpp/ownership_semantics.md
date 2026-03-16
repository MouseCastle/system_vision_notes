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

## 5. Examples

## 6. Common Misunderstandings

## 7. In Practice

## 8. Pitfalls / Anti-patterns

## 9. Related Concepts

## 10. Explain In My Own Words

## 11. Check Questions

## 12. One-line Summary