# TaskConnect - A React App with Dark/Light Theme Toggle

TaskConnect is a React application that allows users to post tasks and get bids from freelancers. This project includes a dark/light theme toggle feature, implemented using a custom `ThemeContext` and styled with Tailwind CSS for responsive design.

## Features

- **Dark/Light Theme Toggle**: Switch between dark and light modes with a toggle button.
- **React Router**: Navigate between pages like Home, Browse Tasks, Add Task, and My Posted Tasks.
- **Tailwind CSS**: Responsive and theme-aware styling for a polished UI.

## Prerequisites

Before you begin, ensure you have the following installed:

- Node.js (v16 or higher)
- npm or yarn
- Vite (for the development server)
- Tailwind CSS (v4 or higher)

## Setup Instructions

### 1. Create a Theme Context

First, create a `ThemeContext` to manage the theme state (dark or light) across your app. This will allow components to access and toggle the theme.

**File**: `src/ThemeContext.jsx`

```jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light"); // Default to light mode
  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => useContext(ThemeContext);
```

### 2. Wrap Your App with ThemeProvider

Wrap your entire app with the `ThemeProvider` in main.jsx to make the theme context available to all components.

**File**: `src/main.jsx`

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import { RouterProvider } from "react-router-dom";
import { router } from "./Router.jsx";
import { AuthProvider } from "./Provider/AuthProvider.jsx";
import { ThemeProvider } from "./ThemeContext.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <ThemeProvider>
      <AuthProvider>
        <RouterProvider router={router} />
      </AuthProvider>
    </ThemeProvider>
  </StrictMode>
);
```

### 3. Create a Toggle Button Component

Create a `ThemeToggle` component that uses the `useTheme` hook to switch between themes. Add icons for a better user experience.

**File**: `src/components/ThemeToggle.jsx`

```jsx
import { useTheme } from "../ThemeContext";
import { GoSun } from "react-icons/go";
import { MdOutlineDarkMode } from "react-icons/md";

export const ThemeToggle = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <button
      onClick={toggleTheme}
      className="p-2 rounded-full hover:bg-gray-200 dark:hover:bg-gray-700"
      style={{ color: "var(--text-color)" }}
    >
      {theme === "light" ? (
        <GoSun size={24} />
      ) : (
        <MdOutlineDarkMode size={24} />
      )}
    </button>
  );
};
```

Note: Install the `react-icons` package if you haven’t already:

### 4. Apply the Theme to the Root Element

Apply the data-theme attribute and Tailwind’s dark class to the html element based on the current theme. For my project, the root component was in `Layout` folder named `MainLayout.jsx`.

**File**: `src/LayOut/MainLayOut.jsx`

```jsx
import { useEffect } from "react";
import { Outlet } from "react-router-dom";
import { useTheme } from "../ThemeContext";
import Header from "../components/Header";
import Footer from "../components/Footer";

const MainLayOut = () => {
  const { theme } = useTheme();

  useEffect(() => {
    document.documentElement.setAttribute("data-theme", theme);
    if (theme === "dark") {
      document.documentElement.classList.add("dark");
    } else {
      document.documentElement.classList.remove("dark");
    }
  }, [theme]);

  return (
    <div
      className="min-h-screen"
      style={{ backgroundColor: "var(--background-color)" }}
    >
      <Header />
      <div className="min-h-[calc(100vh-116px)]">
        <div className="max-w-screen-2xl mx-auto px-8 md:px-12 lg:px-16 xl:px-24">
          <Outlet />
        </div>
      </div>
      <Footer />
    </div>
  );
};

export default MainLayOut;
```

### 5. Add the Toggle Button to the Navbar

Add the ThemeToggle component to your Header.jsx (navbar) so users can switch themes easily. Update Header.jsx to include the toggle button.

### 6. Use Dark Mode Styles

To apply dark/light theme styles in any component, use Tailwind CSS for layout and design. Alternatively, create a reusable ThemedCard component to wrap elements that need theme-aware styling.

### Option 1: Use Tailwind Directly in Components

Use Tailwind's built-in dark: variants to style elements differently in dark mode..

Example: In a page or component

**File**: `src/LayOut/MainLayOut.jsx`

```jsx
<div className="bg-white dark:bg-gray-900 text-black dark:text-white">
  {/* Your content */}
</div>
```

### Option 2: Create a ThemedCard Component

Create a reusable `ThemedCard` component to wrap elements and apply theme-aware styling automatically.

**File**: `src/Component/ThemeCard.jsx`

```jsx
const ThemedCard = ({ children }) => {
  return (
    <div className="bg-white dark:bg-gray-900 text-black dark:text-white  ">
      {children}
    </div>
  );
};

export default ThemedCard;
```
