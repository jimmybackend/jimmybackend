# MCMA Memories

> **Encrypted persistent memory for AI systems.**

This directory is part of **MCMA (Memory Context & Memory Architecture)**, an experimental architecture for storing long-term AI memory outside the transient context window of a model.

MCMA explores a simple idea: an AI system should be able to preserve useful context over time without requiring the plaintext memory itself to live inside GitHub, a database row, or a model prompt.

## What is stored here

The `.mcma` files in this directory are **encrypted memory envelopes**. They are intentionally not human-readable.

A typical envelope contains only non-secret metadata and authenticated ciphertext, for example:

- format/version information
- logical memory path
- temperature/lifecycle classification
- creation timestamp
- key identifier (not the key itself)
- IV / nonce
- authentication tag
- ciphertext

The plaintext memory, master key, derived encryption keys, API tokens, GitHub tokens and private credentials are **not stored in this repository**.

## Current cryptographic design

The current MCMA V2 prototype uses established cryptographic primitives:

- **AES-256-GCM** for authenticated encryption
- **HKDF-SHA256** for key derivation
- versioned key identifiers so encrypted objects can reference a key generation without exposing key material

The encrypted envelope can be stored remotely while the cryptographic secrets remain on the trusted application/server side.

## Memory lifecycle

MCMA experiments with four memory temperatures:

```text
Hot → Warm → Cold → Frozen
```

They are intended to represent how actively a memory participates in current reasoning and retrieval.

- **Hot** — active, frequently relevant memory
- **Warm** — useful context that is accessed less often
- **Cold** — retained information with low immediate relevance
- **Frozen** — preserved memory kept outside normal active retrieval

The lifecycle is designed to allow future agents to reorganize, promote, cool, archive and re-index memories as context changes.

## Working flow

The current prototype has demonstrated this end-to-end path:

```text
Human / AI context
        │
        ▼
     MCMA V2
        │
        ├── classify memory
        ├── derive encryption key
        ├── AES-256-GCM encrypt
        │
        ▼
  .mcma envelope
        │
        ▼
 GitHub persistent storage
```

A working web interface can accept a memory, classify it by temperature, encrypt it on the MCMA side and store only the resulting `.mcma` envelope in GitHub.

On **2026-08-23**, the V2 flow successfully stored a real encrypted memory under:

```text
memories/hot/manual/
```

This confirmed the complete path from plaintext input to encrypted persistent storage.

## Why this matters

Most conversational AI memory is temporary, provider-specific, or tightly coupled to a database schema. MCMA is an experiment in treating memory as an independent architectural layer.

The broader direction includes:

- persistent AI memory across sessions and applications
- RAG and semantic indexing over selected memory layers
- memory agents responsible for organization and retrieval
- automated evaluation and re-indexing when retrieval fails
- independent encrypted memory stores per user or system
- lifecycle management using Hot / Warm / Cold / Frozen states
- separation between storage, indexing, reasoning and cryptographic authority

The goal is not merely to save chat history. The goal is to explore how an AI system can build, organize, protect and recover a durable memory architecture over time.

## Security boundary

The repository should be considered **untrusted storage** from the cryptographic point of view.

A person who obtains an `.mcma` file receives the ciphertext and the metadata needed to identify the encryption format, but **not the secret material required to decrypt it**.

Security therefore depends on keeping the master key and authorized decryption environment separate from this repository, protecting the server that performs cryptographic operations, rotating credentials when appropriate and continuing to use well-reviewed cryptographic primitives rather than inventing custom encryption algorithms.

No master keys, access tokens, private SSH keys or plaintext memories should ever be committed here.

## Engineering status

MCMA is **experimental R&D with a working V2 encrypted-storage path**. It is being developed incrementally alongside broader work on MiChat, persistent memory, RAG, semantic retrieval and AI agents.

The project follows a practical engineering cycle:

```text
idea → architecture → implementation → test → observe → correct → retest
```

This directory intentionally exposes the architecture and encrypted artifacts while keeping cryptographic secrets outside the repository.

---

**MCMA · Persistent encrypted memory for AI systems**

Built as part of the experimental AI/backend work of **Jimmy Backend**.