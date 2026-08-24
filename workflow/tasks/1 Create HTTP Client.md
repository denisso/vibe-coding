## Task: Create HTTP Client with Authorization Interceptor

---

### Overview

#### Solution Summary
Create a universal HTTP client based on native `fetch` that will be used throughout the application. The client must automatically inject the authentication token from storage (`localStorage` or `sessionStorage`) into the `Authorization` header for every request if the token exists.

**What this task covers:**
- Reading token from storage (both `localStorage` and `sessionStorage`).
- Wrapping `fetch` with base URL prepending.
- Adding `Authorization: Bearer <token>` header automatically.

**What this task does NOT cover:**
- Specific API endpoints (auth, products, etc.) – those will be implemented in later tasks.
- Response parsing or error handling (beyond basic propagation).
- Any contract files – they are not needed for this client.

#### Success Metrics
- A function `request(url, options?)` is exported from `src/shared/api/base.ts`.
- It sends requests to `https://dummyjson.com` when given a relative path.
- If a token is present in storage, all requests include the `Authorization` header.
- If no token, the header is absent.
- Code is typed with TypeScript.

#### Component Overview
- `storage.ts` – utility to read/write token from `localStorage`/`sessionStorage`.
- `base.ts` – wrapper over `fetch` that uses `storage.ts` to get token and adds it to headers.

---

### Requirements

#### R1: Storage utility
**Description:** Create `src/shared/lib/storage.ts` exporting:
- `getToken(): string | null` – checks `localStorage` first, then `sessionStorage` for key `'auth_token'`.
- `setToken(token: string, remember: boolean): void` – writes to `localStorage` if `remember` is `true`, else to `sessionStorage`. (Stub for now, will be used later.)
- `clearToken(): void` – removes from both storages.

#### R2: Fetch wrapper
**Description:** Create `src/shared/api/base.ts` exporting `request(url: string, options?: RequestInit): Promise<Response>`.
- Normalize URL: if not starting with `http://` or `https://`, prepend `BASE_URL = 'https://dummyjson.com'`.
- Retrieve token via `getToken()`.
- Create headers: copy existing `options.headers` (if any) or start empty.
- If token exists, set `Authorization: Bearer ${token}` (override any user-provided `Authorization`).
- Call `fetch` with the constructed URL and options, return the promise.

---

### Acceptance Criteria

- **AC1:** File `src/shared/lib/storage.ts` exists and exports `getToken`, `setToken`, `clearToken`.
- **AC2:** File `src/shared/api/base.ts` exists and exports `request`.
- **AC3:** Calling `request('/products')` sends a GET request to `https://dummyjson.com/products`.
- **AC4:** If `localStorage.setItem('auth_token', 'test123')` was called, the request includes header `Authorization: Bearer test123`.
- **AC5:** If no token in either storage, the `Authorization` header is not present.
- **AC6:** Calling `request('https://other.com/api')` does not prepend the base URL.
- **AC7:** The `options` parameter accepts all standard `RequestInit` properties (e.g., `method`, `body`, `headers`).

---

### Implementation Steps

**Step 1: Create storage utility**
- **Files:** `src/shared/lib/storage.ts`
- **Action:** Create
- **Details:** Implement three functions as described in R1.

**Step 2: Create HTTP client**
- **Files:** `src/shared/api/base.ts`
- **Action:** Create
- **Details:**
  - Import `getToken` from `../lib/storage`.
  - Define `BASE_URL = 'https://dummyjson.com'`.
  - Implement `request`:
    - Build full URL.
    - Get token.
    - Build headers (copy existing, add Authorization if token).
    - Call `fetch`.

**Step 3: Export (optional)**
- **Files:** `src/shared/api/index.ts` (optional)
- **Action:** Create if you want a single import point: `export { request } from './base'`.

---

### Testing Strategy

#### Manual Testing (mandatory)
1. Open browser console.
2. Set a test token: `localStorage.setItem('auth_token', 'test-token')`.
3. Execute: `import('./src/shared/api/base').then(m => m.request('/products'))`.
4. Check Network tab – request to `https://dummyjson.com/products` with `Authorization: Bearer test-token` header.
5. Remove token: `localStorage.removeItem('auth_token')`.
6. Repeat request – verify no `Authorization` header.

---

### Notes

#### References
- MDN `fetch`: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API
- MDN `Headers`: https://developer.mozilla.org/en-US/docs/Web/API/Headers
- DummyJSON base URL: https://dummyjson.com

#### Additional Context
- Token storage key is fixed as `'auth_token'`.
- Future tasks will use this `request` function to call specific endpoints (login, products, etc.) and will provide their own contract definitions. This task is purely about building the client infrastructure.
