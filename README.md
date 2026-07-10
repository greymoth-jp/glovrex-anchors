# glovrex-anchors

External Merkle-root witness log for Glovrex's tamper-evident provenance
chain (Education OS / Glovrex, `lib/provenance.ts`).

A hash-chain proves history has not been silently rewritten, but the same
operator holds both the chain file and the signing key that produced it, so
signing alone cannot rule out a full regenerate-and-resign of the chain. This
repo is the third-party witness that closes that gap: each `provenance-anchors/*.json`
file is a `{root, at}` pair — a Merkle root over the chain plus the time it
was computed — committed here on demand. GitHub's own server-recorded commit
time becomes the independent "this root existed at this time" checkpoint;
a later chain that produces a different root did not exist, unaltered, at
that time.

## keys/

`keys/<publicKeyId>.pem` — the PUBLIC half of the Ed25519 signing key used to
sign provenance records. Publishing it here lets a third party independently
verify record signatures against a key provably tied to this project, without
trusting anything served by the app itself. The matching private key never
leaves the operator's machine and is never committed anywhere.

## provenance-anchors/

Each file is one anchor: `{"root": "<sha256 hex>", "at": "<ISO 8601>"}`. A
Merkle root reveals nothing about chain content (one-way hash) — safe to
publish. Written by `lib/gitWitnessAnchor.ts` via `gh api ... contents PUT`,
triggered on demand (never automatically) from `graph.anchor_provenance`
(MCP) or `POST /api/provenance {op:'anchor'}`.
