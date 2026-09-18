# Guía del taller: Medusa JS + Supabase + región Ecuador (USD)

Esta guía es **para ti**. Sígela durante el taller o repítela después en casa.

Si algo no te queda, levanta la mano. Es mejor si no te adelantas al grupo y espera el checkpoint de cada bloque.

**Al terminar vas a tener:**

- Admin de Medusa en http://localhost:9000/app
- Tienda Next.js en http://localhost:8000
- Precios en **dólares**, región **Ecuador** (`/ec`)

---

## Qué vas a construir

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Storefront     │────▶│  Medusa backend  │────▶│  Supabase       │
│  Next.js :8000  │     │  Admin :9000     │     │  Postgres       │
│  (la tienda)    │     │  (productos,     │     │  (solo la DB)   │
└─────────────────┘     │   carrito,       │     └─────────────────┘
                        │   pedidos, auth) │
                        └──────────────────┘
```

Regla de oro:

> **Supabase = Postgres. Medusa = toda la tienda.**  
> No uses Auth, RLS, Realtime ni `supabase-js` en el storefront.  
> Redis no hace falta. Las imágenes quedan en disco local.

Ecuador usa **dólar estadounidense**. En Medusa eso es: región `Ecuador`, país `ec`, moneda `usd`.

Antes de instalar nada, completa [`REQUISITOS-PREVIOS.md`](./REQUISITOS-PREVIOS.md).

---

## Antes de empezar

1. Abre **Cursor** y cambia a **Agent mode** (no Ask).
2. Abre una **carpeta vacía** (File → Open Folder).
3. En la terminal de Cursor, confirma Node 22:

```bash
node -v    # debe ser v22.x  (idealmente >= 22.12)
pnpm -v
```

Si `node -v` dice `v23` (u otra versión que no sea 22):

```bash
nvm use 22
```

Estás listo cuando Cursor está en Agent y `node -v` empieza por `v22`.

---

## Bloque A — Supabase como base de datos

Vas a crear **tu** proyecto de Postgres. No uses el de otra persona: las migraciones se pisan.

### Crear el proyecto

1. Entra a [supabase.com/dashboard](https://supabase.com/dashboard) e inicia sesión.
2. **New project**.
3. Nombre: `medusa-workshop` (o el que quieras).
4. Password de la base: **anótala**. Usa solo letras y números (ej. `MedusaTaller2026`). Si pones `@ # % &`, la URI se rompe.
5. Región: la más cercana (para Ecuador suele ir bien São Paulo / `sa-east-1`, o la que ofrezca el dashboard; AMERICAS).
6. Espera a que el proyecto esté **Healthy**.

### Copiar la URI correcta

1. Botón **Connect** (arriba a la derecha).
2. Tipo: **Session pooler** (NO Transaction, NO Direct).
3. Puerto **5432**.
4. Copia la URI. Se ve así:

```
postgresql://postgres.<PROJECT_REF>:[YOUR-PASSWORD]@aws-0-<REGION>.pooler.supabase.com:5432/postgres
```

5. Reemplaza `[YOUR-PASSWORD]` por la password **real**.

### Qué no copiar

| Conexión           | Puerto                       | ¿Usar?                                    |
| ------------------ | ---------------------------- | ----------------------------------------- |
| Direct             | `db.<ref>.supabase.co:5432`  | No. A menudo es IPv6 y falla en el Wi‑Fi. |
| **Session pooler** | `*.pooler.supabase.com:5432` | **Sí.**                                   |
| Transaction pooler | `*.pooler.supabase.com:6543` | No. Rompe las migraciones de Medusa.      |

**Checkpoint A.** Pausa aquí si no tienes una URI **con password**, puerto `5432` y host `pooler.supabase.com`. No pases al bloque B sin eso.

---

## Bloque B — Instalar Medusa con Cursor

Con la carpeta vacía abierta y Agent mode activo, pega **un** prompt.

Si vas al ritmo del grupo, el facilitador puede pedir los **3 prompts en secuencia** (más abajo). Si estás solo o te adelantaste, usa el prompt largo.

### Prompt largo (todo de una vez)

