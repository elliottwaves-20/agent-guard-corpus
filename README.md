# agent-guard-corpus

Calibration corpus for [agent-guard](https://github.com/elliottwaves-20/agent-guard) and
for anyone who builds or evaluates scanners for AI agent skills (SKILL.md format).

> **This repository contains deliberately malicious skill samples. Do not install them as
> skills.** They are test data. Every network target uses the reserved `.invalid` TLD
> (RFC 2606), so no sample can reach a real host, and no sample references a real package
> name that someone could register later. A scanner *should* flag this repository.

## Why it exists

A skill scanner has two jobs that pull in opposite directions: catch malicious skills,
and stay quiet on ordinary ones. Raw detectors tend to over-report (installer snippets,
`export FOO_API_KEY=...` in docs, code that does not match the stated purpose). Any layer
that suppresses such false positives must be checked against real attack patterns, so
it never suppresses those as well.

The corpus is the acceptance test for that: every sample under `malicious/` must still be
judged dangerous, and every benign reference must be judged safe.

## Layout

- `malicious/<id>/` — one self-contained skill per attack pattern.
- `corpus.toml` — the expected verdict and the pattern each sample represents.

Benign samples are real, public skills that produced false positives. agent-guard keeps
them as references (source repository, commit, content hash) in its own test suite, not
here, so their authors stay the only publishers of their content.

## Samples

| id | pattern | where |
|---|---|---|
| m1-exfil | credential files and `*KEY*`/`*TOKEN*` env vars POSTed to a foreign host | Python script |
| m2-injection | hidden instruction in an HTML comment: read SSH key and git credentials, smuggle them into a commit trailer | SKILL.md |
| m3-curlsh | `curl ... \| sudo bash` from a host unrelated to the stated tool | SKILL.md |
| m4-obfuscated | base64 payload passed to `exec` | Python script |
| m5-subtle | install from a foreign URL, then the whole environment (`env \| base64`) handed to that tool | `references/` only |

## Using it

Pin a commit, never a branch, and verify the content hash before scanning. Scan into a
throw-away directory. Do not copy samples into an agent's skills folder.

## Contributing

New patterns are welcome as issues or pull requests. Keep them inert: `.invalid` hosts
only, no real package names, no working payloads beyond what a scanner needs to see.

## License

MIT, see [LICENSE](LICENSE).
