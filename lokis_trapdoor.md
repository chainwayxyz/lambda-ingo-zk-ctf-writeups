# Loki's Trapdoor

## Challenge

> After successfully breaking into Loki’s vault and getting access to some of his finest treasures and weapons, you spot a small trapdoor under a carpet. The trapdoor is locked and contains a device with a PLONK prover. It says: “Prove that the point $(1,y)$ belongs to the elliptic curve $y^2=x^3+4$. You see that, in order to prove this, you need that $y^2−x^3−4$ is equal to zero, which corresponds to the circuit for the prover provided by Loki. Can you open the trapdoor?

## Solution

We are provided with the modified version of the `lambda_works_plonk_prover` library. We noticed that `new_strong_fiat_shamir_transcript` function is modified. The modified function does not depend on the public inputs, which makes it weak fiat shamir, contrary to its name. After some research, we came across a [video](https://youtu.be/RTSdkWZrEn4?t=341) explaining how to exploit this situation. 

#### Code

```rust
let
```

#### Overview