```
Instala Medusa JS v2 en este workspace, con el starter Next.js (storefront).

Requisitos:
- Usa Node 22 (si hay nvm, nvm use 22). No uses Node 23.
- Usa pnpm.
- Crea el proyecto en una subcarpeta llamada store.
- Incluye el storefront Next.js (--with-nextjs-starter).
- NO instales ni configures Redis. Omite REDIS_URL del .env (no lo dejes vacío: quítalo).
- Salta la conexión a DB en el scaffold (--skip-db) si hace falta. Luego pon DATABASE_URL en apps/backend/.env.

Base de datos (Supabase, solo Postgres):
DATABASE_URL=<PEGAR AQUÍ LA URI DEL SESSION POOLER>

Añade al final de la URI: ?sslmode=no-verify

En medusa-config.ts configura SSL así, porque el certificado de Supabase suele fallar con verify:
databaseDriverOptions: {
  connection: {
    ssl: { rejectUnauthorized: false },
  },
}

CORS del backend:
- STORE_CORS debe incluir http://localhost:8000
- ADMIN_CORS y AUTH_CORS deben incluir http://localhost:9000

Storefront:
- NEXT_PUBLIC_MEDUSA_BACKEND_URL=http://localhost:9000
- NEXT_PUBLIC_BASE_URL=http://localhost:8000
- NEXT_PUBLIC_DEFAULT_REGION=ec   (lo usaremos después; si aún no existe la región, déjalo y lo arreglamos)

Cuando el scaffold termine:
1. Corre: pnpm medusa db:migrate   (desde apps/backend)
2. Crea el admin: pnpm medusa user -e admin@medusajs.com -p supersecret
3. Arranca el backend: pnpm dev   (puerto 9000)
4. Copia la Publishable API Key (Settings → Publishable API Keys en el admin, o de la tabla api_key) a apps/storefront/.env.local como NEXT_PUBLIC_MEDUSA_PUBLISHABLE_KEY
5. Arranca el storefront: pnpm dev   (puerto 8000)

No subas .env a git. No uses Auth ni Storage de Supabase.
```

Sustituye `<PEGAR AQUÍ LA URI DEL SESSION POOLER>` por tu URI **antes** de enviar.

### Prompts partidos (si el grupo va por partes)

**Prompt 1 — scaffold**

```
Crea un proyecto Medusa JS v2 + storefront Next.js en ./store
con create-medusa-app, pnpm, Node 22, --with-nextjs-starter --skip-db --no-browser.
No instales Redis.
```

**Prompt 2 — conectar Supabase**

```
En store/apps/backend/.env deja DATABASE_URL con esta URI de Session pooler
(puerto 5432) y ?sslmode=no-verify:

<PEGAR URI>

Quita REDIS_URL. En medusa-config.ts pon SSL rejectUnauthorized: false.
Luego corre pnpm medusa db:migrate desde apps/backend.
```

**Prompt 3 — admin + storefront**

```
Crea el usuario admin admin@medusajs.com / supersecret.
Arranca el backend en :9000.
Pon la publishable API key en el .env.local del storefront.
Arranca el storefront en :8000.
```

### Qué debes ver

- Admin: http://localhost:9000/app  
  Usuario: `admin@medusajs.com`  
  Contraseña: `supersecret`
- En Supabase → **Table Editor** aparecen muchas tablas (`product`, `region`, `order`, …)
- Productos de prueba: Sweatshirt, T-Shirt, Sweatpants, Shorts

La tienda puede abrir en euros (`/dk`). Eso se corrige en el bloque C.

**Checkpoint B.** Pausa aquí si no puedes entrar al Admin o no ves las tablas `product` y `region` en Supabase. Eso confirma: Medusa escribe, Supabase guarda.

---

## Bloque C — Por qué sale en euros, y cómo dejar USD / Ecuador

El seed de Medusa crea **solo Europa**:

- región `Europe`, moneda `eur`
- países: `dk`, `gb`, `de`, `fr`, …
- el storefront arranca en `dk`
- URL: `http://localhost:8000/dk/store`
- precios: `€10.00`

Los productos **sí tienen precio en USD**, pero el front no lo usa hasta que exista una región en dólares.

En Ecuador el dólar es la moneda oficial. Tienes que:

1. Crear una región **Ecuador** (`ec`) con `usd` **en Medusa** (no basta con cambiar el front).
2. Poner `NEXT_PUBLIC_DEFAULT_REGION=ec` en el storefront.
3. Recargar http://localhost:8000 → debe ir a `/ec`.

### Prompt para Cursor (recomendado)

```
El seed de Medusa dejó la región Europe en EUR y el storefront en dk.
Necesito la tienda en dólares para Ecuador.

1. Crea un script en apps/backend/src/scripts/add-ec-region.ts que:
   - Cree la región "Ecuador" con currency_code "usd" y countries ["ec"]
   - Cree tax region para "ec"
   - Ponga usd como moneda default del store (eur puede quedar como secundaria)
   - Cree service zone + shipping option Standard para Ecuador, precio 10 USD
   - Sea idempotente (si ya existe ec, no dupliques)

2. Corre: pnpm medusa exec ./src/scripts/add-ec-region.ts

3. En apps/storefront/.env.local:
   NEXT_PUBLIC_DEFAULT_REGION=ec

4. En apps/storefront/src/middleware.ts el fallback debe ser "ec", no "dk".

5. Reinicia el storefront. Verifica que http://localhost:8000 redirija a /ec
   y que los precios salgan con $ no con €.
```

