# Theoretische Informatik

## WHILE-Programme

WHILE-Programme sind eine Formalisierung der Berechenbarkeit. Neben ihnen existieren viele weitere Berechnungsmodelle, z. B. Turingmachinen oder GOTO-Programme, die zueinander äquivalent sind. WHILE-Programme lassen nur eine beschränkte Menge an Operationen zu. `x_i` sind dabei Variablensymbole, `c` Konstanten und `P_1` WHILE-Programme.
  
#### Variablenzuweisungen
```
x_1 := x_2 + c
```
```
x_1 := x_2 - c
```

#### LOOP-Anweisungen
```
LOOP x_1 DO P_1 END
```

#### WHILE-Anweisungen
```
WHILE x_1 != 0 DO P_1 END
```

#### Aneinanderreihungen
Die obigen Operationen sind bereits sogenante atomare WHILE-Programme. Mehrere WHILE-Programme lassen sich aneinanderreihen:

```
P_1; P_2
```
