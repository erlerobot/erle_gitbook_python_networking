## TLS Encrypts Your Conversations

The secret to TLS is public-key cryptography.There are several mathematical schemes that have been proved able to support public-key
schemes, but they all have these three features:
- Anyone can generate a key pair, consisting of a private key that they keep to
themselves and a public key that they can broadcast however they want.
- If the public key is used to encrypt information, then the resulting block of binary
data cannot be read by anyone, anywhere in the world, except by someone who
holds the private key.
- If the system that holds the private key uses it to encrypt information, then any
copy of the public key can be used to decrypt the data.

We will focus on how
public keys are used in the TLS system:
Public keys are used at two different levels within TLS: first, to establish a certificate authority (CA)
system that lets servers prove “who they really are” to the clients that want to connect; and, second, to
help a particular client and server communicate securely.

