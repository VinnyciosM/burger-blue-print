# digital-loyalty-card Project Overview for Gemini

This document provides a comprehensive overview of the `digital-loyalty-card` project, generated to assist the Gemini CLI in understanding its structure, purpose, and operational procedures.

## Project Overview

The `digital-loyalty-card` is a web application built using **Vue 3** and **Vite**, designed to function as a digital loyalty card system. It is a modern JavaScript project utilizing `type: module` for its module system.

## Building and Running

### Prerequisites

*   Node.js: `^20.19.0 || >=22.12.0`

### Setup

To set up the project locally, navigate to the project root and install the dependencies:

```sh
npm install
```

### Development

To run the application in development mode with hot-reloading:

```sh
npm run dev
```

This will typically start a development server, accessible in your browser at a local address (e.g., `http://localhost:5173`).

### Production Build

To compile and minify the application for production deployment:

```sh
npm run build
```

This command generates optimized static assets in the `dist` directory.

### Preview Production Build

To preview the production build locally:

```sh
npm run preview
```

## Development Conventions

*   **Framework:** Vue 3
*   **Build Tool:** Vite
*   **IDE Setup:** Recommended VS Code with the official Vue (Volar) extension.
*   **Browser DevTools:** Vue.js devtools are recommended for Chromium-based browsers and Firefox.
*   **Code Structure:** The project follows a standard Vue CLI/Vite project structure, with `src` containing application source code, `public` for static assets, and `components` for reusable Vue components.
