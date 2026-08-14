---
description: Run markdownlint and the link check on the vault, then fix reported issues.
agent: build
---

# Lint the vault

Run the same checks as CI and fix every reported issue.

## Steps

1. Run `npx --yes markdownlint-cli2 '**/*.md'` and fix all lint errors.
2. Check links on tracked files only:

   ```sh
   git ls-files -z '*.md' | while IFS= read -r -d '' f; do
     npx --yes markdown-link-check -c .github/markdown-link-check.json "$f"
   done
   ```

3. Fix any broken links (the config ignores `../` links to sibling repos and
   in-file `#` anchors; anything else must resolve).
4. Report which issues you found and fixed.
