### The Adapter Design Pattern

The Adapter design pattern in Rust is a structural pattern that allows two
incompatible interfaces to collaborate by wrapping an existing type inside a
new structure. Instead of relying on object inheritance - which Rust does not
natively support - this pattern uses struct composition and traits to translate
calls from a client-facing interface into a format that a legacy or third-party
type understands.

### Conceptual Diagram

```
                         [ CLIENT CODE ]
                                │
                                │ 1. Invokes expected interface
                                ▼
┌────────────────────────────────────────────────────────────────┐
│                         «Target Trait»                         │
├────────────────────────────────────────────────────────────────┤
│          the interface your application expects                │
├────────────────────────────────────────────────────────────────┤
│ + request()                                                    │
└───────────────────────────────┬────────────────────────────────┘
                                │
                                │ 2. Dispatches call to implementation
                                ▼
┌────────────────────────────────────────────────────────────────┐
│                        «Adapter Struct»                        │
├────────────────────────────────────────────────────────────────┤
│                 wraps the incompatible type                    │
├────────────────────────────────────────────────────────────────┤
│ - adaptee: Adaptee                                             │
├────────────────────────────────────────────────────────────────┤
│ + request() ──────────────────┼──────────────────────────────┐ │
└───────────────────────────────┼──────────────────────────────┼─┘
                                │                              │
                                │ 3. Translates API & forwards │
                                │    (inside request method)   │
                                ▼                              │
┌──────────────────────────────────────────────────────────────┼─┐
│                        «Adaptee Struct»                      │ │
├────────────────────────────────────────────────────────────────┤
│ e.g. 3rd party library code with an incompatible interface   │ │
├──────────────────────────────────────────────────────────────┼─┤
│ + specific_request() ◄───────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────┘
```

### Key Concepts in Rust

Unlike traditional object-oriented languages that use Class Adapters via
multiple inheritance, Rust exclusively relies on Object Adapters through
composition. The pattern involves three core components:

* **The Target Trait**: The standard interface your current client code expects.
* **The Adaptee**: The incompatible, legacy, or third-party struct containing the necessary functionality.
* **The Adapter**: A wrapper struct that holds an instance of the Adaptee and implements the Target Trait.

### Overcoming the Orphan Rule

In Rust, the adapter pattern often surfaces as the Newtype pattern. Rust's
Orphan Rule dictates that you cannot implement an external trait for an
external struct. Wrapping that third-party struct inside a locally defined
adapter tuple struct (struct MyAdapter(ExternalType);) allows you to legally
bypass this restriction and implement any local or remote traits you need.

### Idiomatic Ecosystem Variations

* **Iterator Adaptors**: The most famous use of this pattern in Rust is built straight into the standard library. Types like Map, Filter, and Zip take an existing iterator and adapt its output behavior into a new iterator type without allocating extra runtime overhead.
* **From and Into Traits**: For simple data-shape adaptations, implementing standard conversion traits like From<T> is considered the idiomatic way to safely change data from one interface into another.
* **Extension Traits**: Developers often combine adapters with blanket implementations to extend existing types uniformly across an application.

### Difference Facade Pattern and Adapter Pattern

The main difference is their intent and how many interfaces they change.

While both patterns act as wrappers to bridge code, they serve completely
different structural purposes:

| Feature | Adapter Pattern | Facade Pattern |
|---|---|---|
| Primary Intent | Converts an interface to fix incompatibility between two pieces of code. | Simplifies an interface to make a complex system easier to use. |
| Number of Targets | Usually wraps a single class, struct, or dependency. | Wraps a subsystem consisting of many classes or structs. |
| API Modification | Adapts code to fit an existing, strict interface (Target). | Creates a brand new, high-level interface from scratch. |
| Relationship | 1-to-1 conversion. | 1-to-Many unification. |

### Visual Comparison

#### The Adapter Pattern (1-to-1)

Your code expects Interface A, but you have Type B. The adapter sits between them so they can talk.

```
 ┌───────────┐      ┌─────────────┐      ┌───────────┐
 │ Client    ├─────►│   Adapter   ├─────►│  Adaptee  │
 └───────────┘      └─────────────┘      └───────────┘
```

#### The Facade Pattern (1-to-Many)

Your code wants to perform a complex task (like "Order Placement"). The Facade
hides the messy details of multiple sub-systems.

```
                                         ┌─────────────────┐
                                   ┌────►│ Billing System  │
                                   │     └─────────────────┘
 ┌───────────┐      ┌───────────┐  │     ┌─────────────────┐
 │ Client    ├─────►│  Facade   ├──┼────►│ Shipping System │
 └───────────┘      └───────────┘  │     └─────────────────┘
                                   │     ┌─────────────────┐
                                   └────►│ Inventory System│
                                         └─────────────────┘
```

### Direct Analogy

* **Adapter**: Think of a travel plug adapter. It doesn't change how the power grid works, and it doesn't change your laptop plug. It just bridges two incompatible physical shapes so power can flow.
* **Facade**: Think of a smart home remote button labeled "Movie Night". When you press it, the facade automatically dims the lights, rolls down the projector screen, and turns on the sound system. You don't have to talk to all three systems individually; the facade handles it via a single button.
