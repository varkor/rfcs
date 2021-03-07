- Feature Name: `undisambiguated_generics_in_patterns`
- Start Date: 2021-03-07
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary

Generic arguments in expressions must be disambiguated with `::` due to syntactic ambiguities.
Generic arguments in patterns currently also require the same disambiguator, but since there are no
syntactic ambiguities (since the comparison operators `<` and `>` are not valid in patterns), this
restriction may be dropped, making patterns more consistent with types.

# Motivation
[motivation]: #motivation

The necessity of `::` to disambiguate generic arguments in expressions is unfortunate, stemming from
ambiguities involving the comparison operators. The syntax for generic expressions in general does
not involve the disambiguator where it is not necessary, for example in types, traits, impls, and so
on. It turns out that patterns do not suffer from the same syntactic ambiguities as expressions, and
so we may allow users to write generic expressions without the disambiguator. This makes patterns
consistent with generic arguments in all other positions and leads to more concise and readable
code.

`::` will continue to be allowed in patterns.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Generic arguments in expressions must be disambiguated with `::`. For example, in the following
code

```rust
let x = std::convert::identity::<u32>(5);
// ...
```

it is necessary to write `::<u32>` to introduce the generic argument, rather than simply `<u32>` as
would be necessary in a type, to disambiguate the generic argument syntax from a comparison. This
is not necessary in patterns, in which it is not possible to express comparisons (meaning there is
no ambiguity).

```rust
struct Wrap<T>(T);

fn match_wrap<T>(w: Wrap<T>) {
    match w {
        Wrap<T>(_) => println!("That's a wrap!"),
    }
}
```

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

A working implementation exists here: https://github.com/rust-lang/rust/pull/80587. There is
expected to be no additional implementational complexity over adding a feature gate.

# Drawbacks
[drawbacks]: #drawbacks

As with any modification to syntax, this change means that, at least to begin, there will be two
valid syntaxes for generic arguments in patterns. However, since both syntaxes are familiar to users
(from generic arguments in types, and from generic arguments in expressions), this is unlikely to
cause confusion.

It has the advantage of allowing users to use a consistent syntax for generic arguments *except* in
the one case in which ambiguity is present.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

The alternative is to continue to disallow generic arguments to be specified without `::`. Generic
arguments are used infrequently in patterns, and so this is likely syntax that users do not often
encounter. This relaxation of the syntax is motivated by a desire to make the syntax for generic
arguments more consistent, making the disambiguator optional wherever it is not necessary.

# Prior art
[prior-art]: #prior-art

Not applicable.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

None.

# Future possibilities
[future-possibilities]: #future-possibilities

It would be possible to introduce a lint suggesting the undisambiguated variant is used in patterns,
though we would likely prefer the lint to be allow-by-default.
