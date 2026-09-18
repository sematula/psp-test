# PSP Core Fixture Expectations

This fixture represents one source repository at a pinned Git revision.

## Required payload entries

- `domain.xml`
- `schema/psp-targeting.xsd`
- `schema/psp-meta.xsd`
- `models/knowledge/article.xml`
- `models/meta/article-meta.xml`

## Expected model identities and pairing

| Base source path | Meta source path | Source model ID | Root target | Child targets |
| --- | --- | --- | --- | --- |
| `models/knowledge/article.xml` | `models/meta/article-meta.xml` | `0:15a` | `0` | `1`, `2` |

The base and meta documents form one completed model pair through their shared source model ID.
The base object is PSP-targeting-conformant; the meta object is PSP-meta-conformant and is not
independently targetable in the provisional import contract. The `models/meta` directory remains
human-readable organization only.

This fixture uses one block. Whether a rendered `block` element stands alone or is nested under a
`superblock` element when multiple blocks are declared remains intentionally deferred.

The XHTML profile is declared by `domain.xml` as an early profile placeholder. Its schema and behavior are outside this PSP Core fixture.

Initial-load expectations:

- XML model bytes are preserved.
- The source host locators are `0:15a:0`, `0:15a:1`, and `0:15a:2`.
- Axonn derives the globally unique runtime model ID by applying the imported address-block/network
	allocation; this fixture does not prescribe the resulting runtime prefix.
- A rendered runtime locator uses bracketed MID/TID form, such as `[fd00::12]:0` for the base root.
- Human-readable aliases are secondary and are not specified by this fixture.

## Provisional host-ID contract

Source model IDs are canonical, uncompressed host values made from one or more non-empty lowercase
hexadecimal components separated by colons. Import rejects uppercase hexadecimal, `0x` prefixes,
empty components, whitespace, and leading-zero aliases instead of normalizing them. The first
component selects the declared numeric address block; block IDs must be unique within a payload.

The following conformance inputs are intentionally small and are consumed by the importer tests:

| Input | Expected result |
| --- | --- |
| `conformance/malformed-uppercase.xml` | reject: uppercase host component |
| `conformance/malformed-prefix.xml` | reject: `0x` host component |
| `conformance/malformed-empty-component.xml` | reject: empty host component |
| `conformance/collision/first.xml` and `conformance/collision/second.xml` | reject: two source identities resolve to one runtime MID |

The legacy `psp-core` validator fixture retains its historical `fd00::/8` model-ID contract. This
forward-looking fixture does not change that validator behavior.
