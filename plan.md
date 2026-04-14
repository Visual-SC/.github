# Plan Técnico: Visual SC — Sistema de Pedidos en Kiosco

## TL;DR

Sistema de pedidos en kiosco para Rodson Coffee que permite a los clientes construir órdenes de forma autónoma, personalizar productos según categoría (espresso, filtrados, etc.), asignar mesa y recibir número de orden. El plan divide el desarrollo en **backend** (API REST con Node.js + Express + MongoDB) y **frontend** (React + TypeScript + TailwindCSS), con fases incrementales que permiten validación temprana.

---

## Stack Tecnológico

| Capa | Tecnología |
|------|------------|
| **Backend** | Node.js 20 LTS + Express.js (MVC), MongoDB Atlas, Mongoose |
| **Frontend** | React 18 + Vite, TypeScript strict, TailwindCSS, React Router, Zustand (carrito) |

---

## FASE 1: Setup e Infraestructura

### Backend (B1)

| # | Tarea | Descripción | Prioridad |
|---|-------|-------------|-----------|
| B1.1 | Inicializar proyecto backend | Crear estructura MVC con Express.js, ESLint, Prettier | Alta |
| B1.2 | Configurar MongoDB | Conexión a MongoDB Atlas, variables de entorno, health check | Alta |
| B1.3 | Definir schemas Mongoose | Schemas para: Product, Category, Order, Table, Customization | Alta |
| B1.4 | Crear seeders | Scripts para poblar BD con productos del catálogo de referencia | Media |

### Frontend (F1)

| # | Tarea | Descripción | Prioridad |
|---|-------|-------------|-----------|
| F1.1 | Inicializar proyecto React | React 18 con Vite, TypeScript, TailwindCSS, estructura de carpetas | Alta |
| F1.2 | Configurar alias y paths | Alias `@/` para imports, configurar tsconfig | Alta |
| F1.3 | Crear design system base | Colores, tipografías, componentes UI base (Button, Card, Input) | Alta |
| F1.4 | Configurar estado global | Zustand para carrito + React Router para navegación | Media |

