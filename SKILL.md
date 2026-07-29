---
name: cleancode
description: Naming-first refactoring. Use when asked to clean up, refactor, or rename code for readability — or when code is hard to follow because of short names, boolean control-flow flags, bare literals, or helpers named after mechanism instead of purpose. Restructures code so a reader who does not know the algorithm can understand it from names alone, then verifies behaviour is preserved.
---

# Naming-First Refactoring

Refactor the target code so that a reader who does not know the algorithm can
understand it by reading names alone. Preserve behaviour exactly. Then verify
the refactoring by running it against the original, or against an independent
reference implementation, over a range of inputs including edge cases.

If no target was named, ask which code to refactor before starting.

## The rules

**1. Name the class after what it produces and how.**
The class name is the one place a reader can learn which algorithm they are
looking at without leaving the file. `PrimeGenerator` says what;
`PrimeGeneratorWithSieveOfEratosthenes` says what and how. If the algorithm has
a name, use it.

**2. Make the entry point a plain verb, and its parameters plain questions.**
`generate(int howManyPrimes)`, not `generate(int n)`. The public method should
be short enough to read as a summary of the whole algorithm.

**3. The entry point reads as a sequence of named intentions.**
Each line is a step whose name states what that step accomplishes. A reader
should be able to stop after the entry point and know the shape of the
algorithm.

**4. Name helpers by purpose, not by mechanism.**
`advanceMultiplesToReachAtLeast(candidate)` describes the machinery and leaves
a reader asking "why advance them?". `stopWatchingMultiplesBelow(candidate)`
answers it. If a name describes what the loop does rather than why it runs,
rewrite it.

**5. Treat "why is this happening here?" as a decomposition failure.**
If the purpose of an operation cannot be expressed in the name at its current
location, the operation is in the wrong place. Move it inside the method whose
goal it serves, so the purpose becomes visible, then name it.

**6. A method that does two things names both, or becomes two methods.**
`recordPrime` hid a second duty. `rememberPrimeAndWatchForItsMultiples` admits
it. Prefer splitting; if the two duties share an index or invariant that would
be fragile to split, keep them together and name both.

**7. Predicates do not change state.**
A method named `is…` or `has…` must be safe to call twice. If the answer
genuinely requires advancing a cursor or cache, extract that into a separately
named command, called from the predicate, whose name announces the mutation.
Never let a predicate quietly modify data that later answers depend on.

**8. Names must be accurate, not merely confident.**
A long assertive name that describes the wrong algorithm is worse than a short
opaque one, because an opaque name tells the reader they do not yet know.
Check every name against what the code actually does — a method that never
divides must not be named after division.

**9. Expand every abbreviation and single letter.**
`p`, `k`, `mi`, `ord`, `mult`, `j` become `primes`, `primesFound`, `primeIndex`,
`primesSmallEnoughToDivideCandidate`, `nextMultipleToWatchFor`, `candidate`.
Loop indices may stay short only where the loop is three lines and the array
name carries the meaning.

**10. Delete flags that exist only to carry control flow.**
A boolean set in one branch and tested after the loop usually wants to be a
predicate's return value. `jprime` should disappear into
`isNotAMultipleOfAnySmallerPrime(candidate)`.

**11. Give unexplained literals a named constant.**
An index or offset that encodes a fact about the problem becomes
`FIRST_ODD_PRIME_INDEX`, not a bare `1`.

**12. Share one vocabulary across related names.**
Once the domain metaphor is chosen, reuse its verb everywhere:
`nextMultipleToWatchFor`, `stopWatchingMultiplesBelow`,
`rememberPrimeAndWatchForItsMultiples`. Consistent verbs let a reader
generalise from one name to the rest.

**13. Do not extract a helper that only restates an operator.**
`squareOf(n) { return n * n; }` adds a hop and no meaning. Extract only when
the name says something the expression cannot — `firstMultipleNoSmallerPrimeWillCatch(prime)`
earns its place; `squareOf` does not.

**14. Replace shared mutable static state with instance fields.**
Methods that communicate through statics cannot be read independently. Give the
state a home and descriptive field names, and reset it at the entry point so
repeat calls are safe.

**15. Comments carry what names cannot, and nothing else.**
Delete any comment that restates mechanism. Keep or add comments only for
things no identifier can hold: a proof or invariant the correctness depends on,
a justification for a non-obvious constant, a rejected alternative and why, or
a domain fact external to the program. If a name asserts a claim, a comment may
prove it. Prefer few, dense, load-bearing comments over many thin ones.

**16. Verify, do not assume.**
State how you checked behaviour was preserved, and report the result. Do not
report the refactoring as complete before the verification has actually run.

## Worked example

Read `reference/worked-example.md` for a full before/after (Java, sieve of
Eratosthenes) showing every rule applied to one piece of code, and a note on
the one thing names could not do.
