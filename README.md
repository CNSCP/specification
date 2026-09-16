# CNS/CP — Connectivity Naming System using Connection Profiles

CNS/CP is a specification for the connection layer: the layer between
identity, which establishes who a party is, and the application
protocols that carry what connected systems say to each other. It
settles whether a connection between two systems is permitted, on what
terms, and records the result.

Two systems that have never been integrated each declare, to a governed
region called a Realm, what they can do — a named, versioned Connection
Profile, a role within it, and the entity they act at. Where two
declarations fit, the Realm's Governor forms a Connection between them.
Neither system needs to know the other exists.

## This repository

| | |
|---|---|
| [`cnscp-2026-specification.md`](cnscp-2026-specification.md) | The 2026 revision. Renders here on GitHub. |
| [`cnscp-2026-specification.pdf`](cnscp-2026-specification.pdf) | The same document, typeset, with line numbers for citation. |
| [`2022/`](2022/) | The December 2022 specification, unchanged, kept as history. |
| [`license.md`](license.md) | How CNS/CP is licensed — a pointer to the one authoritative statement at cnscp.io/license. |
| [`FEEDBACK-LICENSE.md`](FEEDBACK-LICENSE.md) | Terms for feedback submitted through designated channels. |

## Status

**2026 revision, working draft.** No text herein is final. Appendix C
lists what the editors know to be unsettled; everything else in the
document is settled as far as this draft goes.

The 2026 revision supersedes the December 2022 specification. It keeps
the 2022 design and restates it on a canonical vocabulary, with a
six-step process, a defined conformance boundary, and three additions:
Channels, the Realm, and composition across Realms. Appendix B maps the
2022 vocabulary to this one, so a reader of the older text can find
their way.

## The Connection Profile Registry

Connection Profiles are registered and published at
[cp.cnscp.io](https://cp.cnscp.io). Reading is open to anyone: a
Profile's canonical URL returns its content as JSON.

```
curl https://cp.cnscp.io/padi.lighting
```

## Editors

Toby Considine and Anto Budiardjo.

## Feedback

Comment is welcome and should cite the section number. Feedback
submitted through the channels designated at [cnscp.io](https://cnscp.io)
is governed by [`FEEDBACK-LICENSE.md`](FEEDBACK-LICENSE.md). During the
pre-standardization phase the text is maintained by the editors, and this
repository does not take pull requests against it.
