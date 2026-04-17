# Plan Técnico: Visual SC — Sistema de Pedidos en Kiosco

## TL;DR

Sistema de pedidos en kiosco para Rodson Coffee que permite a los clientes construir órdenes de forma autónoma, y recibir número de orden. El plan divide el desarrollo en **backend** (API REST con Node.js + Express + MongoDB) y **frontend** (React + TypeScript + TailwindCSS), con fases incrementales que permiten validación temprana.

---

## Stack Tecnológico

| Capa | Tecnología |
|------|------------|
| **Backend** | Node.js 20 LTS + Express.js (MVC), MongoDB Atlas, Mongoose |
| **Frontend** | React 18 + Vite, TypeScript strict, TailwindCSS, React Router, Zustand (carrito) |

---

## FASE 2: API Backend — Core

### Endpoints de Productos (B2-Products)

| # | Tarea | Endpoint | Descripción |
|---|-------|----------|-------------|
| B2.1 | Listar productos | `GET /api/products` | Retorna catálogo completo con categorías |
| B2.2 | Producto por ID | `GET /api/products/:id` | Detalle de producto con personalizaciones disponibles |
| B2.3 | Productos por categoría | `GET /api/products/category/:slug` | Filtrar por las 7 categorías |
| B2.4 | Verificar disponibilidad | `GET /api/products/:id/availability` | Check de stock/disponibilidad |

**Modelo Product**:
```javascript
{
  id,
  nombre,
  categoria,
  precio,
  descripcion,
  notas_adicionales,
}
```

### Endpoints de Pedidos (B2-Orders)

| # | Tarea | Endpoint | Descripción |
|---|-------|----------|-------------|
| B2.5 | Crear pedido | `POST /api/orders` | Valida items, calcula total, asigna número |
| B2.6 | Obtener pedido | `GET /api/orders/:orderNumber` | Detalle de pedido por número |
| B2.7 | Pedidos activos | `GET /api/orders/active` | Lista para pantalla de tracking |
| B2.8 | Actualizar estado | `PATCH /api/orders/:orderNumber/status` | Cambiar estado (uso admin) |

**Modelo Order**:

```javascript
{
  _id, orderNumber, 
  items: [
    { 
      product,
      quantity,
      unitPrice,
      subtotal 
    }
    ],
  total, // sunma de los subtotales
  createdAt
}
```

### Lógica de Negocio (B2-Logic)

| # | Tarea | Descripción |
|---|-------|-------------|
| B2.11 | Calculadora de precios | Función que calcula precio final con personalizaciones |
| B2.12 | Generador de número de orden | Secuencial, con posible reinicio diario |
| B2.13 | Rate limiting | Implementar límites: 100 req/min por kiosco |

**Dependencias**: B1.3 → B2.1-B2.4 (productos), B1.3 → B2.5-B2.8 (pedidos). B2.11-B2.13 son funciones helper que se usan en B2.5.

---

## FASE 3: Frontend — Interfaz de Kiosco

### Páginas Principales (F3-Screens)

| # | Tarea | Ruta (React Router) | Descripción |
|---|-------|---------------------|-------------|
| F3.1 | Home de pedido | `/pages/Home` | Componente de página con aside de categorías + grilla de productos + aside de orden |

### Componentes de Negocio (F3-Components)

| # | Tarea | Componente | Descripción |
|---|-------|------------|-------------|
| F3.8 | ProductCard | `components/ProductCard` | Card de producto con imagen, nombre, precio |
| F3.9 | CartItem | `components/CartItem` | Línea de item en carrito |
| F3.10 | CategoriesAside | `components/CategoriesAside` | Aside con las categorías del menú para filtrar productos |
| F3.11 | ProductsByCategoryGrid | `components/ProductsByCategoryGrid` | Grilla de productos filtrada por categoría seleccionada, contiene la grilla de productos con `ProductCard`|
| F3.12 | OrderAside | `components/OrderAside` | Aside de orden dentro de Home con resumen de items, edición y total |
| F3.13 | OrderConfirmationFloating | `components/OrderConfirmationFloating` | Interfaz flotante de confirmación de orden dentro de Home (sin cambio de ruta) |

### Layouts (F3-Layouts)