### Alternativa sin script (desde el Admin)

1. **Settings → Regions → Create**
   - Name: `Ecuador`
   - Currency: `USD`
   - Countries: `Ecuador`
   - Payment: System default
2. **Settings → Locations** → zona de envío para Ecuador + opción Standard $10
3. **Settings → Store** → default currency `USD`
4. En `apps/storefront/.env.local`: `NEXT_PUBLIC_DEFAULT_REGION=ec`
5. Reinicia el storefront

### Mini-reto

Abre las dos URLs:

- http://localhost:8000/ec/store → dólares
- http://localhost:8000/dk/store → euros

Misma tienda, dos regiones.

Por qué USD y no una moneda “ecuador”: el USD es la moneda legal. `us` vs `ec` solo cambia el **país** (impuestos, envío, URL). La moneda es la misma.

**Checkpoint C.** Pausa aquí si http://localhost:8000/ec/store no muestra `$`. Si solo cambiaste el `.env` y no creaste la región en Medusa, `/ec` va a fallar.

---

## Bloque D — Tu primer producto

1. En el Admin → **Products → Create**
   - Nombre: algo local (`Café de Loja`, `Camiseta Quito`, etc.)
   - Precio USD: `15`
   - Precio EUR: `15` (para que también se vea en `/dk`)
   - Sales channel: Default
   - Inventory: 20
2. Recarga http://localhost:8000/ec/store
3. Agrégalo al carrito y ve a checkout
4. En checkout usa el pago **manual / system** (no Stripe)
5. Vuelve al Admin → **Orders**: el pedido está ahí
6. (Opcional) Supabase → Table Editor → tabla `order`: la fila existe

Eso cierra el círculo: UI Medusa → API Medusa → Postgres Supabase.

**Checkpoint D.** Pausa aquí si tu producto no aparece en la tienda o el pedido no sale en Orders.

---

## Cómo levantarlo mañana

En **dos terminales**, Node 22:

```bash
nvm use 22

# terminal 1
cd store/apps/backend
pnpm dev

# terminal 2
cd store/apps/storefront
pnpm dev
```

O desde `store/`:

```bash
nvm use 22
pnpm dev
```

| Qué            | URL                            |
| -------------- | ------------------------------ |
| Admin          | http://localhost:9000/app      |
| Tienda Ecuador | http://localhost:8000/ec/store |
| Health         | http://localhost:9000/health   |

Al reiniciar el backend, el Admin puede pedirte login otra vez. Es normal: no hay Redis y la sesión vive en memoria.

No subas archivos `.env` a git.

---

## Si algo falla

| Si ves esto                                 | Causa                                             | Qué hacer                                                                          |
| ------------------------------------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `create-medusa-app` o el backend no arranca | Node 23                                           | `nvm use 22`                                                                       |
| `self-signed certificate` / error SSL       | verify estricto contra el pooler                  | `?sslmode=no-verify` en la URI y `rejectUnauthorized: false` en `medusa-config.ts` |
| migrate se cuelga o falla raro              | Transaction pooler `:6543`                        | URI Session pooler `:5432`                                                         |
| `ECONNREFUSED` / no conecta                 | URI Direct IPv6, o password con `[YOUR-PASSWORD]` | Session pooler + password real                                                     |
| Storefront: Missing publishable key         | `.env.local` sin `pk_...`                         | Admin → Settings → Publishable API Keys                                            |
| Precios en euros                            | región default `dk`                               | región `ec` + `NEXT_PUBLIC_DEFAULT_REGION=ec`                                      |
| `/ec` 404 o región vacía                    | solo cambiaste el front                           | **crea** la región en Medusa (bloque C)                                            |
| Admin pide login otra vez al reiniciar      | no hay Redis, sesión en RAM                       | vuelve a entrar (`admin@medusajs.com` / `supersecret`)                             |
| Contraseña de DB con `@`                    | URI mal parseada                                  | password simple, o URL-encode (`@` → `%40`)                                        |

---

## Qué no hagas hoy

Déjalo fuera para no perder el hilo:

- Redis
- Stripe
- Supabase Auth / RLS / Realtime
- Storage S3 de Supabase (el default `file-local` basta)
- Deploy a Vercel / Medusa Cloud
- Un schema custom `medusa` (en un proyecto de taller, `public` está bien)

Si terminas antes: crea un segundo producto o explora **Orders** y **Customers** en el Admin.
