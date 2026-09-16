# Development Guide

## Quick Start

### Option 1: Using Docker (Recommended)

```bash
# Clone and navigate to the project
git clone https://github.com/dripperzanka-stack/theflipper.site.git
cd theflipper.site

# Copy environment file
cp .env.example .env

# Build and start all services
docker-compose up -d

# Check logs
docker-compose logs -f
```

Access:
- Frontend: http://localhost:3000
- Backend: http://localhost:5000
- Database: localhost:5432

### Option 2: Local Development

#### Setup Backend

```bash
cd backend
cp ../.env.example .env

# Install dependencies
npm install

# Start development server
npm run dev
```

Backend will run on http://localhost:5000

#### Setup Frontend

```bash
cd frontend
cp ../.env.example .env

# Install dependencies
npm install

# Start development server
npm start
```

Frontend will run on http://localhost:3000

#### Setup Database

```bash
# Create PostgreSQL database
createdb theflipper

# Run migrations/schema
psql theflipper < ../database/schema.sql
```

## File Structure

### Backend

```
backend/
├── src/
│   ├── server.ts          # Entry point
│   ├── routes/            # API routes
│   ├── controllers/       # Request handlers
│   ├── models/            # Database models
│   ├── middleware/        # Custom middleware
│   ├── services/          # Business logic
│   ├── database/          # Database setup
│   └── types/             # TypeScript types
├── dist/                  # Compiled output
├── package.json
└── tsconfig.json
```

### Frontend

```
frontend/
├── src/
│   ├── App.tsx            # Main app component
│   ├── index.tsx          # Entry point
│   ├── components/        # Reusable components
│   │   ├── Navbar.tsx
│   │   ├── Sidebar.tsx
│   │   └── ...
│   ├── pages/             # Page components
│   │   ├── Home.tsx
│   │   ├── BotBuilder.tsx
│   │   ├── Dashboard.tsx
│   │   └── ...
│   ├── store/             # Redux store
│   │   ├── slices/
│   │   └── ...
│   ├── services/          # API services
│   │   ├── api.ts
│   │   ├── authService.ts
│   │   └── ...
│   ├── hooks/             # Custom hooks
│   ├── types/             # TypeScript types
│   └── index.css          # Global styles
├── public/
├── build/                 # Production build
└── package.json
```

## Available Scripts

### Backend

```bash
npm run dev           # Start development server with hot-reload
npm run build         # Compile TypeScript
npm start             # Run compiled server
npm test              # Run tests
npm run test:watch    # Run tests in watch mode
npm run lint          # Run ESLint
npm run migrate       # Run database migrations
```

### Frontend

```bash
npm start             # Start development server
npm run build         # Build for production
npm test              # Run tests
npm run eject         # Eject from react-scripts (not recommended)
```

## API Development

### Creating a New Route

1. **Create a Controller** (`backend/src/controllers/example.controller.ts`):

```typescript
import { Request, Response } from 'express';

export const exampleController = {
  getExample: (req: Request, res: Response) => {
    res.json({ message: 'Example response' });
  },
};
```

2. **Create a Route** (`backend/src/routes/example.routes.ts`):

```typescript
import express from 'express';
import { exampleController } from '../controllers/example.controller';

const router = express.Router();

router.get('/', exampleController.getExample);

export default router;
```

3. **Register in Server** (`backend/src/server.ts`):

```typescript
import exampleRoutes from './routes/example.routes';
app.use('/api/example', exampleRoutes);
```

## Database Development

### Creating Migrations

1. Create a new migration file in `database/migrations/`:

```sql
-- migrations/001_create_example_table.sql
CREATE TABLE IF NOT EXISTS examples (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

2. Run migration:

```bash
npm run migrate
```

### Seeding Data

Create `database/seeds/seed.sql`:

```sql
INSERT INTO examples (name) VALUES ('Example 1'), ('Example 2');
```

Run seeds:

```bash
psql theflipper < database/seeds/seed.sql
```

## Frontend Development

### Creating Components

```typescript
// src/components/Example.tsx
import React from 'react';

interface ExampleProps {
  title: string;
  content: string;
}

const Example: React.FC<ExampleProps> = ({ title, content }) => {
  return (
    <div className="bg-white p-4 rounded-lg shadow">
      <h2 className="text-xl font-bold">{title}</h2>
      <p>{content}</p>
    </div>
  );
};

export default Example;
```

### Creating Pages

```typescript
// src/pages/ExamplePage.tsx
import React from 'react';
import Example from '../components/Example';

const ExamplePage: React.FC = () => {
  return (
    <div className="container mx-auto">
      <Example title="Page Title" content="Page content" />
    </div>
  );
};

export default ExamplePage;
```

### Using Redux

1. Create a slice (`src/store/slices/example.slice.ts`):

```typescript
import { createSlice } from '@reduxjs/toolkit';

const exampleSlice = createSlice({
  name: 'example',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
  },
});

export default exampleSlice.reducer;
```

2. Use in component:

```typescript
import { useDispatch, useSelector } from 'react-redux';
import { increment } from '../store/slices/example.slice';

const MyComponent = () => {
  const dispatch = useDispatch();
  const value = useSelector((state: any) => state.example.value);

  return (
    <div>
      <p>Value: {value}</p>
      <button onClick={() => dispatch(increment())}>Increment</button>
    </div>
  );
};
```

## Testing

### Backend Tests

```bash
# Run all tests
npm test

# Run specific test
npm test -- example.test.ts

# Run in watch mode
npm run test:watch
```

### Frontend Tests

```bash
# Run all tests
npm test

# Run specific test
npm test Example.test.tsx

# Coverage
npm test -- --coverage
```

## Debugging

### Backend

1. Add breakpoints in VS Code
2. Run with debugger:

```bash
node --inspect-brk ./node_modules/.bin/ts-node src/server.ts
```

3. Open `chrome://inspect`

### Frontend

1. React DevTools browser extension
2. Redux DevTools for state debugging
3. Browser DevTools console and debugger

## Environment Variables

Copy `.env.example` to `.env` and update values:

```bash
cp .env.example .env
```

### Required Variables

- `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` - Database
- `JWT_SECRET` - JWT signing key
- `NODE_ENV` - Environment (development/production)
- `PORT` - Backend port
- `REACT_APP_API_URL` - Backend API URL

## Troubleshooting

### Database Connection Failed

```bash
# Check PostgreSQL is running
psql -U postgres

# Reset database
dropdb theflipper
createdb theflipper
psql theflipper < database/schema.sql
```

### Port Already in Use

```bash
# Find and kill process on port
lsof -i :5000  # Backend
lsof -i :3000  # Frontend
kill -9 <PID>
```

### Dependencies Issues

```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
```

## Git Workflow

1. Create feature branch:

```bash
git checkout -b feature/your-feature-name
```

2. Make changes and commit:

```bash
git add .
git commit -m "feat: describe your changes"
```

3. Push to remote:

```bash
git push origin feature/your-feature-name
```

4. Create Pull Request on GitHub

## Deployment

See `DEPLOYMENT.md` for production deployment instructions.

---

**Need help?** Check the [README.md](README.md) or open an issue on GitHub.
