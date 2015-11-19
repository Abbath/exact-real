exact-real
==========

Exact real arithmetic implemented by fast binary Cauchy sequences.


Motivating Example
-------------------

Compare evaluating Euler's identity with a `Float`:

``` haskell
λ> let i = 0 :+ 1
λ> exp (i * pi) + 1 :: Complex Float
0.0 :+ (-8.742278e-8)
```

... and with a `CReal`

``` haskell
λ> import Data.CReal
λ> let i = 0 :+ 1
λ> exp (i * pi) + 1 :: Complex (CReal 0)
0 :+ 0
```

Implementation
--------------

The basic operations have explanations and proofs of correctness
[here][correctness].

`CReal`'s phantom type parameter `n :: Nat` represents the precision at which
values should be evaluated at when converting to a less precise representation.
For instance the definition of `x == y` in the instance for `Eq` evaluates `x -
y` at precision `n` and compares the resulting `Integer` to zero. I think that
this is the most reasonable solution to the fact that lots of of operations
(such as equality) are not computable on the reals but we want to pretend that
they are for the sake of writing useful programs. Please see the
[Caveats](#caveats) section for more information.

The `CReal` type is an instance of `Num`, `Fractional`, `Floating`, `Real`,
`RealFrac`, `RealFloat`, `Eq`, `Ord`, `Show` and `Read`. The only functions not
implemented are a handful from `RealFloat` which assume the number is
implemented with a mantissa and exponent.

There is a comprehensive test suite to test the properties of these classes.

The performance isn't terrible on most operations but it's obviously not nearly
as speedy as performing the operations on `Float` or `Double`. The only two
super slow functions are `asinh` and `atanh` at the moment.


Caveats
-------

The implementation is not without its caveats however. The big gotcha is that
although internally the `CReal n`s are represented exactly, whenever a value is
extracted to another type such as a `Rational` or `Float` it is evaluated to
within `2^-p` of the true value.

For example when using the `CReal 0` type (numbers within 1 of the true value)
one can produce the following:

``` haskell
λ> 0.5 == (1 :: CReal 0)
True
λ> 0.5 * 2 == (1 :: CReal 0) * 2
False
```

Contributing
------------

Contributions and bug reports are welcome!

Please feel free to contact me on GitHub or as "jophish" on freenode.

-Joe

[goldberg]: http://www.validlab.com/goldberg/paper.pdf "What Every Computer Scientist Should Know About Floating-Point Arithmetic"
[correctness]: http://www.imada.sdu.dk/~kornerup/RNC4/papers/p07.ps "The Correctness of an Implementation of Exact Arithmetic"
