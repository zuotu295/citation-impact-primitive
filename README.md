# Citation Impact Primitive

[ErrataScope application](https://zuotu295.github.io/erratascope-genlayer/) · [Application source](https://github.com/zuotu295/erratascope-genlayer)

A GenLayer Intelligent Contract that preserves how a specific publisher notice affects an immutable set of citation uses. One paper can be cited for multiple reasons; a correction need not affect every use equally.

## Mechanism

1. An owner seals the original PLOS ONE DOI, title, and 1–4 claims with source locators.
2. The owner supplies a distinct publisher-notice DOI and the expected dossier revision.
3. The leader retrieves the official notice and proposes a complete claim matrix.
4. Validators independently retrieve the same source and assess all claim effects. Source identity, notice relationship, effect agreement, exact quotations and a separate evidence audit are required.
5. Deterministic code maps each effect to an action and appends a revision. Cumulative actions retain prior warnings; a later unrelated correction cannot erase them.

Records contain owner, immutable input hash, exact normalized notice snapshot, notice hash, claim findings, per-notice actions and conservative cumulative actions. The primitive can be consumed independently of the ErrataScope browser application.

## Run local tests

Python 3.12+, no external test dependencies:

```sh
python -m unittest discover -s tests -v
```

The 36 tests mock the GenLayer host, retrieval and model responses. They exercise owner isolation, immutable records, source mismatch, unsupported evidence, validator disagreement, revision concurrency, notice deduplication, full retractions, concern handling and conservative cumulative actions. They do not demonstrate execution inside the fixed GenVM runner or live validator consensus.

## Deploy and call

Deploy `contracts/errata_scope.py` with **no constructor arguments** on Stable Studionet, chain ID **61999**. The first-line dependency pins the GenVM Python SDK. Use full consensus rather than leader-only mode.

Call `seal("citation-notebook", payload)` with the JSON content of `examples/affiliation-dossier.json` as the payload string. Then call `assess("citation-notebook", "10.1371/journal.pone.0269047", 0)`. Actual classification depends on source retrieval and validator consensus; the example is an input, not a claimed on-chain result.

Read `get_dossier(owner, id)`, `get_revision(owner, id)` and `get_review(owner, id, revision)` to inspect persisted state. Full method descriptions, decision rules and limits are in [PROTOCOL.md](PROTOCOL.md).

## Deployment record

`deployment.json` pins the source SHA256, network, deployed address and finalized example. [Network evidence](evidence/studionet-v3.json) records all three transactions; [revision 1](evidence/review-1.json) contains the persisted dossier, publisher snapshot and assessment.

## Scope

PLOS ONE textual publisher corrections, retractions and expressions of concern. The primitive assesses declared citation uses against one notice; it does not establish a paper’s truth or search for all later notices. User-supplied claims are not authenticated as original experimental findings.

MIT · zuotu295

## Finalized example

The sealed dossier cites study scope (C1) and reproduces an author affiliation (C2). The official affiliation correction finalized with majority agreement and persisted revision 1: C1 `NO_CHANGE_FROM_NOTICE`, C2 `REWRITE`. This result concerns this notice only; it does not establish the paper’s scientific validity. Earlier source versions and their unsuccessful assessments remain separately pinned in the evidence directory.
