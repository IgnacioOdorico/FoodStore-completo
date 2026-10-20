<div align="center">

# 🍔 FoodStore

### Plataforma full‑stack de pedidos de comida online

Catálogo, carrito, pagos con **MercadoPago**, seguimiento de pedidos **en tiempo real** y un panel de administración con métricas de negocio.

<br/>

[![Backend](https://img.shields.io/badge/Backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://github.com/IgnacioOdorico/FoodStore-BACKEND)
[![Store](https://img.shields.io/badge/Store-React_19-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://github.com/IgnacioOdorico/FoodStore-FRONTEND-STORE)
[![Admin](https://img.shields.io/badge/Admin-React_19-8B5CF6?style=for-the-badge&logo=react&logoColor=white)](https://github.com/IgnacioOdorico/FoodStore-FRONTEND-ADMIN)
[![Demo](https://img.shields.io/badge/Demo-YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/watch?v=IghWtxwE6Ts)

<br/>

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-6-3178C6?logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-4-06B6D4?logo=tailwindcss&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)
![MercadoPago](https://img.shields.io/badge/MercadoPago-Checkout_PRO-00B1EA?logo=mercadopago&logoColor=white)
![Cloudinary](https://img.shields.io/badge/Cloudinary-Images-3448C5?logo=cloudinary&logoColor=white)

</div>

---

## 📑 Contenido

- [¿Qué es FoodStore?](#-qué-es-foodstore)
- [Los 3 repositorios](#-los-3-repositorios)
- [Arquitectura general](#️-arquitectura-general)
- [Funcionalidades](#-funcionalidades)
- [Flujo de un pedido](#-flujo-de-un-pedido)
- [Roles y permisos](#-roles-y-permisos)
- [Stack tecnológico](#-stack-tecnológico)
- [Cómo levantar todo el proyecto](#-cómo-levantar-todo-el-proyecto)
- [Demo en video](#-demo-en-video)

---

## 🧭 ¿Qué es FoodStore?

**FoodStore** es un sistema completo de venta de comida online, dividido en tres aplicaciones que trabajan juntas:

- 🛒 Una **tienda** donde los clientes navegan el catálogo, arman su carrito, pagan con MercadoPago y siguen el estado de su pedido en vivo.
- 🧑‍💼 Un **panel de administración** donde el equipo gestiona productos, categorías, ingredientes, stock, usuarios y pedidos, con un dashboard de ventas.
- ⚙️ Una **API REST + WebSocket** que concentra toda la lógica de negocio, la seguridad y las integraciones externas.

---

## 📦 Los 3 repositorios

<table>
  <tr>
    <td align="center" width="33%">
      <h3>⚙️ Backend</h3>
      <p>API REST + WebSocket con FastAPI, PostgreSQL, JWT + RBAC, MercadoPago y Cloudinary.</p>
      <a href="https://github.com/IgnacioOdorico/FoodStore-BACKEND">
        <img src="https://img.shields.io/badge/Ver_repositorio-→-009688?style=for-the-badge&logo=github" alt="Backend repo"/>
      </a>
    </td>
    <td align="center" width="33%">
      <h3>🛒 Frontend Store</h3>
      <p>La tienda para clientes: catálogo, carrito persistente, checkout y seguimiento de pedidos.</p>
      <a href="https://github.com/IgnacioOdorico/FoodStore-FRONTEND-STORE">
        <img src="https://img.shields.io/badge/Ver_repositorio-→-61DAFB?style=for-the-badge&logo=github&logoColor=black" alt="Store repo"/>
      </a>
    </td>
    <td align="center" width="33%">
      <h3>🧑‍💼 Frontend Admin</h3>
      <p>Panel de gestión: CRUDs, stock, pedidos en vivo, usuarios y dashboard con métricas.</p>
      <a href="https://github.com/IgnacioOdorico/FoodStore-FRONTEND-ADMIN">
        <img src="https://img.shields.io/badge/Ver_repositorio-→-8B5CF6?style=for-the-badge&logo=github" alt="Admin repo"/>
      </a>
    </td>
  </tr>
</table>

---

## 🏗️ Arquitectura general

```mermaid
flowchart LR
    subgraph Clientes
        S["🛒 Frontend Store<br/>React + Vite"]
        A["🧑‍💼 Frontend Admin<br/>React + Vite"]
    end

    subgraph API["⚙️ Backend · FastAPI"]
        R[Routers] --> SV[Services]
        SV --> U[Unit of Work]
        U --> RP[Repositories]
        RP --> M[SQLModel]
        SV -. eventos post-commit .-> WS[WebSocket Manager]
    end

    S -- "REST /api/v1 (JWT cookie)" --> R
    A -- "REST /api/v1 (JWT cookie)" --> R
    WS -- "ws /ws/pedidos" --> S
    WS -- "ws /ws/pedidos" --> A

    M --> DB[("🐘 PostgreSQL 15")]
    SV --> MP["💳 MercadoPago<br/>Checkout PRO"]
    MP -- webhook IPN --> R
    SV --> CL["🖼️ Cloudinary"]
```

El backend sigue una **arquitectura en capas** (`Router → Service → UoW → Repository → Model`): los routers sólo hablan HTTP, la lógica de negocio vive en servicios stateless, y el **Unit of Work** garantiza transacciones atómicas. Los frontends están organizados **por features** (dominio), con estado de servidor en TanStack Query y estado de cliente en Zustand.

---

## ✨ Funcionalidades

<table>
<tr>
<td valign="top" width="50%">

### 🛒 Tienda (clientes)

- 🔐 Registro, login y perfil de usuario
- 🍕 Catálogo con búsqueda, filtros por categoría y detalle de producto
- 🥜 Ingredientes y **alérgenos** visibles en cada producto
- 🧺 Carrito **persistente** (sobrevive a recargas)
- 💳 Pago con **MercadoPago Checkout PRO**
- 📍 Direcciones de entrega con dirección principal
- 📦 Historial de pedidos con cancelación (mientras esté pendiente o confirmado)
- ⚡ Estado del pedido actualizado **en tiempo real**

</td>
<td valign="top" width="50%">

### 🧑‍💼 Panel Admin (equipo)

- 📊 **Dashboard** con ventas, ingresos, top productos y pedidos por estado
- 🗂️ Categorías **jerárquicas** con imagen en Cloudinary
- 🍔 CRUD de productos, ingredientes y stock
- 🚚 Gestión de pedidos con **feed en vivo** vía WebSocket
- 👥 Administración de usuarios y roles
- 🛡️ Rutas protegidas según rol (ADMIN, STOCK, PEDIDOS)
- 🔔 Notificaciones toast y tablas con filtros

</td>
</tr>
</table>

### 🔒 Seguridad y calidad (backend)

| | |
|---|---|
| 🔑 **Autenticación** | JWT con access + refresh token en cookie |
| 🛡️ **Autorización** | RBAC con 4 roles |
| 🧂 **Contraseñas** | Hash con bcrypt (cost ≥ 12) |
| 🚦 **Rate limiting** | 5 intentos de login cada 15 minutos |
| ⚠️ **Errores** | Respuestas estandarizadas según RFC 7807 |
| 🧾 **Auditoría** | Historial de cambios de estado de cada pedido |
| 🧪 **Tests** | Pytest unitarios e integración |

---

## 🔄 Flujo de un pedido

Los pedidos se gobiernan con una **máquina de estados (FSM)**: sólo se permiten transiciones válidas y cada cambio queda registrado y se notifica por WebSocket.

```mermaid
stateDiagram-v2
    [*] --> PENDIENTE: Cliente confirma el carrito
    PENDIENTE --> CONFIRMADO: Pago aprobado (webhook MP)
    PENDIENTE --> CANCELADO: Pago rechazado o cancelación
    CONFIRMADO --> EN_PREP: Cocina empieza
    CONFIRMADO --> CANCELADO: Cancelación
    EN_PREP --> ENTREGADO: Pedido entregado
    EN_PREP --> CANCELADO: Cancelación (staff)
    ENTREGADO --> [*]
    CANCELADO --> [*]
```

```mermaid
sequenceDiagram
    actor C as Cliente
    participant S as Store
    participant B as Backend
    participant MP as MercadoPago
    participant A as Admin

    C->>S: Confirma carrito
    S->>B: POST /pedidos
    B-->>S: Pedido PENDIENTE + link de pago
    S->>MP: Redirige a Checkout PRO
    MP->>B: Webhook IPN (pago aprobado)
    B->>B: PENDIENTE → CONFIRMADO
    B-->>A: WS: nuevo pedido confirmado
    B-->>S: WS: estado actualizado
    A->>B: Avanza a EN_PREP / ENTREGADO
    B-->>S: WS: el cliente ve el cambio en vivo
```

---

## 👥 Roles y permisos

| Rol | Código | Acceso |
|---|---|---|
| 👑 Administrador | `ADMIN` | Todo el sistema: dashboard, usuarios, catálogo y pedidos |
| 📦 Gestor de Stock | `STOCK` | Productos, categorías, ingredientes y stock |
| 🚚 Gestor de Pedidos | `PEDIDOS` | Ver y avanzar estados de pedidos |
| 🙋 Cliente | `CLIENT` | Tienda: catálogo, carrito y sus propios pedidos |

---

## 🧰 Stack tecnológico

<table>
<tr>
<th>⚙️ Backend</th>
<th>🎨 Frontends</th>
<th>☁️ Infra e integraciones</th>
</tr>
<tr>
<td valign="top">

- Python 3.10+
- FastAPI (REST + WebSocket + OpenAPI)
- SQLModel + Pydantic
- PostgreSQL 15
- Alembic (migraciones)
- python-jose (JWT) + Passlib/bcrypt
- Pytest

</td>
<td valign="top">

- React 19 + TypeScript
- Vite 8
- Tailwind CSS 4
- React Router 7
- TanStack Query / Form / Table
- Zustand
- Axios con interceptores
- Recharts + Tremor (dashboard)
- Sonner + Lucide

</td>
<td valign="top">

- Docker Compose
- ngrok (webhooks en desarrollo)
- MercadoPago Checkout PRO
- Cloudinary (imágenes)

</td>
</tr>
</table>

---

## 🚀 Cómo levantar todo el proyecto

> Requisitos: **Python 3.10+**, **Node.js 20+**, **pnpm 9+** (o npm) y **Docker**.

### 1️⃣ Clonar los tres repositorios

```bash
git clone https://github.com/IgnacioOdorico/FoodStore-BACKEND.git
git clone https://github.com/IgnacioOdorico/FoodStore-FRONTEND-STORE.git
git clone https://github.com/IgnacioOdorico/FoodStore-FRONTEND-ADMIN.git
```

### 2️⃣ Backend

```bash
cd FoodStore-BACKEND
cp .env.example .env          # completar credenciales (Postgres, JWT, MercadoPago, Cloudinary, ngrok)
docker-compose up -d          # PostgreSQL + ngrok
python -m venv .venv
.venv\Scripts\activate        # Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
python -m app.db.seed         # roles, estados, formas de pago y usuario admin
uvicorn app.main:app --reload
```

📘 Documentación interactiva en **http://localhost:8000/docs**

### 3️⃣ Frontend Store

```bash
cd FoodStore-FRONTEND-STORE
pnpm install
cp .env.example .env          # VITE_API_URL=http://localhost:8000/api/v1
pnpm dev
```

### 4️⃣ Frontend Admin

```bash
cd FoodStore-FRONTEND-ADMIN
pnpm install
cp .env.example .env
pnpm dev
```

> 💡 Vite toma el siguiente puerto libre si `5173` ya está ocupado, así que podés correr ambos frontends a la vez.

Cada repositorio tiene su propio README con el detalle completo de configuración, estructura de carpetas y variables de entorno.

---

## 🎬 Demo en video

<div align="center">

[![Ver demo en YouTube](https://img.youtube.com/vi/IghWtxwE6Ts/hqdefault.jpg)](https://www.youtube.com/watch?v=IghWtxwE6Ts)

▶️ [Demo principal](https://www.youtube.com/watch?v=IghWtxwE6Ts) · ➕ [Video complementario](https://www.youtube.com/watch?v=xzheMmXe0Uc)

</div>

---

<div align="center">

Hecho con ☕ y 🍔 por **[Ignacio Odorico](https://github.com/IgnacioOdorico)**

</div>