| # | Tarea | Componente | Descripción |
|---|-------|------------|-------------|
| F3.19 | Header | `layouts/Header` | Logo, navegación y `CartFloating` integrado dentro del Header |
| F3.20 | Footer | `layouts/Footer` | Footer con Redes sociales (instagram), Whatsapp, horario, CTA al final y créditos |

### Estado y Hooks (F3-State)

| # | Tarea | Descripción |
|---|-------|-------------|
| F3.21 | useCart hook | Zustand store: items, add, remove, update, clear, total |
| F3.22 | useProducts hook | Fetch de productos con React Query (TanStack Query) |
| F3.23 | useOrder hook | Crear orden, obtener estado |

**Dependencias**: F3.1 depende de B2.1 y B2.3 para cargar catálogo y filtrar por categoría, e integra los componentes F3.12 (OrderAside) y F3.13 (OrderConfirmationFloating). F3.12 requiere F3.21. La página F3.3 se renderiza tras confirmar orden desde Home.

---

## FASE 4: Integración y Flujo Completo

### Integración API (I4)

| # | Tarea | Descripción |
|---|-------|-------------|
| I4.1 | Conectar catálogo | Frontend consume `GET /api/products` |
| I4.2 | Conectar personalizaciones | Cargar opciones dinámicas desde API |
| I4.3 | Crear pedido | Frontend envía carrito a `POST /api/orders` |
| I4.4 | Mostrar número de orden | Recibir y mostrar `orderNumber` de respuesta |

---

## Archivos Relevantes

### Backend (MVC)

```
src/
├── app.js                  # Entry point, configuración Express
├── config/
│   └── db.js               # Conexión a MongoDB
├── models/
│   ├── Product.js          # Schema de productos con Mongoose
│   ├── Order.js            # Schema de pedidos
├── controllers/
│   ├── productController.js    # Lógica de productos
│   ├── orderController.js      # Lógica de pedidos
├── routes/
│   ├── productRoutes.js    # Rutas de productos
│   ├── orderRoutes.js      # Rutas de pedidos
├── middlewares/
│   └── errorHandler.js     # Manejo centralizado de errores
└── utils/
    ├── priceCalculator.js  # Cálculo de precios con personalizaciones
    └── orderNumberGenerator.js  # Generador de número de orden
```

### Frontend (React + Vite)

```
src/
├── main.tsx                            # Entry point
├── App.tsx                             # Router principal que monta componentes de página
├── stores/
│   └── cartStore.ts                    # Zustand store del carrito
├── hooks/
│   ├── useProducts.ts                  # React Query para productos
│   ├── useOrder.ts                     # Crear y obtener órdenes
├── services/
│   └── api.ts                          # Cliente API con axios/fetch
├── layouts/
│   ├── Header.tsx
│   └── Footer.tsx
├── pages/
│   ├── Home.tsx
├── components/
│   ├── ProductCard.tsx
│   ├── CartItem.tsx
│   ├── CategoriesAside.tsx
│   ├── ProductsByCategoryGrid.tsx
│   ├── OrderAside.tsx
│   ├── OrderConfirmationFloating.tsx
│   ├── EspressoCustomizer.tsx
│   ├── FilteredCoffeeCustomizer.tsx
│   └── ProductDetail.tsx
│   └── OrderCreated.tsx
```

---

## Verificación

| # | Caso de Prueba | Resultado Esperado |
|---|----------------|---------------------|
| 1 | Navegar categorías desde el aside de Home | Ver actualización de la grilla con productos y precios correctos |
| 2 | Latte + leche vegetal + helado | $8,500 + $5,500 + $4,500 = **$18,500** |
| 3 | Chemex 2 tazas Natural | $18,000 + $3,000 = **$21,000** |
| 4 | Agregar 3 productos, modificar, eliminar uno | Total recalculado correctamente |
| 5 | Confirmar pedido desde Home | Mostrar interfaz flotante de confirmación sin salir de Home y número de orden persistido en BD |

---

## Decisiones de Scope

### Incluido en MVP

- Sistema de kiosco para pedidos en local
- 7 categorías de productos con personalizaciones
- Orden con calculo total de la orden
  - Generación de número de orden
- API REST para catálogo y pedidos

### Excluido (fases posteriores)

- Pasarela de pagos (pago en caja por ahora)
- Pantalla de tracking para clientes
- Asignación de ordenes por mesa
- Panel de administración completo
- Sistema de inventario/stock
- Notificaciones push
- Modo "para llevar"

