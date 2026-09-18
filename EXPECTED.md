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
The meta document is attached metadata and is not independently targetable in the provisional import
contract. The `models/meta` directory remains human-readable organization only.

This fixture uses one block. Whether a rendered `block` element stands alone or is nested under a
`superblock` element when multiple blocks are declared remains intentionally deferred.

The XHTML profile is declared by `domain.xml` as an early profile placeholder. Its schema and behavior are outside this PSP Core fixture.

Initial-load expectations:

- XML model bytes are preserved.
- The source host locators are `0:15a:0`, `0:15a:1`, and `0:15a:2`.
- Axonn derives the globally unique runtime model ID by applying the imported address-block/network
	allocation; this fixture does not prescribe the resulting runtime prefix.
- Human-readable aliases are secondary and are not specified by this fixture.
