# Rust Semver Issue Reproduction

This repository reproduces the issue described in [dependabot/dependabot-core#13401](https://github.com/dependabot/dependabot-core/issues/13401).

## Issue Description

Dependabot incorrectly interprets major version for Rust packages in ignore commands.

According to Rust/Cargo semver conventions:
- For versions `0.y.z`, changes in `y` are considered **major** (breaking) changes
- For versions `0.0.z`, changes in `z` are considered **major** (breaking) changes
- Only the leftmost non-zero component matters for compatibility

## Expected Behavior

When using `@dependabot ignore hashbrown major version` with `hashbrown = "0.15.5"`:
- Should ignore updates to 0.16.x, 0.17.x, etc. (major version bumps in Rust semver)
- Should still allow updates to 0.15.6, 0.15.7, etc. (minor/patch in Rust semver)

## Actual Behavior

Dependabot ignores all 0.x versions, treating the entire `0.x` range as a single major version.

## Reproduction Steps

1. Create a PR that updates `hashbrown` from `0.15.5` to `0.16.0`
2. Comment: `@dependabot ignore hashbrown major version`
3. Observe that Dependabot ignores all 0.x updates instead of just 0.16+

## Notes

- Dependabot correctly groups updates using Rust semver rules (as configured in `dependabot.yml`)
- The issue only affects the ignore command's interpretation of "major version"