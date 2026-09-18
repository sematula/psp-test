# PSP Core Fixture Expectations

This fixture represents one source repository at a pinned Git revision.

## Required payload entries

- `domain.xml`
- `schema/psp-targeting.xsd`
- `schema/psp-meta.xsd`
- `models/knowledge/article.xml`
- `models/meta/article-meta.xml`

## Expected model identities

| Source path | Model ID | Root target | Child targets |
| --- | --- | --- | --- |
| `models/knowledge/article.xml` | `fd00::12` | `0` | `1`, `2` |
| `models/meta/article-meta.xml` | `fd00::13` | `0` | `1` |

The `models/meta` directory is human-readable organization only. The metadata document is an ordinary model for this fixture; no special meta-model loading behavior is required.

The XHTML profile is declared by `domain.xml` as an early profile placeholder. Its schema and behavior are outside this PSP Core fixture.

Initial-load expectations:

- XML model bytes are preserved.
- The primary locators are `fd00::12:0`, `fd00::12:1`, `fd00::12:2`, `fd00::13:0`, and `fd00::13:1`.
- Human-readable aliases are secondary and are not specified by this fixture.
