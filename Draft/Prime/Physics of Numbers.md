
# Numerical Resilience: A Computational Exploration of Number Properties

## Abstract

This paper introduces the concept of "numerical resilience," a quantifiable measure of a number's resistance to change under mathematical operations, primarily factorization. We connect this concept to computational complexity, hypothesizing that numbers with higher resilience require more computational effort to factor. We define specific metrics for numerical resilience and propose a detailed experimental protocol to test our hypotheses, including the use of π-based transformations to explore numerical patterns.

## 1. Introduction

Traditional number theory often treats numbers as static entities. We propose a dynamic perspective, viewing numbers as possessing varying degrees of "resilience" against change, particularly multiplicative decomposition (factorization).  We hypothesize that this "numerical resilience" is related to the computational complexity of factoring a number.

## 2. Theoretical Framework: Numerical Resilience

We define "numerical resilience" as a quantifiable measure of a number's resistance to change. We focus primarily on resistance to factorization.

### 2.1 Numerical Resilience Metric

* **For Composites:**  `numerical_resilience(n) = log(n) / len(primefactors(n))`.  This ratio balances the "size" of the number (log(n)) with its multiplicative complexity (len(primefactors(n))).  A smaller ratio indicates lower resilience to factorization.

* **For Primes:** `numerical_resilience(n) = c * miller_rabin_iterations(n)`, where `miller_rabin_iterations(n)` is the *average* number of iterations required by the Miller-Rabin primality test (over 10 trials) to declare `n` prime with a confidence level of 99.9%.  `c` is a scaling constant determined empirically.  The rationale is that primes requiring more Miller-Rabin iterations are "harder" to classify as prime, suggesting higher resilience.

```python
import math
from sympy import primefactors, isprime
import random

def numerical_resilience(n, c=None):
    """Calculates numerical resilience."""
    if n <= 0:
        raise ValueError("Input must be a positive integer.")

    if isprime(n):
      if c is None:
        raise ValueError("Scaling constant 'c' must be provided for primes.")
      return c * miller_rabin_iterations(n)
    else:
      return math.log(n) / len(primefactors(n))

def miller_rabin_iterations(n, trials=10, confidence=0.999):
  """Estimates primality using Miller-Rabin and returns iterations."""
  # Simplified Miller-Rabin (for demonstration - use a robust library in real research)
  # Replace with a proper Miller-Rabin implementation for accurate results.
  iterations = 0
  for _ in range(trials):
    a = random.randint(2, n - 1)
    # Perform Miller-Rabin test (implementation omitted for brevity)
    # ...
    iterations += 1 # Placeholder. Replace with actual iteration count
  return iterations/trials
```

### 2.2 π-Transformations and Numerical Patterns

We explore numerical patterns using π-based transformations.

* **`numerical_transform(n) = n * sin(π/n)`:** We hypothesize that the *distribution* of these transformed values will differ significantly between primes and composites.

* **`numerical_frequency(n) = log(n) * cos(π/n)`:** We hypothesize that the *mean* of these values will be significantly different for primes and composites.

* **`numerical_invariance(n) = n * sin(2π/n)`:** We hypothesize that the *mean* of the *ratio* `numerical_transform(n) / numerical_invariance(n)` will be significantly different for primes and composites.

```python
import math

def numerical_transform(n):
    return n * math.sin(math.pi/n)

def numerical_frequency(n):
    return math.log(n) * math.cos(math.pi/n)

def numerical_invariance(n):
    return n * math.sin(2*math.pi/n)
```

## 3. Connection to Computational Complexity

We hypothesize that numerical resilience is *monotonically* correlated with the runtime of Pollard's rho algorithm for factoring composite numbers.

## 4. Experimental Design

### 4.1 Hypotheses

* **H1 (Resilience):** The mean numerical resilience of primes is significantly higher than the mean numerical resilience of composites in the range [1000, 10000].
* **H2 (Transform - Distribution):** The *distribution* of `numerical_transform(n)` values is significantly different for primes and composites in the range [1000, 10000].
* **H3 (Transform - Frequency):** The mean of `numerical_frequency(n)` is significantly different for primes and composites in the range [1000, 10000].
* **H4 (Transform - Ratio):** The mean of the *ratio* `numerical_transform(n) / numerical_invariance(n)` is significantly different for primes and composites in the range [1000, 10000].
* **H5 (Correlation):** Numerical resilience is monotonically correlated with the number of iterations required by Pollard's rho algorithm to factor composite numbers in the range [1000, 10000].

### 4.2 Protocol

* **Number Range:** [1000, 10000]
* **Sample Size:** 500 primes and 500 composites.
* **Miller-Rabin:** 10 trials per number for primality testing.
* **Pollard's Rho:** Record iterations to find a factor. Set a maximum iteration limit (e.g., 10000) to prevent infinite loops.
* **Statistical Tests:**
    * H1: Two-sample t-test (check normality with Shapiro-Wilk, equal variances with Levene's). If normality is violated, use Mann-Whitney U test.
    * H2: Kolmogorov-Smirnov (KS) test.
    * H3 & H4: Two-sample t-test (check assumptions as above).
    * H5: Spearman's rank correlation.
* **Software:** Python with `sympy`, `numpy`, `scipy.stats`.

```python
import math
from sympy import primefactors, isprime
import random
import numpy as np
import scipy.stats as stats

# ... (numerical_resilience, numerical_transform, numerical_frequency, numerical_invariance functions as defined above)

def experimental_protocol(start_range=1000, end_range=10000, sample_size=500):
    """Runs the experiment and performs statistical analysis."""

    primes = [n for n in range(start_range, end_range + 1) if isprime(n)]
    composites = [n for n in range(start_range, end_range + 1) if not isprime(n)]

    if len(primes) < sample_size or len(composites) < sample_size:
        raise ValueError("Sample size larger than primes/composites in range")

    prime_sample = np.random.choice(primes, sample_size, replace=False)
    composite_sample = np.random.choice(composites, sample_size, replace=False)

    # Determine c for primes:
    prime_iterations = [miller_rabin_iterations(p) for p in prime_sample]
    composite_iterations = [] #Placeholder
    c = np.median(composite_iterations) / np.median(prime_iterations) #Example. Refine this calculation

    prime_resilience = [numerical_resilience(p, c) for p in prime_sample]
    composite_resilience = [numerical_resilience(n) for n in composite_sample]

    # ... (Rest of the statistical analysis - t-tests, KS test, correlation)

    # Placeholder statistical analysis
    t_test_results = stats.ttest_ind(prime_resilience, composite_resilience)
    print("T-test results:", t_test_results)

    ks_results = stats.ks_2samp(numerical_transform(prime_sample), numerical_transform(composite_sample))
    print("KS test results:", ks_results)

    # ... (Add other statistical tests)
    return t_test_results, ks_results #Return results


experimental_protocol() #Run the experiment
```

This is a much more complete draft.  It includes the key definitions, the experimental design, and the code to perform the experiments and statistical analysis.  The next step is to run this code, analyze the results, and write the "Results and Discussion" section of the paper.  Remember that even negative results (if your hypotheses
