# RFC: Trait-parametric Polymorphism

Planning, scheming and designing of `{-# LANGUAGE ConstraintKinds #-}` for Rust

## What is the goal of this repository?

We intend to write an RFC that lets you write things such as:

### A `Map` trait with associated traits

```rust
pub trait Map<K: Self::Key, V: Self::Value> {
    trait Key;
    trait Value;
    
    ...
}

impl<K: Hash, V> Map<K, V> for HashMap<K, V> {
    trait Key = Hash;
    trait Value = Any;
    
    ...
}
```

### A generic trait object type alias

```rust
type FooBox<trait Extra = ()> = Box<dyn Foo + Extra>;
```

### `ExactSizeIterator` passing

```rust
// the output type is Extra if the input type is Extra

fn foo<trait Extra = ()>(iter: impl Iterator + Extra)
    -> impl Iterator + Extra
{
    ...
}
```

## Schedule

This is a long term plan; the intent is to have a bullet proof RFC ready by the end of 2018
or at the beginning of 2019 for consideration.

Landing "ConstraintKinds" will largly require the integration of the `chalk_engine` into `rustc`
to be complete. Then we can have a more rigoruous understanding of what impact this new feature
will have and how we preserve soundness and how this interacts with type inference.

## Contributing

If you want to join in and collaborate, you can do any of the following:
+ file an issue with your thoughts
    + **in particular, it is very useful if you have any motivating examples and use cases**
+ give me (@Centril) a shoutout and ask for collaborator permissions
+ make a PR

## What are `ConstraintKinds`?

Here's a friendly explanation:
https://stackoverflow.com/questions/31317159/constraintkinds-explained-on-a-super-simple-example

### What is a constraint?

A constraint or a bound is a logical requirement on a type.
A type satisfies a certain constraint if it satisfies the requirement to for example,
have a certain set of methods.

In Rust, a constraint is of the form: `MyType: MyTrait` where `MyType` is of the kind `type`,
and `MyTrait` of the kind `type -> constraint`. If we apply a `type` to the kind level function
`type -> constraint`, we get `constraint` back.

### GHC and Haskell

`ConstraintKinds` are a feature implemented in GHC, the Glasgow **Haskell** Compiler.
Conceptually, they let you talk about bounds or *constraints* as kinds.
