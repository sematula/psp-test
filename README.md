# PSP Test

An executable PSP payload used by `axonn-engine`'s and `axonn-gui`'s integration tests. See
`EXPECTED.md` for what this fixture currently contains and asserts.

## What this repository is for

This repository represents exactly one thing: what a real-world, PSP-compliant repository would
contain, and nothing else. It is not a source of design truth — `psp-spec` is — and its shape will
change rapidly during pre-alpha as `psp-spec` itself changes.

This repository is updated only explicitly. `axonn-engine` and `axonn-gui` code, tests, and any
future generation tooling must never write into this repository as a side effect of running.
Anything that isn't plausible PSP-compliant repository content — engine-specific test scaffolding,
generated malformed inputs, throwaway artifacts — belongs in the consuming repository's own test
fixtures instead.
