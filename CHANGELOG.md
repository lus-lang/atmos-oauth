# Changelog

## 1.0.0

**Release date:** TBD

Version 1.0.0 is the first release of `oauth`, which runs on Lus 2.0.

- Added the `oauth` package.
  - `oauth.provider`: Creates a provider for one client of an authorization server, from a preset or from its own settings.
  - `oauth.challenge`: Returns the PKCE challenge of a code verifier.
  - `oauth.is_error`: Returns true for an error object that `oauth` raised.
  - `provider:discover`: Loads the discovery document of the issuer.
  - `provider:authorize_url`: Returns the authorization URL and the flow of one sign-in.
  - `provider:exchange`: Exchanges the code of a callback for tokens and validates the ID token.
  - `provider:refresh`: Exchanges a refresh token for new tokens.
  - `provider:verify_id_token`: Validates an ID token and returns its claims.
  - `provider:userinfo`: Fetches the profile of the user from the userinfo endpoint.
