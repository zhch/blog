Suppose C() is a database constraint between two data items x and y in the database.

Suppose transaction T1 reads x, and then a second transaction T2 updates x and y to new values and commits. If now T1 reads y, it may see an inconsistent state, and therefore produce an inconsistent state as output.

这就叫做Read skew，In terms of histories, we have the anomaly:

```
r1[x]...w2[x]...w2[y]...c2...r1[y]...(c1 or a1)
```