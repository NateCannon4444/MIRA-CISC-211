# Part 1: Simplify $F = A'B'C' + A'B'C + A'BC + AB'C + ABC'
## 1. Group terms to factor variables:
```.txt
F = (A'B'C' + A'B'C) + (A'B'C + A'BC) + (A'B'C + AB'C) + ABC'
```
## 2. Factor out common terms:
```.txt 
From (A'B'C' + A'B'C), factor out A'B': A'B'(C' + C) = A'B'(1) = A'B'
From (A'B'C + A'BC), factor out A'C: A'C(B' + B) = A'C(1) = A'C
From (A'B'C + AB'C), factor out B'C: B'C(A' + A) = B'C(1) = B'C
```
## 3. Combine remaining terms:
```.txt
F = A'B' + A'C + B'C + ABC'
This is the simplified Sum-of-Products form
```

# Part 2: Motion-Sensing Light System
## 1. Original Expression:
```.txt
We define the inputs as m (motion) and t (test mode). The output i (illuminate) is the sum of the three conditions provided:
Motion and test mode off: m x t'
Test mode on and no motion: t x m'
Test mode on and motion: t x m
i = mt' + tm' + tm
```

## 2.Simplify:
```.txt
Group the terms containing t:
i = mt' + t(m' + m)

Apply the identity m' + m = 1:
i = mt' + t(1) = mt' + t

Apply the distributive law:
i = t + m

Final Simplified Expression:
i = m + t
```
