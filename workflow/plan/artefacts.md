## Epic 1: Authentication and Session Management

### Feature 1.1: User Authentication

#### User Story 1.1.1: Login form with field validation
As an unauthenticated user, I want to see a login form with "Username" and "Password" fields so that I can enter my credentials.

**Acceptance Criteria:**
- The form contains input fields for username and password.
- Fields are required (validation for empty values).
- A submit button is present.
- The layout matches the Figma design.

#### User Story 1.1.2: Display errors on failed login attempt
As a user, I want to receive error messages on failed login attempts so that I understand the reason for rejection.

**Acceptance Criteria:**
- On API error (e.g., invalid credentials), a notification or error message is shown below the relevant field.
- Validation errors (empty fields) are displayed below the fields before submission or after an attempt.

#### User Story 1.1.3: Session persistence with "Remember me" checkbox
As a user, I want to keep my session when the "Remember me" checkbox is checked so that I don't have to re-enter credentials after closing the browser.

**Acceptance Criteria:**
- When the checkbox is checked, the token is stored in localStorage.
- When unchecked, the token is stored in sessionStorage.
- After login, the user is redirected to the product list page.

#### User Story 1.1.4: "Create" link as a placeholder
As a user, I want to see a "Create" link on the login form (placeholder) to understand that registration may be available in the future.

**Acceptance Criteria:**
- The "Create" link is displayed but does not navigate anywhere (no logic for redirection).

---

## Epic 2: Product Management (Catalog)

### Feature 2.1: Product List View

#### User Story 2.1.1: Display product table
As an authenticated user, I want to see a product table with columns (Name, Price, Vendor, SKU, Rating) so that I can browse the catalog.

**Acceptance Criteria:**
- Data is loaded from the DummyJSON API (Products).
- Columns match the Figma design.
- A progress bar is shown while loading data.

#### User Story 2.1.2: Loading indicator (progress bar)
As a user, I want to see a loading indicator (progress bar) while data is being fetched so that I know the process is ongoing.

**Acceptance Criteria:**
- The progress bar appears during API requests.
- After loading completes, the progress bar disappears.

#### User Story 2.1.3: Highlight products with low rating (< 3.5)
As a user, I want to see products with a rating < 3.5 highlighted in red so that I can quickly spot low-quality items.

**Acceptance Criteria:**
- Each row's rating is checked.
- If the rating is < 3.5, the rating value or the entire row is highlighted in red (per design).

### Feature 2.2: Product Sorting

#### User Story 2.2.1: Sort by columns (price, rating, etc.)
As a user, I want to sort products by columns (e.g., price or rating) so that I can order data conveniently.

**Acceptance Criteria:**
- Sorting is available for columns: Price, Rating (and others if specified in the design).
- Clicking a column header toggles sorting (ascending/descending).

#### User Story 2.2.2: Persist sorting state
As a user, I want the sorting state to persist after page reload so that I don't have to reconfigure it.

**Acceptance Criteria:**
- Sorting state (column, direction) is stored in URL parameters or localStorage.
- On page reload, the saved state is applied.

#### User Story 2.2.3: Three-dot menu (refresh and quick sort)
As a user, I want to use the three-dot menu to refresh the table and apply quick sorting so that I can manage data display.

**Acceptance Criteria:**
- The three-dot icon opens a dropdown menu.
- A "Refresh" option reloads table data.
- "Sort ascending/descending" options apply sorting to the active column (or the first column by default).

### Feature 2.3: Add New Product (Local)

#### User Story 2.3.1: Open add product form
As a user, I want to open the add product form via the "Add" button so that I can enter details for a new product.

**Acceptance Criteria:**
- Clicking "Add" opens a modal or a separate form.
- Form fields: Name, Price, Vendor, SKU.

#### User Story 2.3.2: Local addition without saving to API
As a user, I want to add a product locally (without saving to the server) so that I can see it in the table immediately.

**Acceptance Criteria:**
- After filling and submitting the form, the product is added to the state (Zustand) and displayed in the table.
- No API request is made for saving.
- A basic Toast notification appears on successful addition.

### Feature 2.4: Product Search

#### User Story 2.4.1: Search via API by name
As a user, I want to search for products by name through the API so that I can quickly find the desired product.

**Acceptance Criteria:**
- A search field is present on the product list page.
- Search is implemented via the DummyJSON API (query parameter).
- Search results are displayed in the table.
- A loading state (progress bar) is shown during the search request.
