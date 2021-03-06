# liblittle
**Header-only containers and algorithms for small data structures.**

## Motivation

STL semantics are convenient, but the STL containers, while having good asymptopic
behaviour, can exhibit considerable overhead for small sizes when compared with
alternative naïve implementations.

Other times, one might want to minimise heap access, and instead use a container
of compile-time size or capacity.

The containers here are designed to have low overhead when the number of elements are
'small'; if they are more efficient or not in any given application though will
depend very much on how they are used and on the nature of the target platform.

## Contents

So far, just two container types and one algorithm:

### `small::multiset` and `tiny::multiset`

`small_multiset` implements the C++ UnorderedAssociativeContainer concept with
the following omissions:

1.  No `equal_range()` method,
2.  No `hash_function()` method,
3.  No `emplace_hint()` method.

It uses a `std::vector` as a backing store, and offers fast O(1) inserts and
slower O(*N*) find and count operations. For sufficiently small *N*, this may
still offer time or space advantages over a tree, hash or sorted-array
implementation.

`tiny::multiset` is a multiset with capacity fixed at compile-time, backed by
an array of uninitialised storage. It does not perform heap allocations, and
correspondingly does not have an allocator nor a `get_allocator()` method.

### `small::map` and `tiny::map`

These are the `map` analogues of `small::multiset` and `tiny::multiset`
above, with the corresponding semantics.

### `tiny::sort`

The templated function `tiny::sort` uses sorting networks for sorting random-access
collections (really, anything supporting [] indexing) of fixed size.

Optimal sorting networks are used up to size 6; larger networks are constructed
recursively. These sorts will likely be much more efficient than a generic
sorting routine for arithmetic types when the compiler can produce branchless
min and max operations.

Branchless comparator kernels are provided for x86_64 architecures, which are
compiled in if the `HF_USE_ASM_KERNELS` preprocessor symbol is defined.

Produced assembly can be examined on the fly with the `tinysort-asm-%`
targets, where `%` is a label of the form _&lt;type&gt;&lt;size&gt;_, e.g. `int4`.
Spaces in the specification can and should be substituted with dashes `-`, e.g.
`unsigned-int*-3`.
