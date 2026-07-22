# EasyShop — Your One-Stop Shopping Destination

A full-stack e-commerce web application built with modern technologies, featuring a responsive design, secure authentication, and a complete shopping experience.

---

## Table of Contents

- [Tech Stack](#tech-stack)
- [Features](#features)
- [Project Structure](#project-structure)
- [Pages & Routes](#pages--routes)
- [API Routes](#api-routes)
- [Authentication & Authorization](#authentication--authorization)
- [Database](#database)
- [State Management](#state-management)
- [Environment Variables](#environment-variables)
- [How to Run](#how-to-run)
- [Deployment Notes](#deployment-notes)

---

## Tech Stack

| Category | Technology |
|----------|------------|
| **Framework** | Next.js 14+ (App Router) |
| **Language** | TypeScript |
| **Database** | MongoDB with Mongoose ODM |
| **State Management** | Redux Toolkit |
| **Styling** | Tailwind CSS |
| **UI Components** | shadcn/ui |
| **HTTP Client** | Axios |
| **Authentication** | JWT (cookie-based) |

---

## Features

### Core Shopping Features

- **Multi-category product catalog** — Browse products across 7 categories:
  - Clothing
  - Gadgets
  - Makeup
  - Furniture
  - Books
  - Bakery
  - Groceries

- **Hero slider** with promotional banners
- **Banner carousel** for dynamic content
- **Shopping cart** with add/remove/quantity management
- **Checkout flow** with order placement
- **Order history** for authenticated users
- **User profile** management

### Admin Dashboard

- Product management (view all products)
- Order management (view orders)
- Admin-only access control

### UI/UX Features

- **Light/Dark theme** toggle
- **Mobile responsive** design with bottom navigation
- **Scroll-to-top** button for improved navigation
- **Featured products** section
- **Special offers** page

---

## Project Structure

```
e-commerce-app/
├── src/
│   ├── app/                    # Next.js App Router pages & API routes
│   │   ├── (auth)/            # Auth-related pages (login, register)
│   │   ├── (shop)/            # Shop pages (products, categories)
│   │   ├── admin/             # Admin dashboard
│   │   ├── api/               # API routes
│   │   ├── checkout/          # Checkout page
│   │   ├── orders/            # Order history
│   │   ├── profile/           # User profile
│   │   ├── layout.tsx         # Root layout
│   │   └── page.tsx           # Home page
│   ├── components/            # Reusable React components
│   │   ├── ui/                # shadcn/ui components
│   │   ├── layout/            # Layout components (header, footer, etc.)
│   │   └── ...
│   ├── lib/                   # Core utilities
│   │   ├── store/             # Redux store configuration
│   │   ├── features/          # Redux slices (auth, cart, sidebar)
│   │   ├── db.ts              # MongoDB connection (cached)
│   │   ├── auth.ts            # JWT authentication utilities
│   │   └── api.ts             # Axios API client
│   ├── types.d.ts             # TypeScript type definitions
│   ├── middleware.ts          # Next.js middleware for route protection
│   └── data/                  # JSON data files (products, categories)
├── public/                    # Static assets
├── .env.example               # Environment variable template
└── package.json
```

### Key Directories

| Directory | Purpose |
|-----------|---------|
| `src/app/` | Next.js 14 App Router structure with pages and API routes |
| `src/components/` | Reusable React components organized by feature |
| `src/lib/` | Core library code: Redux store, database, auth, API client |
| `src/lib/features/` | Redux Toolkit slices for state management |
| `src/data/` | JSON data files for products and categories |
| `src/middleware.ts` | Route protection and authentication middleware |

---

## Pages & Routes

### Public Pages

| Route | Description |
|-------|-------------|
| `/` | Home page with hero slider and featured products |
| `/products` | All products listing |
| `/products/[id]` | Single product detail page |
| `/products/clothing` | Clothing category |
| `/products/gadgets` | Gadgets category |
| `/products/makeup` | Makeup category |
| `/products/furniture` | Furniture category |
| `/products/books` | Books category |
| `/products/bakery` | Bakery category |
| `/products/groceries` | Groceries category |
| `/shops` | Shops listing page |
| `/offers` | Special offers page |

### Authenticated Pages

| Route | Description | Protection |
|-------|-------------|------------|
| `/login` | User login page | Redirects to `/profile` if already logged in |
| `/register` | User registration page | Redirects to `/profile` if already logged in |
| `/checkout` | Checkout page | Requires authentication |
| `/orders` | Order history | Requires authentication |
| `/profile` | User profile page | Requires authentication |

### Admin Pages

| Route | Description | Protection |
|-------|-------------|------------|
| `/admin` | Admin dashboard | Requires admin role |

---

## API Routes

### Authentication APIs

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/auth/login` | POST | User login with email/password |
| `/api/auth/register` | POST | User registration |
| `/api/auth/me` | GET | Get current user info |
| `/api/auth/logout` | POST | Clear auth cookie |
| `/api/auth/check` | GET | Check authentication status |

### Product APIs

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/products` | GET | Get all products |
| `/api/products/featured` | GET | Get featured products |
| `/api/products/books` | GET | Get books category products |
| `/api/products/[productId]` | GET | Get single product by ID |
| `/api/singleProduct` | GET | Get single product (alternative) |

### Cart & Orders APIs

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/cart` | GET/POST | Get or update cart items |
| `/api/orders` | GET/POST | Get or create orders |

---

## Authentication & Authorization

### JWT Cookie-Based Authentication

- JWT tokens are stored in HTTP-only cookies for security
- Token includes user ID, email, and role
- Tokens are verified on protected route access

### Route Protection

Route protection is handled by `src/middleware.ts`:

```
Protected Routes (require auth):
  - /checkout
  - /profile
  - /orders

Admin-Only Routes:
  - /admin (requires admin role)

Auth Pages (redirect if logged in):
  - /login → redirects to /profile if already authenticated
  - /register → redirects to /profile if already authenticated
```

### Authorization Flow

1. User submits credentials via `/api/auth/login`
2. Server validates and returns JWT in HTTP-only cookie
3. Middleware checks cookie on protected routes
4. If valid, request proceeds; otherwise redirect to login
5. Admin routes additionally check for admin role

---

## Database

### MongoDB Connection

- **Connection**: MongoDB with Mongoose ODM
- **Fallback URI**: `mongodb://localhost:27017/easyshop`
- **Connection Caching**: Connections are cached in `src/lib/db.ts` for performance

### Mongoose Schema

The application uses Mongoose schemas for:
- Users (authentication, profile)
- Products (catalog items)
- Orders (transaction records)
- Cart items (user shopping carts)

### Connection Pattern

```typescript
// Cached connection in src/lib/db.ts
import mongoose from 'mongoose';

const MONGODB_URI = process.env.MONGODB_URI || 'mongodb://localhost:27017/easyshop';

let cached = global.mongoose;

if (!cached) {
  cached = global.mongoose = { conn: null, promise: null };
}

async function connectDB() {
  if (cached.conn) return cached.conn;

  if (!cached.promise) {
    cached.promise = mongoose.connect(MONGODB_URI).then((mongoose) => {
      return mongoose;
    });
  }
  cached.conn = await cached.promise;
  return cached.conn;
}
```

---

## State Management

### Redux Toolkit Slices

| Slice | Purpose |
|-------|---------|
| `authSlice` | User authentication state (user info, isAuthenticated, isAdmin) |
| `cartSlice` | Shopping cart state (items, quantities, totals) |
| `sidebarSlice` | UI state for sidebar/drawer components |

### Store Structure

```typescript
// src/lib/store/index.ts
export const store = configureStore({
  reducer: {
    auth: authSlice,
    cart: cartSlice,
    sidebar: sidebarSlice,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### Usage in Components

```typescript
import { useAppSelector, useAppDispatch } from '@/lib/hooks';
import { increment, decrement, removeItem } from '@/lib/features/cart/cartSlice';

// In component
const dispatch = useAppDispatch();
const cartItems = useAppSelector((state) => state.cart.items);
```

---

## Environment Variables

Create a `.env.local` file in the project root with the following variables:

```bash
# Database
MONGODB_URI=mongodb://localhost:27017/easyshop

# Authentication (required)
JWT_SECRET=your-super-secret-jwt-key-min-32-characters

# Optional
NEXT_PUBLIC_API_URL=http://localhost:3000

# If using NextAuth (optional)
NEXTAUTH_SECRET=your-nextauth-secret
NEXTAUTH_URL=http://localhost:3000
```

### Required Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `MONGODB_URI` | Yes | `mongodb://localhost:27017/easyshop` | MongoDB connection string |
| `JWT_SECRET` | Yes | — | Secret key for JWT signing (min 32 chars) |

### Optional Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `NEXT_PUBLIC_API_URL` | `http://localhost:3000` | Public API URL |
| `NEXTAUTH_SECRET` | — | NextAuth secret (if using NextAuth) |
| `NEXTAUTH_URL` | `http://localhost:3000` | NextAuth URL (if using NextAuth) |

**Note**: No `.env` file is present in the repository. You must create `.env.local` with the required variables before running the application.

---

## How to Run

### Prerequisites

- **Node.js** 18.x or later
- **npm** or **pnpm** package manager
- **MongoDB** running locally or a MongoDB Atlas connection string

### Installation Steps

1. **Clone the repository**

```bash
git clone https://github.com/zeeshankanuga/e-commerce-app.git
cd e-commerce-app
```

2. **Install dependencies**

```bash
npm install
# or
pnpm install
```

3. **Set up environment variables**

```bash
# Create .env.local file
cat > .env.local << EOF
MONGODB_URI=mongodb://localhost:27017/easyshop
JWT_SECRET=your-super-secret-jwt-key-min-32-characters
EOF
```

4. **Start MongoDB** (if running locally)

```bash
# macOS with Homebrew
brew services start mongodb-community

# or Linux
sudo systemctl start mongod
```

5. **Run the development server**

```bash
npm run dev
# or
pnpm dev
```

6. **Open the application**

Navigate to [http://localhost:3000](http://localhost:3000) in your browser.

### Build for Production

```bash
npm run build
npm start
```

### Available Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm start` | Start production server |
| `npm run lint` | Run ESLint |
| `npm run lint:fix` | Fix ESLint errors |
| `npm run db:push` | Push Mongoose schemas to database |

---

## Deployment Notes

### Docker Support

The application includes Docker support for containerized deployment:

```bash
# Build Docker image
docker build -t easyshop:latest .

# Run container
docker run -p 3000:3000 --env-file .env.local easyshop:latest
```

### Cloud Deployment

The application can be deployed on various platforms:

| Platform | Notes |
|----------|-------|
| **Vercel** | Recommended for Next.js apps. Configure environment variables in dashboard |
| **Netlify** | Works with Next.js. Use `netlify.toml` for configuration |
| **AWS** | Deploy on ECS, EKS, or Beanstalk |
| **DigitalOcean App Platform** | Supports Next.js with Docker |
| **Railway** | Connect MongoDB Atlas and deploy |

### Production DevOps Architecture

This project is designed to integrate with a comprehensive DevOps pipeline:

```
Developer → GitHub → Jenkins → Build/Test/Scan → Docker Build → DockerHub → GitOps Repo → ArgoCD → AWS EKS → Istio → Application → Monitoring
```

**Production Pipeline Components:**

| Component | Technology | Purpose |
|-----------|------------|---------|
| **CI/CD** | Jenkins | Automated build, test, and deployment pipeline |
| **Containerization** | Docker, DockerHub | Image build and registry |
| **Infrastructure** | Terraform, AWS | Infrastructure as Code provisioning |
| **Orchestration** | AWS EKS (Kubernetes) | Container orchestration |
| **Service Mesh** | Istio | Traffic management, mTLS, canary deployments |
| **GitOps** | ArgoCD | Automated deployment sync from Git |
| **Monitoring** | Prometheus, Grafana | Metrics and visualization |
| **Logging** | FluentBit, OpenSearch | Centralized logging |
| **Alerting** | AlertManager | Incident notifications |

### Environment-Specific Configuration

For production deployments, ensure:

- [ ] Set `MONGODB_URI` to production database
- [ ] Configure `JWT_SECRET` with a strong, unique value
- [ ] Enable HTTPS/SSL termination
- [ ] Configure environment variables in cloud dashboard or secrets manager
- [ ] Set up MongoDB Atlas or managed database for production
- [ ] Configure CDN for static assets

---

## License

This project is open source and available under the MIT License.

---

## Author

Zeeshan Kanuga