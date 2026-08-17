# Vibe Shop – Product Management Dashboard

A modern, reactive product management dashboard built with **Vite + React 18**, **TypeScript**, and **Tailwind CSS**.  
This project follows a **"vibe coding"** philosophy – clean, efficient, and enjoyable development experience with a focus on smooth interactions and a polished UI.

---

## 🚀 Features

- **Authentication** – Login form with field validation and persistent session (via token storage).
- **Product Listing** – Fetches products from DummyJSON API with loading states and error handling.
- **Sorting** – Sort by any column (e.g., price, rating) with persistent state.
- **Search** – Real-time product search via the API.
- **Add Product** – In-memory product addition with a modal form and toast notifications.
- **Visual Feedback** – Products with rating < 3.5 are highlighted in red; progress bar on load.
- **Responsive UI** – Matches the provided Figma layout exactly.
- **State Management** – Zustand for global state, persists sorting and session preferences.

---

## 🛠️ Tech Stack

| Category       | Technologies                                                                 |
|----------------|------------------------------------------------------------------------------|
| Build Tool     | [Vite](https://vitejs.dev/)                                                  |
| Framework      | [React 18+](https://reactjs.org/)                                            |
| Language       | [TypeScript](https://www.typescriptlang.org/) (strict mode)                  |
| State Management | [Zustand](https://zustand-demo.pmnd.rs/)                                  |
| Styling        | [Tailwind CSS](https://tailwindcss.com/)                                     |
| HTTP Client    | Fetch API (native)                                                          |
| Notifications  | Custom toast system (or `react-hot-toast` – flexible)                       |
| Compatibility  | Latest Google Chrome (and modern browsers)                                  |

---

## 🎨 Design

The user interface is **pixel-perfect** with the [Figma layout](<link-to-figma>).  
All components, spacing, colors, and typography follow the design system provided.

---

## 📡 API Integration

- **Products**: [DummyJSON Products](https://dummyjson.com/docs/products)  
- **Authentication**: [DummyJSON Auth](https://dummyjson.com/docs/auth)

All API calls are handled via asynchronous functions with proper error handling and loading states.

---

## 📋 Functional Requirements

### 1. Login Form
- **Validation**: All fields are required.
- **Error Handling**: Server errors are displayed below the respective fields or via a notification.
- **"Create" link** is a non-functional placeholder.
- **Session Persistence**:
  - If **"Remember me"** is checked → token is stored in `localStorage` (persists after browser restart).
  - If unchecked → token is stored in `sessionStorage` (cleared when the tab is closed).

### 2. Product List Page
- **Layout**: Matches Figma columns.
- **Loading**: Shows a progress bar while fetching data.
- **Data**: Fetches from DummyJSON API.
- **Sorting**:
  - Sort by any column (e.g., price, rating).
  - Sorting state is persisted (e.g., via Zustand).
- **Add Product**:
  - Click "Add" opens a modal form with fields: **Name**, **Price**, **Vendor**, **SKU**.
  - On successful addition, a **toast notification** appears.
  - Products are stored locally (no API save required).
- **UI Logic**:
  - Products with rating < 3.5 are highlighted in **red**.
  - The three-dot icon (⋮) serves as a placeholder for:
    - Refresh the table
    - Sort ascending/descending
  - Sorting indicators are visually intuitive (arrow icons).
- **Search**: Search products via API (query parameter).

---

## 📦 Installation & Setup

1. **Clone the repository**  
   ```bash
   git clone https://github.com/your-username/vibe-shop.git
   cd vibe-shop
   ```

2. **Install dependencies**  
   ```bash
   npm install
   # or
   yarn install
   ```

3. **Start the development server**  
   ```bash
   npm run dev
   # or
   yarn dev
   ```

4. **Build for production**  
   ```bash
   npm run build
   # or
   yarn build
   ```

5. **Preview the production build**  
   ```bash
   npm run preview
   # or
   yarn preview
   ```

---

## 🧪 Testing & Linting

- **TypeScript**: `tsc --noEmit` for type checking.
- **ESLint**: `npm run lint` (if configured).
- **Prettier**: Format code with `npm run format`.

---

## 📁 Project Structure

```
src/
├── components/       # Reusable UI components (Button, Input, Modal, Toast, etc.)
├── pages/            # LoginPage, ProductListPage
├── store/            # Zustand stores (auth, products, sort, etc.)
├── hooks/            # Custom hooks (useFetch, useLocalStorage, etc.)
├── services/         # API service functions
├── types/            # TypeScript interfaces and types
├── utils/            # Helper functions (validators, formatters)
└── App.tsx           # Main app with routes
```

---

## 🔮 Future Improvements

- Implement API save for new products.
- Add pagination/infinite scroll.
- Unit and integration tests with Vitest.
- Dark mode toggle.
- More advanced sorting (multi-column).

---

## 👨‍💻 Contributing

Contributions are welcome! Please open an issue or submit a pull request.

---

## 📄 License

[MIT](LICENSE)

---

## 💬 Contact

For any questions, reach out to [your-email] or open an issue.

---

**Happy vibing! ✨**
