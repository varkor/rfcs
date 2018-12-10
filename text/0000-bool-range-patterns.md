- Feature Name: `bool_range_patterns`
- Start Date: 2018-12-10
- RFC PR:
- Rust Issue:

# Summary
[summary]: #summary

Add `bool` range patterns of the form `false..true` and `false..=true`. This fills a gap in the
existing range patterns and adds the dual of `bool` range constructors, which already exist as
`bool` implements the `Range*` traits.

# Motivation
[motivation]: #motivation

This removes an arbitrary limitation with range patterns, which are currently limited to numeric and
`char` types. The error `E0029` currently states:

> In a match expression, only numbers and characters can be matched against a range.
This is because the compiler checks that the range is non-empty at compile-time, and is unable to
evaluate arbitrary comparison functions.

It is clear that this limitation does not apply to values of type `bool` and following the same
reasoning it would be consistent and reasonable to permit `bool` pattern ranges.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Range patterns of `bool`s are permitted.

```rust
fn match_maker(b: bool) {
    match b {
        false..=true => { /* ... */ }
    }
}
```

Such expressions can always be replaced with analogous `if` expressions, but the ability to specify
`bool` range patterns makes the language more self-consistent.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

This is a trivial change in the compiler. Error `E0029` will be modified to permit `bool` ranges and
the pattern handling (including exhaustive integer pattern-matching) will be updated to handle such
ranges.

# Drawbacks
[drawbacks]: #drawbacks

It's unlikely users will use `bool` ranges in practice as such ranges can always be rewritten as
`if` expressions or similar. However, there is little disadvantage to adding `bool` ranges and makes
range patterns more consistent with range constructors.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

This is the only possible design. Alternatively, we continue to forbid `bool` range patterns, but
there is little motivation not to permit them. Overall, it seems that adding `bool` range patterns
has a slight advantage over continuing to reject `bool` range patterns.

# Prior art
[prior-art]: #prior-art

The existing range patterns in Rust are effectively prior art, as precisely the finitely enumerable
types implementing `Eq` (integers and `char`s) are permitted, *except* for `bool`.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

None.

# Future possibilities
[future-possibilities]: #future-possibilities

None. This fills in the last gap in the range pattern types in terms of existing built-in types.
