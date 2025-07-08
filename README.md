# key_dance

## Overview

Proposals for key management systems.
A plethora of key management services, secret stores, key safes and vaults exist. The purpose of this project is to propose ways to use and integrate these tools across organizations in a sane manner.  Constructive criticisim is *very* welcome and essential to ensure that whatever comees out of this is a hardened, secure system.

## Use Cases

Each system should handle the following use cases.

* A developer joins an organization.  They require authentication to organization resources such as AWS, GCS, Github, bare-metal servers.
* A developer leaves an organization.  Their access to services must be revoked.  This includes invalidating any key which they have even had access to.
* CI system deploys a new node.  It needs access to resources such as code repositories, database keys, keys required by applications to access web services, shared secrets required by applications.
* API key compromise.  Key must be invalidated and a new one created and distributed.
* Developer key rotation/ compromise.  Key must be invalidated, new one created and distributed.
* New secret generated.  A database password, an API key etc is generated and added to the system and keyed for the appropriate consumers.

# Attempt 1. SOPS and friends

## Overview

Secrets and keys are stored SOPS encrypted in a VCS.  The encryption key for each secret is encrypted using the public key or KMS managed key of each consumer.
To use a secret, the consumer downloads the secret file and decrypts it with their private key or via a KMS provided key.
Adding a secret consumer to the organization requires decrypting the key for each secret file, and adding a copy of the key encrypted with the new consumers pub key.

Each consumer of the secrets (developers, servers, CI systems etc) has either a key pair or access to a KMS (via IAM?).  These keys allow them to decrypt the secrets to which they have access.

## Issues

Secrets files can get big if there are numerous consumers.
Secrets files must be re-encrypted when a consumer is added/ leaves the system.
Locking, race conditions
One of the consumers of the secret is required to decrypt a secrets file in order to re-encrypt it.  This might require "master keys" which are added to all secrets.
Must track the graph of which consumers have access to which graphs.



[SOPS](https://github.com/getsops/sops) is an encrypted file editor with a lot more going on. 
