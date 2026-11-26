---
"@contextualio/astro-core": patch
---

fix(sign-in): update `@contextual-io/cds-core` to 0.1.4, so a returning visitor whose session JWT expired is signed straight back in instead of looping between `/sign-in` and the page
