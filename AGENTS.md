# AGENTS.md

`lus-lang/atmos-oauth` is the repository of the `oauth` package of the atmosphere registry. Programs load the package with `require("https://atmosp.here/2.0/oauth.lus")`.

## Scope

`oauth` signs users in with the authorization code flow of OAuth 2.0 and OpenID Connect. It builds the authorization URL with a random state, a PKCE challenge, and a nonce, then exchanges the code of the callback for tokens and validates the ID token against the keys of the provider. It also refreshes tokens and reads the profile of the user. Presets cover GitHub, Google, Microsoft, Apple, GitLab, and Discord. The public API is every function that `oauth.lus` returns in its module table and every method of the provider that `oauth.provider` returns. A change to that API needs a new entry in `CHANGELOG.md` and a version change in the header.

## Layout

- `oauth.lus` is the whole package. Its header doc comment contains the package metadata and the declared permissions.
- `tests/*_test.lus` are the tests. They load `oauth.lus` from the parent of `TEST_DIR`, the directory that holds the test file.

## Dependencies

- `jwt`, which `oauth.lus` loads by its atmosphere URL, for ID tokens and for the client secret of Apple
- the standard library `crypto` module, for random values, hashes, and keys
- the standard library `network.fetch`, for every request

The tests load `jwt` from a checkout of `lus-lang/atmos-jwt` next to this repository, so clone it into the same parent directory first.

## Tests

The tests run against an identity provider that `tests/oauth_test.lus` builds in memory and passes to the package as its `fetch` option. `round_trip_test` serves the same provider with `network.serve` on a local port. It is skipped where the process may not bind one.

## Commands

Run these commands from the root of the repository.

```sh
lus test tests          # run the tests
lus check oauth.lus     # lint
lus doc oauth.lus       # print the reference
```

## Rules

- Every public function has a doc comment that states each result and each error, following `spec/design/style.md` of the Lus repository.
- The package never calls `pledge`. A new permission is declared in the header.
- Prose follows the `lus-writing` skill, which `lus agents init` installs and https://lus.dev/agents/skills/lus-writing/SKILL.md serves. In particular, prose has no dashes or semicolons and never names the Roblox Lua dialect.
- Every bug fix comes with a test that fails without it.
- ID tokens are verified with asymmetric algorithms only. The algorithm `none` and the HMAC algorithms stay refused.
- Error messages and the provider table never contain the client secret, a PKCE verifier, an authorization code, or a token.
- The key of `tests/oauth_test.lus` is a test key that protects nothing. Real credentials stay out of the repository.
