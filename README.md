BlockSign Transaction Specification
===================================

A `BlockSign Transaction` is a transaction on the bitcoin blockchain representing legally binding signatures on any document, contract, or agreement. It can be any bitcoin transaction with at least one `Pay-To-PubKey-Hash Input` and at least one `BlockSign Output`.

The `PubKey Hash` (aka. Bitcoin Address) in the `Pay-To-PubKey-Hash Input` identifies the signer (or signers) while the hash digest embedded in the `BlockSign Output` identifies the document being signed.

It is possible for multiple parties to sign the same document either by creating multiple transactions each having the same `BlockSign Output` or by creating a single transaction signed by multiple parties. While the latter results is fewer transactions on the blockchain, the former allows flexibility and is more convenient for signers because there is no need to pass around half signed transactions.

Similarly a single transaction can contain multiple `BlockSign Outputs`, which simply means the signer(s) signed multiple documents.

Though a `BlockSign Transaction` can have other standard inputs or outputs (ex PayToPubkey ScriptPubKey). Such outputs (from a BlockSign point of view) are simply ignored.

A `BlockSign Output` can be created the following way (with example):

1. First the document being signed is hashed using double sha256.

```
3fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e0
```

2. The magic marker (```0x42 0x53``` for MainNet and ```0xBA 0xBA``` for TestNet3) is prepended in front of the hash:

```
42 53 3fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e0
```

3. The hash (double sha256) of the data from step 2 is calculated.

```
5acf07b5d55e60f647e84eb40c9ffcadceeed007fac1ef3a9018496a58638e74
```

4. The first 4 bytes of the hash from step 3 is the checksum.

```
5acf07b5
```

5. The first 4 bytes of the hash from step 3 is appended to the string from step 2, as a checksum.

```
42 53 3fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e0 5acf07b5
```

6. The resulting 38 byte string is embedded in the `ScriptPubKey` using `OP_RETURN`.

```
OP_RETURN 42533fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e05acf07b5
```



Backward compatibility
----------------------

`BlockSign Outputs` are embedded in the blockchain with the `OP_RETURN` opcode which means bitcoin clients not aware of `BlockSign Outputs` will simply ignore them. Because `OP_RETURN` outputs are provably unspendable, such outputs can be also safely removed from the unspent transaction output (UTXO) set.

Forward compatiblity
--------------------
The protocol uses two full bytes to mark signature outputs. Two bytes leaves enough space, so the protocol can later be extended to support signature revokation, key expiration, etc.

