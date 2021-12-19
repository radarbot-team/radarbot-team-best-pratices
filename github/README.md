# Git Style Guide

### Branching
- Please use descriptive names to branches. And use short names.

```
# GOOD EXAMPLE
$ git checkout -b feat/screenshots-system

# BAD
$ git checkout -b metar_fix
```

### Commits

- Use **one commit per logical change**. Do not make several changes in one commit. Don't worry to commit a lot :) just try to keep things organized.

- On commit messages try to describe shortly what you did. Use blank line to separate words.

### Type

- When create a new branch follow this title pattern:

- `feat` - a new feature
- `fix` - a bug fix
- `test` - adding tests or refactoring test. **No production code change.**
- `docs` - documentation
- `style` - formatting, missing-semi-colons, etc; **No code change**
- `refactor` - refactoring production code


### Merging
- __NEVER MERGE DIRECT TO MASTER BRANCH__
- All merges should be first to __develop__ branch.
- Use descriptive titles, and also write a good description with details of changes.