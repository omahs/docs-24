# Main Circuits

This iden3 circuits are the heart of the protocol. The main ones are: 

- [`stateTransition.circom`](main-circuits.md#statetransition), checks the execution of the identity state transtion by taking the old identity state and the new identity state as inputs.
- [`authentication.circom`](main-circuits.md#authentication), checks that the prover is owner of an identity.
- [`credentialAtomicQueryMTP.circom`](main-circuits.md#credentialatomicquerymtp), checks that a claim issued to the prover (and added to issuer's Claims Tree) satisfies a query set by the verifier.
- [`credentialAtomicQuerySig.circom`](main-circuits.md#credentialatomicquerysig) checks that a claim issued to the prover (and signed by the Issuer) satisfies a query set by the verifier.

> You can find all the source code on [Github - Iden3 Circuits](https://github.com/iden3/circuits). All the proving and verification keys necessary to use the circuits were generated after a Trusted Setup Ceremony. Details here:  [Iden3 Protocol Phase2 Trusted Setup Ceremony](https://github.com/iden3/phase2ceremony#auth-circuit)

## stateTransition

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/stateTransition.circom)

- [**Example of instantiation**](https://github.com/iden3/circuits/blob/master/circuits/stateTransition.circom)

#### Instantiation Parameters

- `nLevels` Merkle tree depth level for Claims tree

#### Inputs

| Input                          | Description              | Public or Private
| -----------                    | -----------          |  ----------
| userID                      | Prover's Identifier                | Public
| oldUserState             | Prover's Identity State (before transition)                 | Public
| newUserState     | Prover's Identity State (after transition)           | Public
| isOldStateGenesis               | "1" indicates that the old state is genesis: it means that this is the first State Transition, otherwise "0"              | Public
| claimsTreeRoot                | Prover's Claims Tree Root                | Private
| authClaimMtp[nLevels] | Merkle Tree Proof of Auth Claim inside Prover's Claims tree                 | Private
| authClaim[8]    | Prover's Auth Claim                | Private
| revTreeRoot    | Prover's Revocation Tree Root                 | Private
| authClaimNonRevMtp[nLevels]    | Merkle Tree Proof of non membership of Auth Claim inside Prover's Revocation Tree                | Private
| authClaimNonRevMtpNoAux              | Flag that indicates whether to check the auxiliary Node                  | Private
| authClaimNonRevMtpAuxHv               | Auxiliary Node Value              | Private
| authClaimNonRevMtpAuxHi          | Auxiliary Node Index           | Private
| rootsTreeRoot          | Prover's Roots Tree Root            | Private
| signatureR8x            | Signature of the challenge (Rx point)           | Private
| signatureR8y            | Signature of the challenge (Ry point)           | Private
| signatureS            | Signature of the challenge (S point)             | Private

#### Scope

- If oldState is genesis, verifies that userID is derived from the oldUserState (= genesis state). Performed using [`cutId()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L184), [`cutState()`](https://github.com/iden3/circuits/blob/master/circuits/lib/utils/treeUtils.circom#L198)and [`isEqual()`](https://github.com/iden3/circomlib/blob/master/circuits/comparators.circom#L37) templates
- newUserState is different than zero using [`isZero()`](https://github.com/iden3/circomlib/blob/master/circuits/comparators.circom#L24) comparator
- oldUserState and newUserState are different using `isEqual()`
- Verifies user's identity ownership using [`idOwnershipBySignature(nLevels)`](./main-circuits.md#idownershipbysignature) template. The message signed by the user is `H(oldstate, newstate)` where `H` is a Poseidon hash function executed inside the [`Poseidon(nInputs)`](https://github.com/iden3/circomlib/blob/master/circuits/poseidon.circom#L198) template

#### Circuit Specific Files (From Trusted Setup)

- [Final zkey `circuit_final.zkey`](https://iden3-circuits-bucket.s3.eu-west-1.amazonaws.com/circuits/v0.1.0/auth/circuit_final.zkey)
- [Verification Key `verification_key.json`](https://iden3-circuits-bucket.s3.eu-west-1.amazonaws.com/circuits/v0.1.0/auth/verification_key.json)
- [WASM Witness Generator `circuit.wasm`](https://iden3-circuits-bucket.s3.eu-west-1.amazonaws.com/circuits/v0.1.0/auth/circuit.wasm)

## authentication

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/authentication.circom)

- [**Example of instantiation**](https://github.com/iden3/circuits/blob/master/circuits/auth.circom)

> coming soon!

## credentialAtomicQueryMTP

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/query/credentialAtomicQueryMTP.circom)

- [**Example of instantiation**](https://github.com/iden3/circuits/blob/master/circuits/credentialAtomicQueryMTP.circom)

This circuit checks that an issuer has issued a claim for identity and validates the ownership of that identity in the following manner:

1. Checks the identity ownership by idOwnershipBySignature template
2. Verifies the claim subject, the schema and the expiration time.
3. Checks if the issuer claim exists in the issuer claims tree.
4. Checks if the issuer claim is not revoked by an issuer.
5. Checks if the issuer claim satisfies a query.


## credentialAtomicQuerySig

- [**Github**](https://github.com/iden3/circuits/blob/master/circuits/lib/query/credentialAtomicQuerySig.circom)

- [**Example of instantiation**](https://github.com/iden3/circuits/blob/master/circuits/credentialAtomicQuerySig.circom)

This circuit checks that an issuer has issued a claim for identity and validates ownership of that identity in the following manner:

1. Checks the identity ownership by idOwnershipBySignature template.
2. Verifies the claim subject, the schema and the expiration time
3. Checks if the issuer claim exists in the issuer claims tree.
4. Verifies the claim signature by the issuer.
5. Verifies if the issuer state matches with the one from the blockchain as the public input.
6. Checks if the issuer claim satisfies a query.







