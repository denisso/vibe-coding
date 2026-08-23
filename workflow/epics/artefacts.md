## Epic 1: Authentication and Session Management

### Feature 1.1: User Authentication (UI and logic)

#### Task 1.1.1 – Create the Login page according to the Figma design
- Create the page component `pages/login/ui/LoginPage.tsx`.
- Build the form with "Username" and "Password" fields, a Submit button, a "Remember me" checkbox, and a placeholder "Create" link.
- Use Tailwind for styling (match the layout).
- Set up routing: path `/login`, accessible without authentication.

**Subtask:**
- Create the `pages/login` folder following FSD (pages layer).
- Add basic markup (container, card, fields).
- Implement reusable input and button components from `shared/ui`.
- Add the checkbox and "Create" link (no action).

**Dependencies:** none.

---

#### Task 1.1.2 – Implement client‑side field validation
- Validate that both fields (username and password) are required.
- Display error messages below the respective fields when validation fails.
- Use React Hook Form + Zod (or manual validation with `useState` – team choice).

**Subtask:**
- Set up form validation (react-hook-form + Zod schema).
- Add error states and display them in the UI.
- Handle form submission: trigger validation and then the API call.

**Dependencies:** Task 1.1.1 (the form markup is ready).

---

#### Task 1.1.3 – Create the API client for DummyJSON Auth and implement login
- Create the service `shared/api/auth.ts` with a `login(username, password)` function.
- Use `fetch` or `axios` (already installed).
- Handle successful response (receiving a token) and errors (status 400).
- On error, propagate the message to the component for display.

**Subtask:**
- Write types for the response (TokenResponse, ErrorResponse).
- Handle network errors (e.g., `try/catch`).
- Integrate the API call into the form submission handler.

**Dependencies:** Task 1.1.2 (the form is ready to submit).

---

#### Task 1.1.4 – Manage session state (Zustand store)
- Create `features/auth/model/auth.store.ts` with state: `user`, `token`, `isAuthenticated`.
- Add actions: `login(token, user)`, `logout()`, `restoreSession()`.
- Use `persist` middleware (or manual storage) – but storage logic will be in Task 1.1.5.

**Subtask:**
- Define types for the state.
- Implement `login` and `logout` methods.
- Subscribe components to state changes (e.g., for redirects).

**Dependencies:** none (can be done in parallel with UI).

---

#### Task 1.1.5 – Implement session persistence (localStorage / sessionStorage) based on the checkbox
- On successful login:
  - if the "Remember me" checkbox is checked – store the token in `localStorage`;
  - otherwise – store it in `sessionStorage`.
- On app initialisation, check both storages and restore the session (call `restoreSession`).
- On logout, clear both storages and reset the state.

**Subtask:**
- Write utilities for reading/writing to `localStorage` / `sessionStorage`.
- Integrate the check on app load (in `App` or `main.tsx`).
- Connect the checkbox state to determine which storage to use on login.

**Dependencies:** Task 1.1.4 (store ready), Task 1.1.1 (checkbox exists).

---

#### Task 1.1.6 – Redirect after login and protect routes
- After successful login, redirect to `/products` (the main page).
- Create a `PrivateRoute` component (or use `Navigate` from react-router) to check `isAuthenticated`.
- Protect the product list page and other private routes.

**Subtask:**
- In `LoginPage`, call `useNavigate` after successful login.
- Create an HOC or wrapper component `RequireAuth`.
- Wrap private pages in the router.

**Dependencies:** Task 1.1.4 (store with auth flag), Task 1.1.5 (session restoration).

---

#### Task 1.1.7 – Add API error handling and notification display
- On login error (invalid credentials), show the error message below the field or via a Toast notification.
- Create a reusable `Toast` component in `shared/ui` for displaying error messages (and later for success actions).

**Subtask:**
- Write a Toast component with enter/exit animations.
- Connect API errors to the error state in the form component.
- Use Zustand for global toast management (or local state).

**Dependencies:** Task 1.1.3 (API returns errors), Task 1.1.2 (form can display errors).

---

#### Task 1.1.8 – "Create" link as a placeholder
- Ensure the "Create" link does not navigate anywhere (e.g., `href="#"` or an empty `onClick`).
- Style it according to the Figma design.

**Subtask:**
- Add the link component with appropriate Tailwind classes.
- Block navigation (`event.preventDefault` or empty href).

**Dependencies:** Task 1.1.1 (form markup ready).

---

### Feature 1.2 – Session restoration and logout (additional tasks, separated)

#### Task 1.2.1 – Implement automatic session restoration on app load
- In the entry point (e.g., `App.tsx`), call `restoreSession` from the store.
- If a token exists in `localStorage` or `sessionStorage` – set `isAuthenticated = true` and optionally load user data.
- If the token is invalid – clear storage and reset state.

**Subtask:**
- Extend `auth.store` with the `restoreSession` action.
- Call this action when the root component mounts.

**Dependencies:** Task 1.1.4, Task 1.1.5.

---

#### Task 1.2.2 – Implement logout
- Create a logout button in the header (or wherever the design specifies) – can be deferred to the second epic, but included here for completeness.
- On click, call `logout` from the store, clear storages, and redirect to `/login`.

**Subtask:**
- Add a logout button on the main page (temporary placement).
- Perform redirect after clearing.

**Dependencies:** Task 1.1.4.

---

#### Task 1.2.3 – Create a shared token utility (shared/lib/token.ts)
- Extract logic for reading/writing/removing tokens from different storages into a separate module.
- Encapsulate the choice of storage depending on the `remember` flag.

**Subtask:**
- Write functions `setToken`, `getToken`, `removeToken`.
- Integrate them into the store and the login form.

**Dependencies:** Task 1.1.5 (used for persistence).

---

### Execution order (dependencies between tasks):
- 1.1.1 → 1.1.2 → 1.1.3 (sequential chain: UI → validation → API).
- 1.1.4 and 1.1.5 can be parallel with UI, but 1.1.5 requires the checkbox (1.1.1).
- 1.1.6 requires 1.1.4 and 1.1.5.
- 1.1.7 requires 1.1.3 and 1.1.2.
- 1.2.1 requires 1.1.4 and 1.1.5.
- 1.2.2 requires 1.1.4.

All tasks should be implemented within the first epic. Estimated complexity – about 2–3 sprints (depending on the number of developers).
