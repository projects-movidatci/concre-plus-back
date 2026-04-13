# API Concreto — API y base de datos

API **Node.js + Express** para el sistema de venta de concreto: **PostgreSQL** (`pg`), validación con **Zod**, **helmet**, **cors**, **express-rate-limit**, logging con **pino**.

La aplicación web (frontend) está en el repositorio **demo**; allí se documenta solo el cliente. Esta guía cubre **servidor y base de datos**.

## Requisitos

- **Node.js** 18 o superior (recomendado LTS)
- **PostgreSQL** 14+ (local o en la nube)

## Instalación

```bash
npm install
```

## Variables de entorno

```bash
copy .env.example .env
```

PowerShell:

```powershell
Copy-Item .env.example .env
```

| Variable | Descripción |
|----------|-------------|
| `NODE_ENV` | `development`, `test` o `production`. |
| `PORT` | Puerto HTTP (por defecto **3000**). |
| `DATABASE_URL` | URL PostgreSQL: `postgresql://usuario:password@host:5432/base`. |
| `DB_SSL` | `true` en casi todos los PostgreSQL en la nube; `false` en local típico. |
| `JWT_SECRET` | Secreto para JWT (**mínimo 8 caracteres**); en producción, largo y aleatorio. |
| `JWT_EXPIRES_IN` | Caducidad del token (p. ej. `8h`, `7d`). |

## Comandos

| Comando | Descripción |
|---------|-------------|
| `npm run dev` | Desarrollo con **nodemon**. |
| `npm start` | Producción: `node src/server.js`. |
| `npm test` | Placeholder (sin pruebas aún). |

Servidor por defecto: `http://localhost:3000`.

---

## Base de datos: esquema y producción

### Esquema principal

El esquema multi-tenant completo vive en el repo **demo**: archivo **`database/schema.sql`**. Aplícalo primero sobre una base **vacía**:

```bash
psql "postgresql://USUARIO:PASSWORD@HOST:5432/NOMBRE_BD" -f ruta/al/demo/database/schema.sql
```

### Scripts en este repositorio

En **`database/`** hay migraciones o parches (por ejemplo `001_quotations.sql`). Ejecútalos después del esquema principal y respetando el orden de dependencias entre tablas.

Windows (cmd), con `DATABASE_URL` en el entorno:

```bash
psql "%DATABASE_URL%" -f database/001_quotations.sql
```

Linux / macOS:

```bash
psql "$DATABASE_URL" -f database/001_quotations.sql
```

### Checklist producción

1. Crear la base en el proveedor y obtener `DATABASE_URL`.
2. `DB_SSL=true` si el proveedor exige TLS.
3. Ejecutar `schema.sql` del repo **demo** y luego los `.sql` de **`database/`** de este repo.
4. `JWT_SECRET` fuerte y `NODE_ENV=production` en el entorno del servidor (no commitear `.env` real).

### Scripts opcionales

En **`scripts/`** hay semillas de prueba que usan `DATABASE_URL`; no ejecutar en producción sin revisar.

---

## Endpoints de referencia

- `GET /` — Estado general
- `GET /health` — API y conexión a PostgreSQL

---

## Cliente (frontend)

En el repo **demo**, copia `.env.example` a `.env` y define `VITE_APP_API_PREFIX` con la URL pública de esta API (en local, p. ej. `http://localhost:3000`). Detalle en el **README del frontend** en la raíz de **demo**.
