---
title: Understanding Grover's algorithm on cnot.io
tags:
- QuantumComputing
---

Understanding [cnot.io](https://cnot.io/quantum_algorithms/grover/grovers_algorithm.html#full_quantum_circuit): grover's algorithm circuit. <br>
I will explain much of the concepts using the simulation in [this page](https://algassert.com/quirk)
# Prerequisites
## Traits of Hadamard Gate
<img src="/images/2021-02-15_10.55.48.png" style="width:100%">
<img src="/images/2021-02-15_10.56.01.png" style="width:100%">
<img src="/images/2021-02-15_10.45.22.png" style="width:100%">

Hadamard gate splits the probability, and it is it's own inverse.<br>
As you can see in these pictures, if the input was |0> then after two hadamard gate the result goes back to |0>.<br>

<img src="/images/11.09.32.png" style="width:100%"/>

However if we switches the state of the qubit by applying Z-gate after the H-gate <br>

<img src="/images/2021-02-15_10.47.30.png" style="width:100%">

and apply the H-gate again, the result goes to \|1> <br><br>

## Traits of CNOT Gate
<img src="/images/2021-02-15_11.39.08.png" style="width:100%">

In CNOT gate the control qubit (first one) flips the target qubit (second one), if and only if the control qubit is \|1>.

As you can see in this picture, when we put the control qubit in superposition \|+> by applying H-gate the formula goes to: 
```
CNOT|0+> = 1/(2^(1/2)) ( |00> + |11>)
```
<br>
## Phase Kickback
<img src="/images/2021-02-16_12.03.55.png" style="width:100%">

If we put the target qubit in superposition as well in the circuit above, the state formula goes to:
```
|++> = 1/2 (|00> + |01> + |10> + |11>)
```

<img src="/images/2021-02-16_1.48.39.png" style="width:100%">

If we put the target qubit into \|-> state by applying Z gate, then the state formula goes to:
```
|-+> = 1/2 (|00> + |01> - |10> - |11>) = = |-->
```
As you can see the state of the control qubit is affected, while the phase of the target qubit isn't affected. This is called phase kickback technique. <br>

# The Quantum Oracle
<img src="/images/2021-02-16_10.13.56.png" style="width:60%">
First thing we need to do for the algorithm is to construct the quantum oracle circuit. <br>
This is the quantum oracle circult proposed in the cnot.io's post.

<img src="/images/2021-02-16_12.56.46.png" style="width:20%">
As you can see, after the qubit passes each hadamard operators all possible states exist with the probability: 1/(2^4)

<img src="/images/1.05.07.png" style="width:10%">

Then we setup the Toffoli gate (extended version of CNOT gate). <br>
As the target state we searching for is |1010⟩, the cnot gates are set in this way, with two controls and two anti-controls.

<img src="/images/2021-02-16_11.17.11.png" style="width:70%">
<img src="/images/2021-02-16_10.17.58.png" style="width:70%">

This is where the phase kickback technique shows up. <br>
As you can see, after applying the toffoli gate and making the target qubit as \|-> with applying H-gate and Z-gate the phase of the answer state |1010> has been switched. <br>
To be more specific, the phases of the |10101> |10100>, including the target bit's state, has been switched.

<img src="/images/2021-02-16_1.29.57.png" style="width:50%">

After that, all states passes H-gates again. As I mentioned above in Traits of Hadamard gate, only the states whose phase are unchanged goes back to the original state= \|0000>, by applying H-gate again. <br>

# Diffusion Operator
<img src="/images/2021-02-16_11.47.13.png" style="width:30%">
Next thing to do is constructing the diffusion operator. <br>

<img src="/images/2021-02-16_11.47.18.png" style="width:10%">

Once again we use the kickback technique but this time the target is \|0000> which is the original state. <br>
After passing the toffoli gate, the phase of the state \|0000> is switched, and any other state's phases stay unchanged.

<img src="/images/2021-02-16_11.42.35.png" style="width:60%">
Then we apply the Hadamard gates again, then we can get the answer with certain probability. <br>
Then repeat the whole circuit (quantum oracle + diffusion operator) O(sqrt(N)) times.

# Summary
This is  the concepts of the circuit proposed in cnot.io: grover's algorithm, as I understand. <br>
However, I still cannot figure out why the probability of \|0000> becomes 76.5625% = (7/8)^2 at the end of the Quantum oracle circuit,
or why the probability of getting answer becomes 47.2656% after the diffusion operator. I'll add extra explanation about those probabilities later, if I can. <br>
# References
[https://algassert.com/quirk](https://algassert.com/quirk) <br>
[https://qiskit.org/textbook/ch-gates/phase-kickback.html](https://qiskit.org/textbook/ch-gates/phase-kickback.html) <br>
[https://cnot.io/quantum_algorithms/grover/](https://cnot.io/quantum_algorithms/grover/)
