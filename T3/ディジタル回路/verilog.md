---
documentclass: ltjsarticle
header-includes:
  - \usepackage[margin=1in]{geometry}
  - \usepackage{longtable}
  - \usepackage{booktabs}
figureTitle: "図"
tableTitle: "表"
listingTitle: "コード"
figPrefix: "図"
eqnPrefix: "式"
tblPrefix: "表"
lstPrefix: "コード"
---

# Verilog HDL における if-else 文の結合

T305 岡崎英之介

## はじめに

&emsp; ハードウェア記述言語である Verilog HDL において、if-else 文は明示的にブロックを指定せずに記述することができる。このため、if-else 文をネストして記述すると、if と else の結合の組み合わせが明示的でなくなる。これについて、正しい認識を持たずに記述することは予期せぬバグを招く可能性があるので、文法について調査を行った。

## 調査方法

&emsp; Verilog HDL には多くの実装が存在するが、それらは IEEE 1364^[ https://ieeexplore.ieee.org/document/1620780  ] で標準化された仕様に基づいているので、これについて調査を行った。

## 調査結果

&emsp; IEEE 1364 P.125 9.4 Conditional statement において、以下の記述がある。


```
Because the else part of an if-else is optional, there can be confusion when an else is omitted from a nested if
sequence. This is resolved by always associating the else with the closest previous if that lacks an else. In the
example below, the else goes with the inner if, as shown by indentation.
```


これによれば else 文は、 else 文を 持たない直前の if 文と結合する。
下の例では、else 文はインデントで示されているように、内側の if 文と結合する。


```c
if (index > 0)
    if (rega > regb)
        result = rega;
    else // else applies to preceding if
        result = regb;
```


また、このような結合を避けたい場合には、以下の例のように begin end ブロックを用いることが推奨されている。 if 文はブロックの外にある else 文とは結合しないので、この例における else 文は最上部の if 文と結合する。


```c
if (index > 0) 
    begin
    if (rega > regb)
        result = rega;
    end
else result = regb;
```


## 結論

&emsp; Verilog HDL において、 else 文は直前にある else 文を持たない if 文と結合する。これを避けたい場合は、begin-end を用いて明示的に if 文を別のブロックに分ける必要がある。
