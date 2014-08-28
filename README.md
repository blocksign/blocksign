BlockSign Transaction Specification
===================================

A `BlockSign Transaction` is a transaction on the bitcoin blockchain representing legally binding signatures on any document, contract, or agreement. It can be any bitcoin transaction with at least one `Pay-To-PubKey-Hash Input` and at least one `BlockSign Output`.

The `PubKey Hash` (Bitcoin Address) in the Input identifies the signer (or signers) while the hash digest embedded in the `BlockSign Output` identifies the document being signed.

It is possible for multiple parties to sign the same document either by creating multiple transactions each having the same `BlockSign Output` or by creating a single transaction signed by multiple parties. While the latter results is fewer transactions on the blockchain, the former allows flexibility and is more convenient for signers because there is no need to pass around half signed transactions.

Similarly a single transaction can contain multiple `BlockSign Outputs`, which simply means the signer(s) signed multiple documents.

A `BlockSign Transaction` can have other standard inputs or outputs (ex PayToPubkey ScriptPubKey). Such outputs (from a BlockSign point of view) are simply ignored.

A `BlockSign Output` is created the following way:

1. The document being signed is hashed using double sha256.

Ex: ```3fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e0```

2. The magic marker (0x42,0x53 for MainNet and 0xBA,0xBA for TestNet3) is prepended in front of the hash:

Ex: ```4253 3fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e0```

3. The hash (double sha256) of the data from step 2 is calculated.

Ex: ```36aaf3fd80cb66ecb9225358e8abed411c469db9d7880a2fb07b71b13a2b16b4```

4. The first 4 bytes of the hash from step 3 is the checksum.

Ex: ```36aaf3fd```

5. The first 4 bytes of the hash from step 3 is appended to the string from step 2, as a checksum.

Ex: ```52533fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e036aaf3fd```

6. The resulting 38 byte string is embedded in the `ScriptPubKey` using `OP_RETURN`.

```OP_RETURN 52533fc4a2721876cec0389049f57c6c8fe8604129a0885368a713fdf94dd79a77e036aaf3fd```




Backward compatibility
----------------------

`BlockSign Outputs` are embedded in the blockchain with the `OP_RETURN` opcode which means bitcoin clients not aware of `BlockSign Outputs` will simply ignore it. Because `OP_RETURN` outputs are provably unspendable, such outputs can be also safely removed from the UTXO (unspent transaction output) set.

Forward compatiblity
--------------------
The protocol uses two full bytes to mark signature outputs. The second byte leaves enough bits, so the protocol can later be extended to support signature revokation, key expiration, etc.

