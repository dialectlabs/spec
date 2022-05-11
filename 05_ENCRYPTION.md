# Message Encryption

End-to-end encryption is a necessary part of any secure messaging protocol. In web3, one of the simplest techniques for achieving end-to-end encryption is the [Diffie-Hellman key exchange scheme](https://en.wikipedia.org/wiki/Diffie–Hellman_key_exchange).

![Diffie-Hellman key exchange. Source: wikipedia.org](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Public_key_shared_secret.svg/500px-Public_key_shared_secret.svg.png)

In this scheme, two individuals who wish to message with each other are able to derive a shared secret key using a combination of their own private key and the other participant's public key. This shared secret key may then be used in an encryption/decryption algorithm without the need to communicate any sensitive information over a network.

## Disclaimer

It is an open question whether asymmetric keypairs typically used for signing should also be used to derive symmetric keys for encryption. With that said, the appeal of Diffie-Hellman in web3 is that users are already custodying keypairs for signing, and no new state needs to be maintained for encryption. For the purposes of this document, we make the assumption that the Diffie-Hellman scheme is an acceptable encryption technique.

## Problem

There is no standard, exposed method for wallets to perform encryption. Today, most web wallets support the following methods:

```
- signTransaction
- signAllTransactions
- signMessage
```

The first two methods are the familiar transaction signing methods for submitting transactions to the blockchain. The lesser-known `signMessage` method is a more general method for proving ownership, and is typically used for creating an authenticated payload for communicating with other services, proving ownership of a given wallet address.

### The Sollet `diffieHellman` API

The [Sollet wallet](https://sollet.io) is the only Solana wallet that exposes an encryption method. It does so via the [`diffieHellman` method](https://github.com/project-serum/spl-token-wallet/tree/0a4c2a00c09f2ce690dce686990a32b15e836f03/src/utils/diffie-hellman):

```javascript
// uses the wallet instance's privateKey
async diffieHellman(publicKey: UInt8Array): Promise<{publicKey: UInt8Array, secretKey: UInt8Array}> {}
```

However, this `diffieHellman` method is experimental, is not intended for production use, and returns keys in a form that poses a security risk. Specifically, it converts an `Ed25519` keypair & public key to a `Curve25519` keypair & public key, which is an intermediate step toward generating the shared secret key used for encryption.

This intermediate representation is reversible, and therefore the sollet `diffieHellman` method returns what is equivalent to the user's keypair to the broader javascript runtime, which is a critical security risk.

## Solution

We propose the addition of an alternative method to wallet APIs for performing encryption (which would replace the Sollet `diffieHellman` method):

```javascript
// uses the wallet instance's privateKey
async diffieHellman(publicKey: Uint8Array): Promise<secretKey: Uint8Array> {}
```

Rather than returning the `Curve25519` representation of the user's keypair, this method returns the derived, shared secret key.

In a web extension environment, any dapps calling this method over the extension's JSON RPC API would never have access to any representation of the users' private key, in the same way that `signTransaction` and other existing methods never do.

In a mobile wallet runtime with private key custody, the private key is never being directly accessed by any third party code.

### Treating the shared secret key as private

Alternatively, if we treat the shared secret key as private information, wallets could instead expose a direct encryption and decryption methods that take the encryptable payload or encrypted payload respectively:

```javascript
// both methods use the wallet instance's privateKey
async encryptMessage(publicKey: Uint8Array, message: Uint8Array, nonce: Uint8Array): Promise<{encryptedMessage: Uint8Array}> {}
async decryptMessage(publicKey: Uint8Array, encryptedMessage: Uint8Array, nonce: Uint8Array): Promise<{message: Uint8Array}> {}
```

These functions, while more opinionated about the intended usage of the shared secret key, have the added benefit of never exposing the shared secret key to any third party code executing in the runtime.

## Limitations & looking ahead

The Diffie-Hellman scheme is extendable beyond the one-to-one exchange described above, but [increases significantly in complexity](https://crypto.stackexchange.com/a/1027): work must be done by every participant any time a new member is added.

This poses a limitation on the user experience in web3, especially if messages and state are stored on-chain. Hence, encrypted group chats, especially those with fluid membership, may be difficult to manage.

### Hub nodes

Coming soon.
