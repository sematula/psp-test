# PSP Test Fixture Expectations

This fixture represents one source repository at a pinned Git revision. It exists to exercise
`axonn-engine`'s import of PSP knowledge-domain state (and, later, the Ops it describes); it is not
a source of design truth. Its shape follows `psp-spec/docs/repository-shape.md` and its addressing
follows `psp-spec/docs/model-addressing.md` — when those pages change, this fixture is expected to
be rewritten to match, not preserved.

## Required payload entries

- `domain.xml`
- `schema/psp-targeting.xsd`
- `schema/psp-meta.xsd`
- `state/models/knowledge/article.xml`
- `state/models/meta/article-meta.xml`
- `state/models/knowledge/note.xml`
- `state/models/meta/note-meta.xml`
- `state/domain/targets.xml` — engine-maintained, empty until something is imported/evaluated
- `state/domain/sets.xml` — engine-maintained, empty until a Set is declared and evaluated
- `ops/` — placeholder; no Op contracts are defined in this fixture yet
- `artifacts/transforms/`, `artifacts/templates/` — placeholders; no content yet
- `artifacts/ephemeral/` — never committed (see `.gitignore`); engines write disposable output here

## Declared address blocks

`domain.xml` declares two address-sizing tiers:

- `<psp:block group="0" />` — an unencapsulated sibling block, used by the `article`/`article-meta`
  pair below.
- `<psp:superblock group="f"><psp:block group="1" /></psp:superblock>` — a superblock-tiered block,
  used by the `note`/`note-meta` pair below, to exercise superblock tiering per
  `psp-spec/docs/model-addressing.md` even though this fixture doesn't need more than 16 blocks.

## Expected model identities and pairing

| Base source path | Meta source path | Source model ID | Root target | Child targets |
| --- | --- | --- | --- | --- |
| `state/models/knowledge/article.xml` | `state/models/meta/article-meta.xml` | `0:15a` | `0` | `1`, `2` |
| `state/models/knowledge/note.xml` | `state/models/meta/note-meta.xml` | `f1:200` | `0` | none |

Each row forms one completed model pair through its shared source model ID. Base objects are
PSP-targeting-conformant; meta objects are PSP-meta-conformant and are not independently targetable
in the provisional import contract. The `state/models/meta` directory remains human-readable
organization only.

The XHTML profile is declared by `domain.xml` as an early profile placeholder. Its schema and
behavior are outside this fixture.

Initial-load expectations:

- XML model bytes are preserved.
- The source host locators are `0:15a:0`, `0:15a:1`, `0:15a:2`, and `f1:200:0`.
- Axonn derives the globally unique runtime model ID by applying the imported address-block/network
  allocation; this fixture does not prescribe the resulting runtime prefix.
- A rendered runtime locator uses bracketed MID/TID form, such as `[fd10::15a]:0` for the base root.
- Human-readable aliases are secondary and are not specified by this fixture.

## Recorded-ID contract

Source model IDs are canonical, uncompressed host values made from one or more non-empty lowercase
hexadecimal components separated by colons, per `psp-spec/docs/model-addressing.md`'s Recorded ID
rules. Import rejects uppercase hexadecimal, `0x` prefixes, empty components, whitespace, and
leading-zero aliases instead of normalizing them. The leading component(s) select the declared
block (and, once tiered, superblock) that owns the model; block/superblock `group` values are
scoped to their immediate parent, not repository-wide unique.

The following conformance inputs are intentionally small and are consumed by the importer tests:

| Input | Expected result |
| --- | --- |
| `conformance/malformed-uppercase.xml` | reject: uppercase host component |
| `conformance/malformed-prefix.xml` | reject: `0x` host component |
| `conformance/malformed-empty-component.xml` | reject: empty host component |
| `conformance/collision/first.xml` and `conformance/collision/second.xml` | reject: two source identities resolve to one runtime MID |

The legacy `psp-core` validator fixture retains its own historical `fd00::/8` model-ID contract and
its own flat `domain.xml` + `schema/` + `models/` shape. This fixture does not change that
validator's behavior; the two fixtures are intentionally independent.

## Known gap: axonn-engine has not yet caught up to this fixture

As of this revision, `axonn-engine`'s importer (`src/psp.rs`) still requires a top-level `models/`
tree and parses `<psp:block id="N">` under a `<psp:addressing>` wrapper — the shape and syntax this
fixture used *before* this update. Importing this fixture's current revision will fail against the
current engine until:

- `axonn-engine`#24 reconciles `src/addressing.rs` with `psp-spec/docs/model-addressing.md`'s
  block/superblock/recorded-ID rules, and
- a follow-up engine ticket migrates `src/psp.rs`'s tree lookups from `models` to `state/models`
  (and adds handling for `state/domain/`, `ops/`, and `artifacts/`) per
  `psp-spec/docs/repository-shape.md`.

This is expected and intentional: the fixture is updated first so both tickets have something
concrete to implement against.

