# Foundations, WP & LC

## Basic Logic

$A \implies B \ \equiv \ \overline{A} \lor B$

$\text{A stronger B} \ \equiv \ A \implies B$

$true$ is the weakest statement \
$false$ is the strongest statement


## Weakest Precondition
$\pmb{WP}[\![ x=read() ]\!](A) \equiv \forall x: A$ \
$\pmb{WP}[\![ write(expr) ]\!](A) \equiv A$ \
$\pmb{WP}[\![ x = expr ]\!](A) \equiv  A[expr/x]$

$A[expr/x]$ means: replaces all occurences of x in A with the *right* part of the expression. \
Example: \
<img src="image-3.png" width=70%>


$\pmb{WP}[\![ c ]\!](B_{false}, B_{true}) \equiv$ \
$ (\overline{c} \land B_{false}) \lor (c \land B_{true}) \equiv$ \
$(c \implies B_{true}) \land (\overline{c} \implies B_{false})$ 

## Local Consistency
<img src="image.png" width=20%> \
$\text{A and B are loc. cons.} \equiv A \implies \pmb{WP}[\![ s ]\!](B) $ \
$\equiv \pmb{SP}[\![ s ]\!](A) \implies B$ \
Assertions generated by the ```WP``` and ```SP``` operator are by default locally consistent.

# Loop Invariants & Termination
If you choose a loop invariant thats too strong your WP proof wont succed (you wont be able to annotate ```true``` at the top)

If you choose a loop invariant that is too weak,  it will not be locally consistent.