

- A _**Z**ero-**k**nowledge **V**irtual **M**achine_ is a virtual-machine that, besides executing programs, also produces a [[ZKP/Zero Knowledge Proofs|zero-knowledge proof]] of their execution. It is a more general form of a **zkEVM** which only executes and proves the execution of Ethereum transactions and contracts on its state trie.

- **sp1** is a **zkVM** written in Rust, developed by the **Succinct Labs** foundation. It can prove the execution of programs in the `RISC-V` instruction-set, by generating proofs in either its native format or in **Plonk** or **Groth16**. It also provides a library and toolchain to ease the creation of sp1-friendly programs from high-level code written in Rust
- Other popular zkVM's that operate on RISC-V are (RISC Zero)[https://dev.risczero.com/api/zkvm/] and (Jolt)[https://jolt.a16zcrypto.com/]

| **Si** | **Stage**                       | **Description** |
| ------ | ------------------------------- | --------------- |
| 1.     | **Compile Program**             | Write a Rust program that uses the [`sp1-zkvm`](https://docs.rs/sp1-zkvm/latest/sp1_zkvm/) library to compile the program, while using [utilities](https://docs.rs/sp1-zkvm/latest/sp1_zkvm/#modules) provided by the library to interact with the virtual-machine including I/O, heap-allocations & system-calls. This step may be done in other language that can compile to `RISC-V` (which includes all languages that have an LLVM backend), but then the `sp1-zkvm` library may not be used and linking would have to be done manually. [Sample Rust program that will get compiled to RISC-V](https://github.com/succinctlabs/sp1-project-template/blob/main/program/src/main.rs) |
| 2.     | **Generate Proof of Execution** | Invoke the virtual-machine using [`sp1-sdk`](https://docs.rs/sp1-sdk/latest/sp1_sdk/) to execute the compiled program, which produces its output and a proof of its execution. Before execution, the proving and verifying keys are generated from the program. [Sample Rust program that creates the prover-client, makes it generate the proof and then verifies the same proof](https://github.com/succinctlabs/sp1-project-template/blob/main/script/src/bin/main.rs) |
| 3.     | **Verify Proof of Execution**   | Verify the execution of the program using its provided proof and verifying key (the inputs provided to the program are not needed for verification) |



# Proof Generation
**sp1** separates the proof generation process into 4 stages:

1. Generate shard proofs which split up and prove the valid execution of a RISC-V program.
2. Compress shard proofs into a single shard proof.
3. Wrap the shard proof into a SNARK-friendly field.
4. Wrap the last shard proof, proven over the SNARK-friendly field, into a PLONK proof

<https://docs.rs/sp1-prover/latest/sp1_prover/>
