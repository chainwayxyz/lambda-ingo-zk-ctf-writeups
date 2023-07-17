# Obi-Wan's Search

## Challenge

> In his quest to stop the Sith’s menace, Obi-Wan Kenobi finds a (Sith) holocron, giving a zero-knowledge proof of the existence of the Sith’s galactic foundry (using galactic Plonk). This place is rumored to contain several artifacts that could aid the Galactic Republic in its war efforts. The position, given by (x,h,y), satisfies the equation y=x*h+b. After some study, Obi-Wan finds the values of y and b (which belong to Sith lore). The only problem is that, even with this knowledge, it may take him quite long to find the mysterious planet, and the situation in the Republic is desperate. He also finds, together with the holocron, a second item containing the SRS used to generate the proof, the prover, and a description of the circuit used. Will he be able to find the position of the foundry before it is too late? The flag consists of the x and h concatenated and written in hex (for example, x=0x123, h=0x789, the FLAG=123789)
>
> ## Additional data
>
> 1. `y: "3610e39ce7acc430c1fa91efcec93722d77bc4e910ccb195fa4294b64ecb0d35"`,
> 2. `b: "1b0871ce73e72c599426228e37e7469be9f4fa0b7c9dae950bb77539ca9ebb0f"`.
>
> The files `srs` and `proof` can be deserialized using Lambdaworks methods as follows.
>
> ```rust
> use std::{fs, io::{BufReader, Read}};
> use lambdaworks_plonk::prover::Proof;
> use lambdaworks_crypto::commitments::kzg::StructuredReferenceString;
> use lambdaworks_math::traits::{Deserializable, Serializable};
> use crate::sith_generate_proof::{SithProof, SithSRS};
> 
> fn read_challenge_data_from_files() -> (SithSRS, SithProof) {
>     // Read proof from file
>     let f = fs::File::open("./proof").unwrap();
>     let mut reader = BufReader::new(f);
>     let mut buffer = Vec::new();
>     reader.read_to_end(&mut buffer).unwrap();
>     let proof = Proof::deserialize(&buffer).unwrap();
> 
>     // Read SRS from file
>     let f = fs::File::open("./srs").unwrap();
>     let mut reader = BufReader::new(f);
>     let mut buffer = Vec::new();
>     reader.read_to_end(&mut buffer).unwrap();
>     let srs = StructuredReferenceString::deserialize(&buffer).unwrap();
>     (srs, proof)
> }
> ```

## Solution

In this challenge, we noticed that `generate` method of `SithRandomFieldGenerator` struct, which is supposed to generate random numbers, always yields 0 (zero). This makes the polynomial blinding part completely trivial, just outputing the input polynomial itself.

$P_a(x)$ polynomial has evaluations indicated below

- $P_a(ω^0)=b$
- $P_a(ω^1)=y$
- $P_a(ω^2)=x$
- $P_a(ω^3)=b$
- $P_a(ω^4)=y$
- $P_a(ω^5)=b$
- $P_a(ω^6)=b$
- $P_a(ω^7)=b$

We know all the evaluations except for $P_a(ω^2)=x$. However, in addition to these, we also know the evaluation at ζ residing in the `a_zeta` field of the proof. Normally, this would be the evaluation of the **blinded** polynomial but since the blinding is bypassed, we get an extra evaluation of our main polynomial.

- $P_a(ζ)=$ proof.a_zeta

Using [**Lagrance Interpolation**](https://en.wikipedia.org/wiki/Lagrange_polynomial), we can reconstruct the polynomial and get its evalution at $ω^2$, effectively obtaining **x**.

```
x = 2194826651B32CA1055614FC6E2F2DE86EAB941D2C55BD467268E9
h = 432904CCA36659420AAC29F8DC5E5BD0DD57283A58AB7A8CE4D1CA
```

#### Code

Interpolating function that takes the evaluations and where it is desired to evaluate the polynomial.

```python
def interpolate(points, x, modulo):
    result = 0
    n = len(points)
    for i in range(n):
        xi, yi = points[i]
        term = yi % modulo
        for j in range(n):
            if i != j:
                xj, _ = points[j]
                term = (term * (x - xj) * pow(xi - xj, -1, modulo)) % modulo
        result = (result + term) % modulo
    return result
```

