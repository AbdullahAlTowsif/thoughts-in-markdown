# Go OOP Concepts Summary

A concise overview of the Object-Oriented Programming (OOP) pillars in Go, based on our discussion about **receiver functions** and related concepts.

---

## 1. Which OOP Pillar Matches the Go Receiver Function?

**Answer: Encapsulation**

### Explanation
In classical OOP, a class groups **state** (fields) and **behavior** (methods) together.  
Go has no classes, but **receiver methods** (functions with a receiver) provide the same association: they attach behavior to a concrete type (usually a struct).

This is the primary way Go realizes the “data + behavior” part of **encapsulation**.  
Access control is handled at the package level via capitalization (exported vs unexported names).

### Example
```go
package main

import "fmt"

// Struct holds the data
type Rectangle struct {
    Width, Height float64
}

// Method with a value receiver – associated with Rectangle
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Method with a pointer receiver – can modify the original value
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

func main() {
    rect := Rectangle{Width: 10, Height: 5}
    fmt.Println(rect.Area())   // 50  – method call on the value

    rect.Scale(2)
    fmt.Println(rect.Area())   // 200 – state was modified through the method
}
```

Here the `Area` and `Scale` methods are tightly coupled to the `Rectangle` type via their receivers — exactly as methods belong to a class in traditional OOP.

---

## 2. Does Go Have Polymorphism, Abstraction, and Inheritance?

| OOP Pillar     | Supported in Go? | How it is achieved                                      |
|----------------|------------------|---------------------------------------------------------|
| Encapsulation  | Yes              | Structs + receiver methods + package-level visibility   |
| Polymorphism   | Yes              | Interfaces (implicit / duck typing)                     |
| Abstraction    | Yes              | Interfaces                                              |
| Inheritance    | No               | Composition / embedding instead                         |

### Polymorphism — Yes
Go supports polymorphism through **interfaces**.

- Any type that implements the methods declared by an interface automatically satisfies it (no `implements` keyword needed).
- You can write functions that accept the interface type and work with any concrete type that fulfills it.

```go
type Speaker interface {
    Speak() string
}

type Dog struct{}
func (d Dog) Speak() string { return "Woof" }

type Cat struct{}
func (c Cat) Speak() string { return "Meow" }

func MakeSound(s Speaker) {
    fmt.Println(s.Speak())
}

// Both work — polymorphic behavior
MakeSound(Dog{})
MakeSound(Cat{})
```

### Abstraction — Yes
Interfaces also provide **abstraction**.

- An interface defines *what* behavior is required without specifying *how* it is implemented.
- Callers depend only on the interface, not on concrete types → hides implementation details.

### Inheritance — No (classical inheritance)
Go deliberately does **not** have class-based inheritance or type hierarchies (`extends`, subclassing, etc.).

Instead it uses:
- **Composition** (struct embedding) — the preferred way to reuse code and “promote” methods.
- Embedding gives method promotion that *looks* a bit like inheritance, but it is still composition (no “is-a” relationship, no fragile base-class problems).

```go
type Animal struct {
    Name string
}

func (a Animal) Speak() string {
    return "..."
}

type Dog struct {
    Animal   // embedding (composition)
    Breed string
}

// Dog can call Speak() via promotion, but Dog is not a subclass of Animal
```

---

## Summary

Go is often described as a **“post-OOP”** or **“object-oriented but not class-oriented”** language.  
It keeps the useful parts of OOP:

- Encapsulation (structs + receivers)
- Polymorphism (interfaces)
- Abstraction (interfaces)

…while rejecting classical inheritance in favor of **composition**.

Receiver functions are the key mechanism that lets Go attach behavior to types, enabling the encapsulation pillar and forming the foundation for interfaces (polymorphism & abstraction).

---

*Generated from our conversation on Go OOP concepts.*
