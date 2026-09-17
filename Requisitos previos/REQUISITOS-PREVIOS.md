# Requisitos previos al taller

Lee esto **antes** del taller e instala todo en tu máquina. El día del evento no habrá tiempo para montar el entorno desde cero.

Vamos a instalar [Medusa JS](https://docs.medusajs.com/learn/installation) (backend + Admin + storefront Next.js) y a usar [Supabase](https://supabase.com) como PostgreSQL. El código lo escribiremos con [Cursor](https://cursor.com).

Cuando termines, marca el [checklist final](#checklist-para-el-día-del-taller) y trae la laptop con todo verificado.

---

## 1. Requisitos oficiales de Medusa

Fuente: [docs.medusajs.com/learn/installation](https://docs.medusajs.com/learn/installation)

Medusa **v2** (la versión del taller, `2.21`) pide esto:

| Requisito              | Qué pide Medusa                                                                   | Qué usaremos en el taller                                              |
| ---------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Node.js**            | LTS `v20.19.0+` **o** `v22.12.0+`. El storefront Next.js **no soporta Node 25+**. | **Node 22 LTS** (recomendado). **No uses Node 23.**                    |
| **Git**                | CLI de Git instalada                                                              | Git                                                                    |
| **PostgreSQL**         | Postgres instalado y corriendo                                                    | **Supabase** (Postgres en la nube). No instales Postgres en tu laptop. |
| **Gestor de paquetes** | npm, yarn o pnpm. Recomiendan pnpm o yarn.                                        | **pnpm**                                                               |

Opcional según Medusa, **no lo necesitamos** para este taller:

| Pieza              | ¿Obligatorio en Medusa?                  | ¿En el taller?                    |
| ------------------ | ---------------------------------------- | --------------------------------- |
| **Redis**          | No. En local las sesiones van a memoria. | No lo instales.                   |
| **Docker**         | No                                       | No                                |
| **Stripe / pagos** | No                                       | Usaremos el pago manual de Medusa |

Hardware mínimo razonable: **8 GB de RAM**, ~5 GB libres en disco, puertos **8000** y **9000** libres, conexión a internet (Supabase y `create-medusa-app` salen a la red).

Sistemas soportados: **macOS, Windows 10/11, Linux**.

---

## 2. Programas que debes instalar

### 2.1 Cursor (obligatorio)

Es el editor que usaremos. El taller corre en **Agent mode**.

1. Descarga: [https://cursor.com](https://cursor.com)
2. Instálalo y ábrelo
3. Crea o inicia sesión (cuenta **gratis** alcanza para el taller)
4. Confirma que puedes cambiar a **Agent** (no solo Ask / Chat)

Sin cuenta de Cursor no vas a poder seguir el flujo del taller.

### 2.2 Git

Medusa lo exige. Cursor también lo usa.

- **macOS:** `xcode-select --install` o [git-scm.com](https://git-scm.com)
- **Windows:** [Git for Windows](https://git-scm.com/download/win) (marca la opción de Git Bash)
- **Linux:** `sudo apt install git` (Debian/Ubuntu) o el paquete `git` de tu distro

Comprueba:

```bash
git --version
```

### 2.3 Node.js 22 LTS (obligatorio)

Medusa 2.21 declara:

```text
"engines": { "node": "^20.19.0 || >=22.12.0" }
```

En este taller usamos **Node 22**. Evita:

- Node **23** (no es LTS y en la práctica rompe el scaffold)
- Node **18** o menor
- Node **25+** (el storefront Next.js no lo soporta)

La forma más simple es **nvm** (macOS/Linux) o **nvm-windows** / **fnm** (Windows).

**macOS / Linux**

```bash
# instalar nvm: https://github.com/nvm-sh/nvm
nvm install 22
nvm use 22
nvm alias default 22
node -v    # debe verse v22.x.x  (idealmente >= 22.12)
```

**Windows**

1. Instala [nvm-windows](https://github.com/coreybutler/nvm-windows) o [fnm](https://github.com/Schniz/fnm)
2. En PowerShell o Git Bash:

```bash
nvm install 22
nvm use 22
node -v
```

Si ya tienes Node y `node -v` muestra `v23` o `v25`, **cámbialo a 22 antes del taller**.

### 2.4 pnpm (obligatorio)

El starter de Medusa que usamos declara `packageManager: pnpm@10`.

Con Node 22 ya instalado:

```bash
corepack enable
corepack prepare pnpm@10.11.1 --activate
pnpm -v    # debe verse 10.x
```

Si `corepack` no existe:

```bash
npm install -g pnpm@10
pnpm -v
```

### 2.5 Navegador (obligatorio)

Chrome, Edge, Firefox o Safari reciente. Lo usaremos para:

- Admin de Medusa → `http://localhost:9000/app`
- Storefront → `http://localhost:8000`
- Dashboard de Supabase

---

## 3. Cuentas que debes crear

Créalas **antes** del taller, con un correo al que tengas acceso ese día.

| Cuenta       | Para qué                                         | Plan | Link                                           |
| ------------ | ------------------------------------------------ | ---- | ---------------------------------------------- |
| **Cursor**   | Agent mode, instalar Medusa desde el chat        | Free | [cursor.com](https://cursor.com)               |
| **Supabase** | PostgreSQL de Medusa                             | Free | [supabase.com](https://supabase.com)           |
| **GitHub**   | Clonar este repo y (si aplica) autenticar Cursor | Free | [github.com/signup](https://github.com/signup) |

Notas:

- **Supabase:** solo usaremos Postgres. No hace falta configurar Auth, Storage ni RLS antes del taller. El **proyecto de base de datos lo creamos juntos** el día del evento (así no se duerme el proyecto free). Sí debes tener la **cuenta ya creada y verificada por email**.
- **GitHub:** necesaria si vas a clonar este repositorio. Si Cursor te pide login con GitHub, hazlo de antemano.
- **No hace falta:** cuenta de Stripe, Medusa Cloud, Vercel, Docker Hub, Redis Cloud.

---

## 4. Qué no instales

Para no perder tiempo ni pelearte con servicios de más:

- PostgreSQL local (usamos Supabase)
- Redis
- Docker / Docker Desktop
- VS Code (el taller es en Cursor; si ya lo tienes, no hay problema, pero no lo usaremos)
- Yarn o npm como gestor principal (usa **pnpm**)

---

## 5. Cómo comprobar que estás listo

Abre la **terminal** (en macOS: Terminal; en Windows: Git Bash o PowerShell) y corre:

```bash
node -v
pnpm -v
git --version
```

Resultado esperado:

```text
v22.12.0     # o cualquier 22.x >= 22.12  (NO v23, NO v18)
10.x.x       # pnpm 10
git version 2.x
```

Prueba extra (opcional): confirma que Cursor abre y que ya iniciaste sesión.

Si algo falla, arréglalo **antes** del taller. El día del evento asumimos que este bloque ya está verde.

---

## 6. Checklist para el día del taller

Imprime esto o márcalo en GitHub.

- [ ] Laptop cargada + cargador
- [ ] Internet (el venue tiene Wi‑Fi; Medusa y Supabase necesitan red)
- [ ] [ ] Cursor instalado y con sesión iniciada
- [ ] Puedo usar **Agent mode** en Cursor
- [ ] Git instalado (`git --version`)
- [ ] Node **22** (`node -v` empieza por `v22`)
- [ ] pnpm 10 (`pnpm -v`)
- [ ] Cuenta de **Supabase** creada y email verificado
- [ ] Cuenta de **GitHub** creada
- [ ] Navegador actualizado
- [ ] Puertos 8000 y 9000 no ocupados por otra app
- [ ] Este repo clonado **o** una carpeta vacía lista para abrir en Cursor

Clonar este repositorio:

```bash
git clone <URL-DE-ESTE-REPO>
cd <nombre-del-repo>
```

Luego abre esa carpeta en Cursor: **File → Open Folder**.

---

## 7. Dudas frecuentes

**¿Puedo usar Node 20?**  
Sí, si es `v20.19.0` o superior. En el taller unificamos en **22** para no depurar dos versiones.

**¿Puedo usar Node 23 o 24?**  
No uses 23. Node 24 LTS lo menciona Medusa, pero el entorno que preparamos y probamos es **22**. Trae 22.

**¿Tengo que instalar Postgres con Homebrew / instalador de Windows?**  
No. Postgres vive en tu proyecto de Supabase.

**¿Y si no tengo cuenta de pago en Cursor?**  
El plan free alcanza para seguir el taller. Y adicional tendras créditos para ograrlo

**¿Windows funciona?**  
Sí. Instala Git for Windows + Node 22 + pnpm + Cursor. El día del taller usa Agent mode igual que en Mac.

**¿Llego sin esto instalado?**  
Vas a perder la primera hora. Completa esta lista en casa.

---

## Referencias

- Instalación de Medusa: [docs.medusajs.com/learn/installation](https://docs.medusajs.com/learn/installation)
- Node: [nodejs.org](https://nodejs.org) (elige **22 LTS**)
- nvm (macOS/Linux): [github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)
- nvm-windows: [github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows)
- pnpm: [pnpm.io](https://pnpm.io)
- Cursor: [cursor.com](https://cursor.com)
- Supabase: [supabase.com](https://supabase.com)
- Git: [git-scm.com](https://git-scm.com)
