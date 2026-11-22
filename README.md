# oauth

`oauth` signs users in with the authorization code flow of OAuth 2.0 and OpenID Connect. It builds the authorization URL with a random state, a PKCE challenge, and a nonce, then exchanges the code of the callback for tokens and validates the ID token against the keys of the provider. It also refreshes tokens and reads the profile of the user. Presets cover GitHub, Google, Microsoft, Apple, GitLab, and Discord.

The following example signs a user in with Google. The first handler sends the browser to Google and keeps the flow in the session of the user. The second handler receives the callback and reads the identity of the user from the validated ID token.

```lus nocheck
local oauth = require("https://atmosp.here/2.0/oauth.lus")

local google = oauth.provider("google", {
    client_id = os.getenv("GOOGLE_CLIENT_ID"),
    client_secret = os.getenv("GOOGLE_CLIENT_SECRET"),
    redirect = "https://app.example/callback",
})

app:get("/login", function(req, res)
    local url, flow = google:authorize_url()
    req.session.flow = flow
    return res:redirect(url)
end)

app:get("/callback", function(req, res)
    local ok, tokens = catch google:exchange(req.query, req.session.flow)
    req.session.flow = nil
    if not ok then
        return 400, tostring(tokens)
    end
    req.session.user = { id = tokens.claims.sub, email = tokens.claims.email }
    return res:redirect("/")
end)
```

A provider with no preset takes an `issuer` and reads its endpoints from the OpenID Connect discovery document of that issuer, as in the following example.

```lus nocheck
local idp = oauth.provider({
    issuer = "https://login.example.com",
    client_id = id, client_secret = secret, redirect = "https://app.example/callback",
    scope = { "openid", "email" },
})
```

The package requires Lus 2.0 with the RSA and ECDSA keys of `crypto` and declares the permission `network:http`, which it uses to send the discovery, key, token, and userinfo requests. It never calls `pledge`, so the program that loads it keeps control of its permissions.

The reference is in the doc comments of `oauth.lus`, which `lus doc oauth.lus` prints.
