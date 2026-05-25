# Express TypeScript Backend Boilerplate

A production-ready REST API starter built with **Express**, **TypeScript**, **PostgreSQL**, and **JWT authentication**.

---

## Tech Stack

| Tool              | Purpose                    |
| ----------------- | -------------------------- |
| Express           | HTTP server & routing      |
| TypeScript        | Type safety                |
| PostgreSQL (`pg`) | Database                   |
| bcryptjs          | Password hashing           |
| jsonwebtoken      | JWT auth                   |
| dotenv            | Environment config         |
| cors              | Cross-origin requests      |
| tsup              | Bundler (ESM output)       |
| tsx               | Dev server with hot reload |

---

## Project Structure

```
├── src/
│   ├── config/
│   │   └── index.ts          # App configuration & env vars
│   ├── db/
│   │   └── index.ts          # PostgreSQL connection pool
│   ├── middleware/
│   │   └── auth.ts           # JWT authentication middleware
│   ├── utils/                # Shared utility functions
│   ├── modules/              # Feature modules (routes, controllers, services)
│   ├── app.ts                # Express app setup
│   └── server.ts             # Server entry point
├── dist/                     # Compiled output (generated)
├── .env                      # Environment variables (do not commit)
├── .gitignore
├── tsconfig.json
├── tsup.config.js
└── package.json
```

---

## Getting Started

### Prerequisites

- Node.js v18+
- PostgreSQL running locally or a connection string

### 1. Clone & Install

```bash
git clone https://github.com/mdyhakash/backend_boilerplate_ts.git
cd backend_boilerplate_ts
npm install
```

### 2. Set Up Environment Variables

Create a `.env` file in the project root:

```env
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
JWT_SECRET=your_super_secret_key
NODE_ENV=development
```

### 3. Run in Development

```bash
npm run dev
```

Uses `tsx watch` for hot reload on file changes.

### 4. Build for Production

```bash
npm run build
```

Compiles TypeScript via `tsup` into the `dist/` folder as an ESM bundle.

### 5. Start Production Server

```bash
npm start
```

Runs the compiled `dist/server.js`.

---

## Scripts

| Script  | Command                     | Description                 |
| ------- | --------------------------- | --------------------------- |
| `dev`   | `tsx watch ./src/server.ts` | Development with hot reload |
| `build` | `tsup`                      | Bundle for production       |
| `start` | `node dist/server.js`       | Run production build        |

---

## Configuration Files

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### `tsup.config.js`

Bundles the app as ESM with a `createRequire` banner for CommonJS compatibility:

```js
import { defineConfig } from "tsup";

export default defineConfig({
  entry: ["src/server.ts"],
  format: ["esm"],
  target: "esnext",
  outDir: "dist",
  clean: true,
  bundle: true,
  splitting: false,
  sourcemap: true,
  banner: {
    js: `
    import {createRequire} from 'module';
    const require = createRequire(import.meta.url);
    `,
  },
});
```

> The `createRequire` banner allows the ESM bundle to use CommonJS `require()` where needed (e.g., for packages that don't ship ESM).

---

## Adding a New Module

Modules are self-contained feature units. Follow this pattern inside `src/modules/`:

```
modules/
└── users/
    ├── users.router.ts      # Express Router
    ├── users.controller.ts  # Request handlers
    ├── users.service.ts     # Business logic & DB queries
    └── users.interface.ts   # Type declarations
```

Register the router in `src/app.ts`:

```ts
import usersRouter from "./modules/users/users.router.js";
app.use("/api/users", usersRouter);
```

---

## Authentication

JWT middleware lives in `src/middleware/auth.ts`. Protect a route by adding it:

```ts
import { authMiddleware } from "../middleware/auth.js";

router.get("/profile", authMiddleware, getProfile);
```

The middleware reads the `Authorization: Bearer <token>` header and attaches the decoded payload to `req.user`.

---

## Database

The PostgreSQL pool is initialized in `src/db/index.ts`. Import it anywhere you need a query:

```ts
import pool from "../db/index.js";

const result = await pool.query("SELECT * FROM users WHERE id = $1", [id]);
```

---

## .gitignore

The following are excluded from version control:

```
node_modules/
dist/
.env
*.log
```

---

## Dependencies

### Runtime

```bash
npm install express pg bcryptjs jsonwebtoken tsup dotenv cors
```

### Dev

```bash
npm install -D typescript tsx @types/express @types/pg @types/jsonwebtoken @types/cors
```
