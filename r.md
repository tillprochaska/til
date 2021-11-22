# R

## Binominalverteilungen

### Probability Mass Function (PMF) P(Y = k):
Wie hoch ist die Wahrscheinlichkeit, dass der Erfolgsfall genau `k` mal eintritt?

```r
dbinom(k, size, prob)
```

### Cumultative Density Function (CDF) P(Y ≤ k) bzw. P(Y ≥ k):
Wie hoch ist die Wahrscheinlichkeit, dass der Erfolgsfall maximal `k`  bzw. mindestens `k` mal eintritt?

```r
pbinom(k, size, prob)
pbinom(k,size, prob, lower.tail = FALSE)
```

### Quantile Function: Q = P<sup>-1</sup>
Wie groß muss `k` sein, damit P(Y ≤ k) gleich `q` ist? Die Quantilfunktion ist die Umkehrfunktion der CDF.

```r
qbinom(q, size, prob)
```

## Normalverteilungen

### Probability Density Funciton (PDF):
Wie hoch ist die Wahrscheinlichkeitsdichte an der Stelle `x`?

```r
dnorm(x, mean, sd)
```

### Cumultative Density Function (CDF):
Wie hoch ist die Wahrscheinlichkeit, dass die Zufallsvariable den Wert `x` oder kleiner bzw. größer annimmt?

```r
pnorm(x, mean, sd)
pnorm(x, mean, sd, lower.tail = FALSE)
```

### Quantile Function:
Wie groß muss `x` sein, sodass die Wahrscheinlichkeit P(Y ≤ k) gleich `q` ist?

```r
qnorm(q, mean, sd)
```
