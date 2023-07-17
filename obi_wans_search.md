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



#### Code

```rust
let
```

#### Overview

