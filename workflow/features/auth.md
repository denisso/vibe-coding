
### Scenario: Storage utility exports required functions

- **GIVEN** the project is set up with TypeScript
- **WHEN** the developer creates `src/shared/lib/storage.ts`
- **THEN** the file SHALL export a function `getToken(): string | null`
- **AND** the file SHALL export a function `setToken(token: string, remember: boolean): void`
- **AND** the file SHALL export a function `clearToken(): void`

---

### Scenario: getToken reads from localStorage first

- **GIVEN** a token exists in `localStorage` with key `'auth_token'`
- **AND** no token exists in `sessionStorage` with the same key
- **WHEN** the `getToken()` function is called
- **THEN** it SHALL return the token from `localStorage`

---

### Scenario: getToken falls back to sessionStorage

- **GIVEN** no token exists in `localStorage` with key `'auth_token'`
- **AND** a token exists in `sessionStorage` with key `'auth_token'`
- **WHEN** the `getToken()` function is called
- **THEN** it SHALL return the token from `sessionStorage`

---

### Scenario: getToken returns null when no token exists

- **GIVEN** no token exists in either `localStorage` or `sessionStorage`
- **WHEN** the `getToken()` function is called
- **THEN** it SHALL return `null`

---

### Scenario: setToken writes to localStorage when remember is true

- **GIVEN** `setToken(token, true)` is called with a valid token
- **WHEN** the function executes
- **THEN** the token SHALL be written to `localStorage` with key `'auth_token'`
- **AND** the token SHALL NOT be written to `sessionStorage`

---

### Scenario: setToken writes to sessionStorage when remember is false

- **GIVEN** `setToken(token, false)` is called with a valid token
- **WHEN** the function executes
- **THEN** the token SHALL be written to `sessionStorage` with key `'auth_token'`
- **AND** the token SHALL NOT be written to `localStorage`

---

### Scenario: clearToken removes token from both storages

- **GIVEN** a token exists in both `localStorage` and `sessionStorage`
- **WHEN** the `clearToken()` function is called
- **THEN** the token SHALL be removed from `localStorage`
- **AND** the token SHALL be removed from `sessionStorage`

---

### Scenario: request exports a fetch wrapper function

- **GIVEN** the project is set up with TypeScript
- **WHEN** the developer creates `src/shared/api/base.ts`
- **THEN** the file SHALL export a function `request(url: string, options?: RequestInit): Promise<Response>`

---

### Scenario: request prepends base URL for relative paths

- **GIVEN** the function `request('/products')` is called
- **WHEN** the request is sent
- **THEN** the request SHALL be sent to `https://dummyjson.com/products`
- **AND** the base URL `https://dummyjson.com` SHALL be prepended

---

### Scenario: request does not prepend base URL for absolute URLs

- **GIVEN** the function `request('https://other.com/api')` is called
- **WHEN** the request is sent
- **THEN** the request SHALL be sent to `https://other.com/api` exactly as provided
- **AND** the base URL SHALL NOT be prepended

---

### Scenario: request includes Authorization header when token exists

- **GIVEN** a token exists in storage with value `'test123'`
- **WHEN** the `request()` function is called with any path
- **THEN** the request SHALL include the header `Authorization: Bearer test123`

---

### Scenario: request does not include Authorization header when no token exists

- **GIVEN** no token exists in either `localStorage` or `sessionStorage`
- **WHEN** the `request()` function is called with any path
- **THEN** the request SHALL NOT include the `Authorization` header

---

### Scenario: request preserves user-provided headers

- **GIVEN** the `request()` function is called with custom headers `{ 'X-Custom': 'value' }`
- **WHEN** the request is sent
- **THEN** the request SHALL include the custom header `X-Custom: value`
- **AND** the `Authorization` header SHALL be added if a token exists

---

### Scenario: request overrides user-provided Authorization header

- **GIVEN** a token exists in storage with value `'test123'`
- **AND** the user provides an `Authorization` header with value `'Bearer fake'`
- **WHEN** the `request()` function is called
- **THEN** the `Authorization` header SHALL be set to `Bearer test123`
- **AND** the user-provided `Authorization` header SHALL be overridden

---

### Scenario: request accepts all standard RequestInit options

- **GIVEN** the `request()` function is called with `{ method: 'POST', body: JSON.stringify({}) }`
- **WHEN** the request is sent
- **THEN** the request SHALL use the specified `method` and `body`
- **AND** the function SHALL accept all standard `RequestInit` properties (`method`, `body`, `headers`, `credentials`, etc.)

---

### Scenario: request returns a Promise<Response>

- **GIVEN** the `request()` function is called with any valid URL
- **WHEN** the request completes
- **THEN** the function SHALL return a `Promise` that resolves to a `Response` object

