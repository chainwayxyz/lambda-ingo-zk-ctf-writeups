# Loki's Trapdoor

## Challenge

> After successfully breaking into Loki’s vault and getting access to some of his finest treasures and weapons, you spot a small trapdoor under a carpet. The trapdoor is locked and contains a device with a PLONK prover. It says: “Prove that the point $(1,y)$ belongs to the elliptic curve $y^2=x^3+4$. You see that, in order to prove this, you need that $y^2−x^3−4$ is equal to zero, which corresponds to the circuit for the prover provided by Loki. Can you open the trapdoor?

## Solution

We are provided with the modified version of the `lambda_works_plonk_prover` library. We noticed that `new_strong_fiat_shamir_transcript` function was modified. The modified function does not depend on the public inputs, which makes it weak fiat shamir, contrary to its name. After some research, we came across a [video](https://youtu.be/RTSdkWZrEn4?t=341) explaining how to exploit this situation.

#### Code

We just need to pass the constraint check. 

```rust
let constraints_check = p_zeta - (&zh_zeta * &p.t_zeta) == FieldElement::zero();
```
For that, we found the suitable `y` value that makes the statement hold.

```rust
let constraints_check = true;
let temp = -(p_zeta - (&zh_zeta * &p.t_zeta));
let new_y = (temp - &l1_zeta)/((l1_zeta.clone()*(&input.omega)*((&zeta - &input.domain[0]) / (&zeta - &input.domain[1]))));
println!("new y = {}", new_y.representative());
```

```
y = 0x800f460ea15b3ab5220e2bff91b0cf8f71cbcc163da4fa7afd6fe1b45961030
```
By doing this, we made the proof for $(x,y)=(1,1)$ (which is clearly wrong) valid by changing y to this value.
```rust
let proof_data = ChallengeProofData {
    x: x.clone(),
    y: FieldElement::from_hex_unchecked("800f460ea15b3ab5220e2bff91b0cf8f71cbcc163da4fa7afd6fe1b45961030"),
    proof,
};
```

