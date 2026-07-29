# Worked example: sieve of Eratosthenes

Every rule in `SKILL.md` applied to one piece of code.

The "before" is the `PrimeGenerator` from Robert C. Martin's *Clean Code*, as
reproduced and argued over by Martin and John Ousterhout in
[A Philosophy of Software Design vs Clean Code](https://github.com/johnousterhout/aposd-vs-clean-code/blob/main/README.md).
The "after" is this skill's rendering, not theirs.

Before:

```java
public class PrimeGenerator {
    protected static int[] generate(int n) {
        int[] p = new int[n];
        ArrayList<Integer> mult = new ArrayList<Integer>();
        p[0] = 2;
        mult.add(2);
        int k = 1;
        for (int j = 3; k < p.length; j += 2) {
            boolean jprime = false;
            int ord = mult.size();
            int square = p[ord] * p[ord];
            if (j == square) {
                mult.add(j);
            } else {
                jprime = true;
                for (int mi = 1; mi < ord; mi++) {
                    int m = mult.get(mi);
                    while (m < j)
                        m += 2 * p[mi];
                    mult.set(mi, m);
                    if (j == m) {
                        jprime = false;
                        break;
                    }
                }
            }
            if (jprime)
                p[k++] = j;
        }
        return p;
    }
}
```

After:

```java
public class PrimeGeneratorWithSieveOfEratosthenes {

    private static final int FIRST_ODD_PRIME_INDEX = 1;

    private int[] primes;
    private int[] nextMultipleToWatchFor;
    private int primesFound;
    private int primesSmallEnoughToDivideCandidate;

    public int[] generate(int howManyPrimes) {
        primes = new int[howManyPrimes];
        nextMultipleToWatchFor = new int[howManyPrimes];
        primesFound = 0;
        primesSmallEnoughToDivideCandidate = 0;

        if (howManyPrimes > 0) {
            rememberPrimeAndWatchForItsMultiples(2);
        }
        for (int candidate = 3; primesFound < howManyPrimes; candidate += 2) {
            noteWhichPrimesAreSmallEnoughToDivide(candidate);
            if (isNotAMultipleOfAnySmallerPrime(candidate)) {
                rememberPrimeAndWatchForItsMultiples(candidate);
            }
        }
        return primes;
    }

    private void noteWhichPrimesAreSmallEnoughToDivide(int candidate) {
        while (primesSmallEnoughToDivideCandidate < primesFound) {
            int nextLargerPrime = primes[primesSmallEnoughToDivideCandidate];
            if (nextLargerPrime * nextLargerPrime > candidate) {
                return;
            }
            primesSmallEnoughToDivideCandidate++;
        }
    }

    private boolean isNotAMultipleOfAnySmallerPrime(int candidate) {
        for (int i = FIRST_ODD_PRIME_INDEX; i < primesSmallEnoughToDivideCandidate; i++) {
            if (isMultipleOfPrime(candidate, i)) {
                return false;
            }
        }
        return true;
    }

    private boolean isMultipleOfPrime(int candidate, int primeIndex) {
        stopWatchingMultiplesBelow(candidate, primeIndex);
        return nextMultipleToWatchFor[primeIndex] == candidate;
    }

    private void stopWatchingMultiplesBelow(int candidate, int primeIndex) {
        while (nextMultipleToWatchFor[primeIndex] < candidate) {
            nextMultipleToWatchFor[primeIndex] += stepToNextOddMultipleOf(primeIndex);
        }
    }

    private void rememberPrimeAndWatchForItsMultiples(int prime) {
        primes[primesFound] = prime;
        nextMultipleToWatchFor[primesFound] = firstMultipleNoSmallerPrimeWillCatch(prime);
        primesFound++;
    }

    private int firstMultipleNoSmallerPrimeWillCatch(int prime) {
        return prime * prime;
    }

    private int stepToNextOddMultipleOf(int primeIndex) {
        return 2 * primes[primeIndex];
    }
}
```

Note what the names accomplished: the algorithm is identified in the class
name, the entry point reads as four intentions, `jprime` vanished into a
predicate's return value, and the reason the multiples table starts at the
prime's square is stated by `firstMultipleNoSmallerPrimeWillCatch`.

Note also the one thing the names could not do: they assert that no smaller
prime catches an earlier multiple, but they cannot prove it. That is the shape
of comment rule 15 permits — a short proof attached to that method, and nowhere
else.