**Dependencias**: F1.1 → F1.2 → F1.3. B1.1 → B1.2 → B1.3. Frontend y backend pueden desarrollarse en paralelo.

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
  _id, name, slug, category, basePrice, description, image,
  isAvailable, customizations: [{ type, label, price, options }]
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
  _id, orderNumber, items: [{ product, quantity, customizations, unitPrice, subtotal }],
  tableNumber, totalAmount, status: 'pending' | 'preparing' | 'ready' | 'delivered',
  createdAt
}
```

### Endpoints de Mesas (B2-Tables)

| # | Tarea | Endpoint | Descripción |
|---|-------|----------|-------------|
| B2.9 | Listar mesas | `GET /api/tables` | Obtener rango de mesas disponibles |
| B2.10 | Validar mesa | `GET /api/tables/:number/validate` | Verificar si número de mesa es válido |

### Lógica de Negocio (B2-Logic)

| # | Tarea | Descripción |
|---|-------|-------------|
| B2.11 | Calculadora de precios | Función que calcula precio final con personalizaciones |
| B2.12 | Generador de número de orden | Secuencial, con posible reinicio diario |
| B2.13 | Validador de personalizaciones | Verificar que personalizaciones aplican al producto |
| B2.14 | Rate limiting | Implementar límites: 100 req/min por kiosco |

**Dependencias**: B1.3 → B2.1-B2.4 (productos), B1.3 → B2.5-B2.8 (pedidos). B2.11-B2.13 son funciones helper que se usan en B2.5.

---

## FASE 3: Frontend — Interfaz de Kiosco

### Pantallas Principales (F3-Screens)

| # | Tarea | Ruta (React Router) | Descripción |
|---|-------|---------------------|-------------|
| F3.1 | Pantalla de inicio | `/` | Botón "Iniciar Pedido", branding Rodson |
| F3.2 | Categorías | `/menu` | Grid de 7 categorías con iconos |
| F3.3 | Productos por categoría | `/menu/:category` | Lista de productos con precio base |
| F3.4 | Detalle de producto | `/menu/:category/:product` | Personalizaciones según tipo de producto |
| F3.5 | Carrito | `/cart` | Resumen de items, edición, total |
| F3.6 | Selección de mesa | `/checkout/table` | Input numérico para número de mesa |
| F3.7 | Confirmación | `/checkout/confirm` | Resumen final, botón confirmar |
| F3.8 | Orden creada | `/order/:number` | Número de orden grande, instrucciones |

### Componentes de Negocio (F3-Components)

| # | Tarea | Componente | Descripción |
|---|-------|------------|-------------|
| F3.9 | CategoryCard | `components/features/CategoryCard` | Card de categoría con icono |
| F3.10 | ProductCard | `components/features/ProductCard` | Card de producto con imagen, nombre, precio |
| F3.11 | ProductCustomizer | `components/features/ProductCustomizer` | Formulario dinámico de personalizaciones |
| F3.12 | EspressoCustomizer | `components/features/EspressoCustomizer` | Leche vegetal, helado, licor |
| F3.13 | FilteredCoffeeCustomizer | `components/features/FilteredCoffeeCustomizer` | Método, tazas, tipo de café |
| F3.14 | CartItem | `components/features/CartItem` | Línea de item en carrito |
| F3.15 | CartSummary | `components/features/CartSummary` | Total, subtotales, botones acción |
| F3.16 | OrderNumber | `components/features/OrderNumber` | Display grande del número de orden |
| F3.17 | TableInput | `components/features/TableInput` | Input numérico con validación |

### Layouts (F3-Layouts)

| # | Tarea | Componente | Descripción |
|---|-------|------------|-------------|
| F3.18 | KioskLayout | `components/layouts/KioskLayout` | Layout full-screen para kiosco |
| F3.19 | Header | `components/layouts/Header` | Logo, carrito mini, navegación |
| F3.20 | CartFloating | `components/layouts/CartFloating` | Botón flotante con contador de items |

### Estado y Hooks (F3-State)

| # | Tarea | Descripción |
|---|-------|-------------|
| F3.21 | useCart hook | Zustand store: items, add, remove, update, clear, total |
| F3.22 | useProducts hook | Fetch de productos con React Query (TanStack Query) |
| F3.23 | useOrder hook | Crear orden, obtener estado |
| F3.24 | useTableValidation | Validar número de mesa contra API |

**Dependencias**: F3.1 depende de F1.3. F3.4 requiere F3.11-F3.13. F3.5 requiere F3.14-F3.15 y F3.21. Las pantallas F3.6-F3.8 dependen de F3.5.

---

## FASE 4: Integración y Flujo Completo

### Integración API (I4)

| # | Tarea | Descripción |
|---|-------|-------------|
| I4.1 | Conectar catálogo | Frontend consume `GET /api/products` |
| I4.2 | Conectar personalizaciones | Cargar opciones dinámicas desde API |
| I4.3 | Crear pedido | Frontend envía carrito a `POST /api/orders` |
| I4.4 | Validar mesa | Frontend valida con `GET /api/tables/:number/validate` |
| I4.5 | Mostrar número de orden | Recibir y mostrar `orderNumber` de respuesta |

### Validaciones End-to-End (I4-Validation)

| # | Tarea | Descripción |
|---|-------|-------------|
| I4.6 | Validar carrito no vacío | No permitir checkout sin items |
| I4.7 | Validar mesa requerida | No permitir confirmar sin mesa |
| I4.8 | Validar precios correctos | Comparar cálculo frontend vs backend |
| I4.9 | Manejar errores de API | Mostrar mensajes claros, no perder carrito |

---

## FASE 5: Optimización

### Performance (O5)

| # | Tarea | Descripción |
|---|-------|-------------|
| O5.1 | Optimizar imágenes | WebP, lazy loading nativo de React |
| O5.2 | Caché de catálogo | React Query cache, caché en API |
| O5.3 | Verificar tiempos | Cumplir: catálogo <2s, agregar <500ms, confirmar <3s |

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
│   └── Table.js            # Schema de mesas
├── controllers/
│   ├── productController.js    # Lógica de productos
│   ├── orderController.js      # Lógica de pedidos
│   └── tableController.js      # Lógica de mesas
├── routes/
│   ├── productRoutes.js    # Rutas de productos
│   ├── orderRoutes.js      # Rutas de pedidos
│   └── tableRoutes.js      # Rutas de mesas
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
├── App.tsx                             # Router principal
├── pages/
│   ├── Home.tsx                        # Pantalla de inicio
│   ├── Menu.tsx                        # Categorías
│   ├── CategoryProducts.tsx            # Productos por categoría
│   ├── ProductDetail.tsx               # Detalle con personalización
│   ├── Cart.tsx                        # Carrito
│   ├── TableSelection.tsx              # Selección de mesa
│   ├── Confirmation.tsx                # Confirmación
│   └── OrderCreated.tsx                # Orden creada
├── stores/
│   └── cartStore.ts                    # Zustand store del carrito
├── hooks/
│   ├── useProducts.ts                  # React Query para productos
│   ├── useOrder.ts                     # Crear y obtener órdenes
│   └── useTableValidation.ts           # Validar mesa
├── services/
│   └── api.ts                          # Cliente API con axios/fetch
├── components/
│   ├── features/
│   │   ├── EspressoCustomizer.tsx
│   │   ├── FilteredCoffeeCustomizer.tsx
│   │   ├── ProductCard.tsx
│   │   ├── CartItem.tsx
│   │   └── CartSummary.tsx
│   ├── layouts/
│   │   ├── KioskLayout.tsx
│   │   └── Header.tsx
│   └── ui/                             # Componentes base reutilizables
└── types/                              # Tipos TypeScript compartidos
```

