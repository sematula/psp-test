# PSP Test Fixture Expectations

This fixture represents exactly one thing: what a real-world, PSP-compliant repository would
contain, and nothing else. It exists to exercise `axonn-engine`'s import of PSP knowledge-domain
state (and, later, the Ops it describes); it is not a source of design truth, and its shape will
change rapidly during pre-alpha. Its shape follows `psp-spec/docs/repository-shape.md` and its
addressing follows `psp-spec/docs/model-addressing.md` — when those pages change, this fixture is
expected to be rewritten to match, not preserved.

This fixture is updated only explicitly. `axonn-engine` and `axonn-gui` code, tests, and generators
must never write into this repository as a side effect of running — anything that isn't a
plausible PSP-compliant repository content (engine-specific test scaffolding, generated malformed
inputs, throwaway artifacts) belongs in the consuming repository's own test fixtures, not here.

## Required payload entries

- `domain.xml`
- `schema/psp-targeting.xsd`
- `schema/psp-meta.xsd`
- `state/models/knowledge/article.xml`
- `state/models/meta/article-meta.xml`
- `state/domain/targets.xml` — engine-maintained, empty until something is imported/evaluated
- `state/domain/sets.xml` — engine-maintained, empty until a Set is declared and evaluated
- `ops/` — placeholder; no Op contracts are defined in this fixture yet
- `artifacts/transforms/`, `artifacts/templates/` — placeholders; no content yet
- `artifacts/ephemeral/` — never committed (see `.gitignore`); engines write disposable output here

## Declared address blocks

`domain.xml` declares a single, unencapsulated block: `<psp:block group="0" />`. This fixture will
never need more than one block's ~4 billion (2^32) model-address capacity, so superblock tiering is
intentionally not exercised here; it's deferred until a fixture actually needs it.

## Expected model identities and pairing

| Base source path | Meta source path | Source model ID | Root target | Child targets |
| --- | --- | --- | --- | --- |
| `state/models/knowledge/article.xml` | `state/models/meta/article-meta.xml` | `0000:015a` | `0` | `1`, `2` |

Each row forms one completed model pair through its shared source model ID. Base objects are
PSP-targeting-conformant; meta objects are PSP-meta-conformant and are not independently targetable
in the provisional import contract. The `state/models/meta` directory remains human-readable
organization only.

The XHTML profile is declared by `domain.xml` as an early profile placeholder. Its schema and
behavior are outside this fixture.

Initial-load expectations:

- XML model bytes are preserved.
- The source host locators are `0000:015a:0`, `0000:015a:1`, and `0000:015a:2`.
- Axonn derives the globally unique runtime model ID by applying the imported address-block/network
  allocation; this fixture does not prescribe the resulting runtime prefix.
- A rendered runtime locator uses bracketed MID/TID form, such as `[fd10::15a]:0` for the base root.
- Human-readable aliases are secondary and are not specified by this fixture.

## Stored-ID contract

Source model IDs are canonical stored IDs per `psp-spec/docs/model-addressing.md`: an optional
lowercase-hex block/superblock tier prefix (absent here, since only one block is declared), then
the 32-bit segment written uncompressed as two zero-padded, four-hex-digit hextets (`0000:015a`,
never a shortened `0:15a` or `15a`). Import rejects uppercase hexadecimal, `0x` prefixes, whitespace,
and any segment hextet that isn't exactly four digits, instead of normalizing them. The leading
tier prefix (when present) selects the declared block/superblock that owns the model; `group`
values are scoped to their immediate parent, not repository-wide unique.

Malformed-input and collision rejection is exercised by `axonn-engine`'s own test suite, which
mutates a copy of this fixture in memory rather than reading pre-built malformed files from this
repository (see `axonn-engine/tests/validate_payload_revision.rs`) — this repository holds only
valid, importable repository content.

The legacy `psp-core` validator fixture retains its own historical `fd00::/8` model-ID contract and
its own flat `domain.xml` + `schema/` + `models/` shape. This fixture does not change that
validator's behavior; the two fixtures are intentionally independent.

## Known gap: axonn-engine has not yet caught up to this fixture

As of this revision, `axonn-engine`'s importer (`src/psp.rs`) still requires a top-level `models/`
tree and parses `<psp:block id="N">` under a `<psp:addressing>` wrapper — the shape and syntax this
fixture used *before* this update. Importing this fixture's current revision will fail against the
current engine until:

- `axonn-engine`#24 reconciles `src/addressing.rs` with `psp-spec/docs/model-addressing.md`'s
  block/superblock/stored-ID rules, and
- a follow-up engine ticket migrates `src/psp.rs`'s tree lookups from `models` to `state/models`
  (and adds handling for `state/domain/`, `ops/`, and `artifacts/`) per
  `psp-spec/docs/repository-shape.md`.

This is expected and intentional: the fixture is updated first so both tickets have something
concrete to implement against.

