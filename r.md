# R

## Binominalverteilungen

### Wahrscheinlichkeit für genau k Erfolge P(Y = k):

```r
dbinom(k, size, prob)
```

### Wahrscheinlichkeit für k oder mehr Erfolge P(Y ≥ k):

```r
pbinom(k,size, prob, lower.tail = FALSE)
```

### Wahrscheinlichkeit von k oder weniger Erfolgen P(Y ≤ k):

```r
pbinom(k, size, prob)
```