---

## Verificación

| # | Caso de Prueba | Resultado Esperado |
|---|----------------|---------------------|
| 1 | Navegar todas las categorías | Ver productos con precios correctos |
| 2 | Latte + leche vegetal + helado | $8,500 + $5,500 + $4,500 = **$18,500** |
| 3 | Chemex 2 tazas Natural | $18,000 + $3,000 = **$21,000** |
| 4 | Agregar 3 productos, modificar, eliminar uno | Total recalculado correctamente |
| 5 | Ingresar mesa válida (1-10) | Aceptado |
| 6 | Ingresar mesa inválida (99) | Mostrar error claro |
| 7 | Confirmar pedido | Número de orden único, persistido en BD |

---

## Decisiones de Scope

### Incluido en MVP
- Sistema de kiosco para pedidos en local
- 7 categorías de productos con personalizaciones
- Carrito con cálculo en tiempo real
- Asignación de número de mesa (input manual)
- Generación de número de orden
- API REST para catálogo y pedidos

### Excluido (fases posteriores)
- Pasarela de pagos (pago en caja por ahora)
- Pantalla de tracking para clientes
- Panel de administración completo
- Sistema de inventario/stock
- Notificaciones push
- Modo "para llevar"

### Supuestos
- Mesas numeradas del 1 al 10 (configurable)
- No hay control de concurrencia de mesas
- Número de orden secuencial con reinicio diario opcional
- Imágenes proporcionadas por el cliente

---

## Consideraciones Pendientes

1. **¿Implementar pasarela de pagos ahora?**
   - *Opción A*: Sin pagos — pago en caja después de confirmar
   - *Opción B*: Integrar Wompi/MercadoPago
   - **Recomendación**: Diferir a fase 2

2. **¿Pantalla de tracking para cocina/baristas?**
   - Pantalla simple que consume `GET /api/orders/active`
   - **Recomendación**: Incluir para validar flujo completo

3. **¿Soporte para modo "para llevar"?**
   - Las reglas de negocio solo mencionan consumo en mesa
   - **Recomendación**: Confirmar con cliente si incluir en MVP
