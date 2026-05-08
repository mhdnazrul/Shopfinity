# 🛍️ Shopfinity – Full Stack eCommerce Platform

[![.NET](https://img.shields.io/badge/.NET-9-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=next.js)](https://nextjs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16+-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)

## 🚀 Overview

**Shopfinity** is a production-oriented eCommerce demo: a **Next.js** storefront and admin UI talking to an **ASP.NET Core** REST API, with **PostgreSQL** as the primary database. It includes authentication (JWT cookies + CSRF for mutating requests), product catalog, cart, wishlist, checkout, orders, reviews, and an admin area for products and orders.

This repository is structured as a **monorepo**: the API and domain live under `Shopfinity.*` projects; the web app lives in **`shopfinity-web/`**.

## ✨ Features

| Area | Highlights |
|------|------------|
| **Auth** | Register / login, JWT in HttpOnly cookies, **CSRF** (`XSRF-TOKEN` / `X-XSRF-TOKEN`) on POST/PUT/PATCH/DELETE |
| **Catalog** | Categories, product detail, **ranked search** (including PostgreSQL `pg_trgm` suggestions) |
| **Cart & wishlist** | Server-backed cart; wishlist with conflict handling |
| **Checkout** | Order placement with stock checks |
| **Admin** | Products CRUD, categories, orders, image uploads |
| **Search** | Navbar typeahead + listing filters; API search endpoints |

## 🧠 Tech Stack

**Frontend (`shopfinity-web/`)**

- [Next.js](https://nextjs.org/) 16 (App Router)
- TypeScript
- [TanStack Query](https://tanstack.com/query) (React Query)
- [Tailwind CSS](https://tailwindcss.com/) 4
- [Axios](https://axios-http.com/) (with `withCredentials` for cookie auth)
- [React Hook Form](https://react-hook-form.com/) + [Zod](https://zod.dev/)

**Backend**

- ASP.NET Core **Web API** (Clean Architecture–style layout)
- **Entity Framework Core** + PostgreSQL (`Npgsql`)
- ASP.NET Core **Identity**
- FluentValidation, AutoMapper, rate limiting on selected endpoints

**Database**

- **PostgreSQL** (required for full feature parity; search suggestions use `pg_trgm`)

## 📁 Repository layout

```
shopfinity/
├── Shopfinity.API/              # HTTP API, middleware, DI
├── Shopfinity.Application/      # Use cases, DTOs, services
├── Shopfinity.Domain/           # Entities, enums
├── Shopfinity.Infrastructure/   # EF Core, migrations, Identity
├── Shopfinity.Tests/            # Unit / integration tests
├── shopfinity-web/              # Next.js app (store + admin)
├── Shopfinity.sln
├── LICENSE
└── README.md
```

## ⚙️ Installation

### Prerequisites

- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
- [Node.js](https://nodejs.org/) 20+ (LTS recommended)
- [PostgreSQL](https://www.postgresql.org/download/) 14+

### 1. Clone the repository

```bash
git clone https://github.com/mhdnazrul/shopfinity.git
cd shopfinity
```

### 2. Database

Create a database and a user (or use `postgres` locally). Example:

```sql
CREATE DATABASE shopfinity;
```

Update the connection string in **`Shopfinity.API/appsettings.Development.json`** (or use [User Secrets](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets)):

```json
"ConnectionStrings": {
  "DefaultConnection": "Host=localhost;Port=5432;Database=shopfinity;Username=postgres;Password=YOUR_PASSWORD"
}
```

Set **`JwtSettings:Key`** to a **random string at least 32 characters long** (required for signing JWTs).

Apply EF Core migrations:

```bash
dotnet ef database update --project Shopfinity.Infrastructure --startup-project Shopfinity.API
```

> **Search performance:** migrations enable the PostgreSQL `pg_trgm` extension and a GIN index on product names. Ensure your DB user can run `CREATE EXTENSION`.

### 3. Run the API

```bash
dotnet run --project Shopfinity.API --launch-profile http
```

Default HTTP URL (see `launchSettings.json`): **http://localhost:5049**

Swagger/OpenAPI is available in Development at `/swagger` (if enabled in the project).

### 4. Run the web app

```bash
cd shopfinity-web
cp .env.example .env.local
# Edit .env.local — set NEXT_PUBLIC_API_URL to your API base URL (e.g. http://localhost:5049)

npm install
npm run dev
```

Open **http://localhost:3000**.

### 5. Verify builds

```bash
# From repository root
dotnet build
dotnet test

cd shopfinity-web
npm run build
```

## 🔐 Demo accounts (seed data)

After the first run, the seeder creates test users (see `Shopfinity.Infrastructure/Data/DatabaseSeeder.cs`):

| Role | Email | Password |
|------|--------|----------|
| Admin | `admin@shopfinity.com` | `Admin123!` |
| User | `test@shopfinity.com` | `Password123!` |

**Change or remove these in production.** Never ship default passwords.

## 🌍 Environment variables

| Variable | Where | Purpose |
|----------|--------|---------|
| `NEXT_PUBLIC_API_URL` | `shopfinity-web/.env.local` | Public base URL of the API (no trailing slash) |

Backend secrets belong in `appsettings.{Environment}.json`, User Secrets, or your host’s secret store—not in Git.

## 📄 License

See [LICENSE](./LICENSE).

## 🤝 Contributing

Issues and pull requests are welcome. Please run `dotnet test` and `npm run build` in `shopfinity-web` before submitting a change.

---

*Built with Next.js, ASP.NET Core, and PostgreSQL.*
