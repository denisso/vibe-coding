# HTTP Client with Authorization Interceptor

### Requirement: Token storage utility

The system SHALL provide utilities to manage an authentication token in web storage. It SHALL read the token from `localStorage` first, then `sessionStorage`, using the fixed key `'auth_token'`. It SHALL allow writing the token to `localStorage` when a `remember` flag is `true`, otherwise to `sessionStorage`. It SHALL allow clearing the token from both storages.

#### Scenario: Reading token when present in localStorage returns it

- **WHEN** `getToken()` is called and a token exists in `localStorage` under `'auth_token'`
- **THEN** `getToken()` SHALL return the token value from `localStorage`

#### Scenario: Reading token when only in sessionStorage returns it

- **WHEN** `getToken()` is called and no token exists in `localStorage` but a token exists in `sessionStorage` under `'auth_token'`
- **THEN** `getToken()` SHALL return the token value from `sessionStorage`

#### Scenario: Setting token with `remember: true` stores in localStorage

- **WHEN** `setToken(token, true)` is called
- **THEN** the token SHALL be written to `localStorage` with key `'auth_token'`
- **AND** the token SHALL NOT be written to `sessionStorage`

#### Scenario: Setting token with `remember: false` stores in sessionStorage

- **WHEN** `setToken(token, false)` is called
- **THEN** the token SHALL be written to `sessionStorage` with key `'auth_token'`
- **AND** the token SHALL NOT be written to `localStorage`

#### Scenario: Clearing token removes from both storages

- **WHEN** `clearToken()` is called
- **THEN** the token SHALL be removed from `localStorage` (if present)
- **AND** the token SHALL be removed from `sessionStorage` (if present)

---

### Requirement: HTTP client with automatic authorization header injection

The system SHALL provide a `request` function that wraps `fetch` and automatically injects an `Authorization` header with a `Bearer` token if a token is available from the storage utility. It SHALL prepend the base URL `'https://dummyjson.com'` to relative paths. It SHALL preserve any custom headers provided in the `options` argument, but the `Authorization` header SHALL be overridden by the token when present.

#### Scenario: Request with relative path and token in storage adds Authorization header

- **WHEN** `request('/products')` is called and a token exists in storage
- **THEN** the request SHALL be sent to `https://dummyjson.com/products`
- **AND** the request SHALL include an `Authorization` header with value `Bearer <token>`

#### Scenario: Request with relative path and no token does not add Authorization

- **WHEN** `request('/products')` is called and no token exists in storage
- **THEN** the request SHALL be sent to `https://dummyjson.com/products`
- **AND** the request SHALL NOT include an `Authorization` header

#### Scenario: Request with absolute URL does not prepend base URL

- **WHEN** `request('https://other.com/api')` is called
- **THEN** the request SHALL be sent to `https://other.com/api` without prepending the base URL

#### Scenario: Request with custom headers preserves them and overrides Authorization with token

- **WHEN** `request('/products', { headers: { 'X-Custom': 'value', 'Authorization': 'Basic foo' } })` is called and a token exists in storage
- **THEN** the request SHALL include the custom header `X-Custom: value`
- **AND** the request SHALL include an `Authorization` header with value `Bearer <token>` (overriding the custom one)
