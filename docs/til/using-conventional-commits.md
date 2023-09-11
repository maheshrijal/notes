---
title: Using Convential commits
---

[Conventional Commit](https://www.conventionalcommits.org/) is a formatting convention that provides a set of rules to formulate a consistent commit message structure.

Structure:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

The commit type can include the following:

- `feat` – a new feature is introduced with the changes
- `fix` – a bug fix has occurred
- `chore` – changes that do not relate to a fix or feature and don't modify src or test files (for example updating dependencies)
- `refactor` – refactored code that neither fixes a bug nor adds a feature
- `docs` – updates to documentation such as a the README or other markdown files
- `style` – changes that do not affect the meaning of the code, likely related to code formatting such as white-space, missing semi-colons, and so on.
- `test` – including new or correcting previous tests
- `perf` – performance improvements
- `ci` – continuous integration related
- `build` – changes that affect the build system or external dependencies
- `revert` – reverts a previous commit