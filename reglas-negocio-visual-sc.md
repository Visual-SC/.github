# Reglas de Negocio - Visual SC
## Sistema de Pedidos en Kiosco para Rodson Coffee

**Versión:** 1.0  
**Fecha:** 13 de Abril, 2026  
**Cliente:** Rodson Coffee (@rodsoncoffee)  
**Ubicación:** Carrera 20 #53-35, Bogotá, Colombia

---

## 1. Contexto del Negocio

### 1.1 Descripción General
Rodson Coffee es una cafetería de especialidad que combina tres propuestas de valor:
- **Café de especialidad**
- **Brunch**
- **Espacio cultural**

### 1.2 Propósito del Sistema
Desarrollar una aplicación web de pedidos en kiosco para uso dentro del local que permita al cliente:
- Construir su orden de manera autónoma
- Personalizar productos según preferencias
- Identificar su mesa mediante un número

---

## 2. Reglas del Catálogo de Productos

### 2.1 Categorías Principales

El sistema debe manejar **7 categorías obligatorias**:

1. **BASE DE ESPRESSO**
2. **BEBIDAS FRÍAS**
3. **BEBIDAS CALIENTES SIN CAFÉ**
4. **PASTELERÍA DULCE**
5. **PASTELERÍA DE SAL**
6. **ALICORADOS**
7. **ADICIONES**

### 2.2 Estructura de Productos
Cada producto debe contener:
- **ID único**: Identificador interno del producto
- **Nombre**: Nombre comercial del producto
- **Categoría**: Una de las 7 categorías establecidas
- **Precio base**: Precio
- **Descripción**: Texto breve descriptivo
- **Imagen**: URL o referencia a imagen del producto (opcional)

---

## 3. Reglas de Personalización de Productos

### 3.1 Personalización de Espresso
Para productos de la categoría **BASE DE ESPRESSO**:

#### 3.1.1 Tamaño
- Algunos productos pueden tener selección de tamaño
- Los tamaños disponibles deben estar predefinidos por producto
- El cambio de tamaño puede modificar el precio base

#### 3.1.2 Leche Vegetal
- **Regla de precio**: Recargo de **+$5,500 COP**
- Debe ser opcional (checkbox o toggle)
- Por defecto: NO seleccionado
- Opciones posibles: Leche de almendras, avena, coco, soya (según disponibilidad)

#### 3.1.3 Bola de Helado de Vainilla
- **Regla de precio**: Recargo de **+$4,500 COP**
- Debe ser opcional
- Por defecto: NO seleccionado
- Validación: Solo sabor vainilla disponible

#### 3.1.4 Licor
- **Regla de precio**: Recargo de **+$8,000 COP**
- Debe ser opcional
- Por defecto: NO seleccionado
- Restricción: Esta opción implica contenido alcohólico

### 3.2 Personalización de Métodos Filtrados
Para productos de la categoría **MÉTODOS DE CAFÉ FILTRADO**:

#### 3.2.1 Método de Preparación (OBLIGATORIO)
Opciones disponibles:
- **Inmersión**
- **Goteo**
- **Presión**

Cada método puede tener:
- Descripción breve del proceso
- Tiempo estimado de preparación
- Características del resultado

#### 3.2.2 Cantidad de Tazas (OBLIGATORIO)
Opciones de precio:
- **1 taza**: $10,000 COP
- **2 tazas**: $18,000 COP

**Regla de descuento**: La segunda taza tiene descuento implícito de $2,000 COP

#### 3.2.3 Tipo de Café
- **Naturales**: Recargo adicional de **+$3,000 COP**
- **Honeys**: Recargo adicional de **+$3,000 COP**
- **Lavados**: Precio base sin recargo

**Validación**: El sistema debe mostrar claramente el recargo cuando aplique

### 3.3 Personalización de Otros Productos
Para **BEBIDAS FRÍAS**, **BEBIDAS CALIENTES**, **PASTELERÍA**, **ALICORADOS**:
- Selección directa sin personalizaciones adicionales
- O personalizaciones específicas definidas por producto
- Validación de stock en tiempo real si aplica

---

## 4. Reglas del Carrito de Compras

### 4.1 Gestión de Items
- El cliente puede agregar **múltiples productos** antes de confirmar
- Cada producto puede agregarse **múltiples veces** con diferentes personalizaciones
- El carrito debe mostrar:
  - Nombre del producto
  - Personalizaciones aplicadas
  - Precio unitario
  - Cantidad
  - Subtotal por línea
  - Total general

### 4.2 Modificación del Carrito
- El cliente puede **eliminar items** del carrito
- El cliente puede **modificar cantidades** de items idénticos
- El cliente puede **editar personalizaciones** de un item existente
- Cada modificación debe recalcular el total en **tiempo real**

### 4.3 Validaciones del Carrito
- **Mínimo**: El carrito debe tener al menos **1 producto** para proceder
- **Máximo**: No hay límite de productos (salvo restricciones técnicas)
- **Precios**: Todos los precios deben calcularse correctamente incluyendo personalizaciones
- **Disponibilidad**: Validar que todos los productos sigan disponibles antes de confirmar

### 4.4 Persistencia del Carrito
- El carrito debe mantenerse mientras el usuario está en la sesión
- Si el kiosco se reinicia, el carrito debe vaciarse
- El carrito NO debe persistir entre diferentes clientes

---

## 5. Reglas del Flujo de Pedidos

### 5.1 Inicio del Pedido
```
ACCIÓN: Cliente toca "Iniciar Pedido" en kiosco
RESULTADO: Sistema muestra categorías de productos
ESTADO: Sesión activa
```

### 5.2 Asignación de Número de Mesa (OBLIGATORIO)
Todos los pedidos son **para consumo dentro del local**. El cliente debe ingresar su **número de mesa** antes de confirmar el pedido:

- El local cuenta con mesas numeradas físicamente
- El cliente ingresa manualmente el número de mesa que ocupa
- El pedido será entregado en la mesa por el personal

**Validación**: El sistema NO puede proceder sin un número de mesa válido

### 5.3 Construcción de la Orden
```
MIENTRAS el cliente está construyendo la orden:
  1. Navegar categorías
  2. Seleccionar producto
  3. Personalizar (si aplica)
  4. Agregar al carrito
  5. Revisar carrito
  6. DECISIÓN: ¿Agregar más productos?
     SÍ → Volver al paso 1
     NO → Proceder a confirmación
```

### 5.4 Revisión y Confirmación

Antes de confirmar, el sistema debe mostrar:
- **Lista completa** de productos en el carrito
- **Personalizaciones** de cada producto
- **Precio unitario** y **subtotales**
- **Total general** del pedido
- **Número de mesa** asignado

**Botones de acción**:
- **Confirmar Pedido**: Genera la orden
- **Seguir Comprando**: Vuelve al catálogo
- **Cancelar Todo**: Vacía el carrito y reinicia

### 5.5 Asignación de Número de Orden

```
REGLA: Cada pedido confirmado recibe un número único
FORMATO: Numérico secuencial (ejemplo: #127, #128, #129)
REINICIO: El contador puede reiniciarse diariamente o ser continuo
VISUALIZACIÓN: El número debe ser visible en fuente GRANDE
```

**Características del número**:
- Único por pedido
- Fácil de leer y recordar
- Mostrado inmediatamente después de confirmar la orden

---

## 6. Reglas de Identificación de Mesa

### 6.1 Numeración de Mesas
El local cuenta con un número reducido de mesas, cada una identificada con un **número físico visible**.

- No se requiere plano digital del local
- Las mesas están numeradas físicamente (ej: 1, 2, 3...)
- El cliente identifica su mesa por el número visible en ella

### 6.2 Ingreso del Número de Mesa
**Flujo de asignación**:

```
1. Cliente se sienta en una mesa del local
2. Cliente inicia pedido en kiosco
3. Sistema solicita número de mesa
4. Cliente ingresa el número visible en su mesa
5. Sistema asocia el pedido al número ingresado
```

**Validaciones**:
- El número de mesa debe ser un valor válido dentro del rango configurado
- El sistema muestra error claro si el número no existe

### 6.3 Configuración de Mesas
Desde el panel de administración se puede:
- Definir el **rango de mesas disponibles** (ej: 1 a 10)
- Activar o desactivar mesas temporalmente

---

## 7. Reglas de Pricing y Cálculos

### 7.1 Estructura de Precios
```javascript
PrecioFinal = PrecioBase + Σ(Personalizaciones) + Recargos
```

**Componentes**:
- **Precio Base**: Precio del producto sin modificaciones
- **Personalizaciones**: Suma de todos los add-ons seleccionados
- **Recargos**: Cargos adicionales por tipo de café (naturales/honeys)

### 7.2 Tabla de Precios de Personalizaciones

| Personalización | Precio | Aplica a |
|----------------|--------|----------|
| Leche vegetal | +$5,500 | Espresso |
| Helado vainilla | +$4,500 | Espresso |
| Licor | +$8,000 | Espresso |
| Café natural/honey | +$3,000 | Métodos filtrados |

### 7.3 Precios Especiales

#### Métodos Filtrados

- **1 taza**: $10,000 COP
- **2 tazas**: $18,000 COP (descuento implícito)

**Cálculo con naturales/honeys**:
- 1 taza natural: $10,000 + $3,000 = $13,000
- 2 tazas natural: $18,000 + $3,000 = $21,000

### 7.4 Reglas de Redondeo

- Todos los precios en **pesos colombianos (COP)**
- Sin centavos (redondeo a múltiplos de $500 si necesario)
- Formato de visualización: **$10,000** o **$10.000**

---

## 8. Reglas de Validación y Control de Errores

### 8.1 Validaciones de Entrada

#### Selección de Productos

```
VALIDAR:
  ✓ Producto existe en catálogo
  ✓ Producto está disponible (activo)
  ✓ Producto tiene stock (si aplica control de inventario)
  ✓ Categoría del producto es válida
```

#### Personalizaciones
```
VALIDAR:
  ✓ Personalización es aplicable al producto
  ✓ Valores de personalización son válidos
  ✓ Combinaciones de personalizaciones son permitidas
  ✓ Precios de personalizaciones están actualizados
```

#### Carrito

```
VALIDAR:
  ✓ Carrito no está vacío al confirmar
  ✓ Todos los productos siguen disponibles
  ✓ Cálculos de precios son correctos
  ✓ No hay productos duplicados por error
```

#### Número de Mesa

```
VALIDAR:
  ✓ Número de mesa ha sido ingresado
  ✓ Número de mesa existe dentro del rango configurado
  ✓ Número de mesa es válido
```

### 8.2 Manejo de Errores

#### Errores de Usuario

Cuando el usuario comete un error:

- **Mostrar mensaje claro** del problema
- **Sugerir acción correctiva** específica
- **Mantener el estado** anterior (no perder el carrito)
- **Permitir corrección** fácil

**Ejemplos**:
```
❌ Error: "Error en el sistema"
✅ Correcto: "La mesa #5 ya está ocupada. Por favor selecciona otra mesa disponible."

❌ Error: "Producto no válido"
✅ Correcto: "Lo sentimos, el Latte Vainilla no está disponible en este momento. ¿Te gustaría probar nuestro Latte Caramelo?"
```

#### Errores de Sistema
Si hay error técnico:
- **Guardar estado** del carrito si es posible
- **Notificar al personal** automáticamente
- **Ofrecer alternativa** al cliente
- **Registrar error** en logs para debugging

---

## 10. Reglas de Rendimiento y Escalabilidad

### 10.1 Tiempos de Respuesta
El sistema debe cumplir con estos objetivos:

| Acción | Tiempo Máximo | Objetivo |
|--------|---------------|----------|
| Cargar catálogo | 2 segundos | 1 segundo |
| Agregar al carrito | 500ms | 200ms |
| Confirmar pedido | 3 segundos | 1.5 segundos |
| Actualizar pantalla tracking | 1 segundo | 500ms |
| Refrescar estado mesa | 2 segundos | 1 segundo |

### 10.2 Concurrencia

```
ESCENARIO: Múltiples clientes usando kioscos simultáneamente
REGLA: El sistema debe manejar al menos N pedidos concurrentes
N = Número de kioscos × 2

EJEMPLO: 
  3 kioscos instalados
  Capacidad mínima: 6 pedidos simultáneos
```

### 10.3 Disponibilidad

- **Objetivo**: 99.5% uptime durante horario de servicio
- **Mantenimiento**: Fuera de horario laboral
- **Redundancia**: Backup de base de datos automático

### 10.4 Optimización de Assets

- **Imágenes**: Comprimidas y en formatos modernos (WebP)
- **Caché**: Catálogo en caché local del kiosco
- **Lazy loading**: Cargar imágenes solo cuando son visibles
- **CDN**: Servir assets estáticos desde CDN si es posible

---

## 10. Reglas de Integración con Backend

### 11.1 API Endpoints
El frontend del kiosco debe comunicarse con backend mediante API REST:

#### Productos
```
GET /api/products
  → Obtener catálogo completo

GET /api/products/{id}
  → Obtener detalle de producto específico

GET /api/products/category/{category}
  → Obtener productos por categoría

GET /api/products/{id}/availability
  → Verificar disponibilidad de producto
```

#### Pedidos
```
POST /api/orders
  → Crear nuevo pedido
  Body: {
    items: [...],
    mode: "mesa" | "para-llevar",
    table_number?: number
  }

GET /api/orders/{order_number}
  → Obtener detalle de pedido

PATCH /api/orders/{order_number}/status
  → Actualizar estado de pedido (uso interno)

GET /api/orders/active
  → Obtener pedidos activos para pantalla tracking
```

### 11.2 Manejo de Respuestas
Todas las respuestas de API deben seguir estructura estándar:

```json
{
  "success": true/false,
  "data": {...},
  "message": "Mensaje descriptivo",
  "errors": [...] // solo si success = false
}
```

### 11.3 Autenticación de API
- **Kiosco público**: Token de kiosco (no usuario)
- **Panel admin**: JWT con expiración
- **Regeneración**: Tokens deben poder regenerarse

### 11.4 Rate Limiting
Para proteger el backend:
- Límite de **100 requests/minuto** por kiosco
- Límite de **10 pedidos/hora** por kiosco (prevenir spam)

---

## 12. Reglas de Entorno de Desarrollo

### 12.1 Configuración de Ambientes

#### Desarrollo (DEV)
- URL: `http://localhost:3000` (frontend) / `http://localhost:5000` (backend)
- Base de datos: MongoDB local o contenedor Docker
- Datos: Data de prueba/seed
- Logs: Nivel DEBUG
- No requiere HTTPS

#### Staging (STG)
- URL: `https://staging.visual-sc.com`
- Base de datos: MongoDB Atlas (cluster de staging)
- Datos: Copia de producción (anonymizada)
- Logs: Nivel INFO
- HTTPS obligatorio
- Replica configuración de producción

#### Producción (PROD)
- URL: `https://kiosco.rodsoncoffee.com`
- Base de datos: MongoDB Atlas (cluster productivo)
- Datos: Reales
- Logs: Nivel WARNING
- HTTPS obligatorio
- Backups automáticos diarios
- Monitoring 24/7

### 12.2 Variables de Entorno
Archivo `.env` debe incluir:

```bash
# General
NODE_ENV=development|staging|production
PORT=3000

# Database
MONGODB_URI=mongodb://...
DB_NAME=visual_sc

# API
API_URL=https://api.visual-sc.com
API_VERSION=v1

# Seguridad
JWT_SECRET=...
SESSION_SECRET=...

# Servicios externos
PAYMENT_GATEWAY_KEY=...
ANALYTICS_ID=...

# Features flags
ENABLE_AI_RECOMMENDATIONS=true|false
ENABLE_INVENTORY_CONTROL=true|false
```
---

## 13. Glosario de Términos

| Término | Definición |
|---------|------------|
| **Kiosco** | Terminal de auto-servicio para realizar pedidos |
| **Espresso** | Café preparado forzando agua caliente a presión a través de café molido fino |
| **Método filtrado** | Técnicas de preparación de café que usan filtro (inmersión, goteo, presión) |
| **Para llevar** | Modalidad de pedido donde el cliente recoge y se va |
| **Para mesa** | Modalidad donde el cliente consume en el local |
| **Número de orden** | Identificador único de cada pedido |
| **Tracking** | Seguimiento en tiempo real del estado del pedido |
| **Personalización** | Opciones adicionales o modificaciones a un producto |
| **Carrito** | Colección temporal de productos antes de confirmar pedido |
| **Naturales/Honeys** | Tipos de procesamiento de café que afectan el sabor |
| **Barista** | Persona especializada en preparación de café |
| **TPV** | Terminal Punto de Venta para procesamiento de pagos |
| **Ticket promedio** | Valor promedio de un pedido |
| **Uptime** | Porcentaje de tiempo que el sistema está operativo |
| **AI-first** | Enfoque de desarrollo que prioriza capacidades de inteligencia artificial |

---

## 14. Anexos

### 14.1 Catálogo de Productos de Referencia

#### Base de Espresso
- Espresso Simple ($6,000)
- Espresso Doble ($8,000)
- Cappuccino ($8,000)
- Latte ($8,500)
- Flat White ($9,000)
- Macchiato ($7,000)
- Mocha ($10,000)
- Americano ($7,000)

#### Métodos Filtrados
- Chemex ($10,000 / $18,000)
- V60 ($10,000 / $18,000)
- Prensa Francesa ($10,000 / $18,000)
- Aeropress ($10,000 / $18,000)

#### Bebidas Frías
- Cold Brew ($9,000)
- Frappé ($11,000)
- Limonada de Café ($8,000)
- Malteada de Café ($12,000)

#### Pastelería Dulce
- Croissant ($5,000)
- Muffin ($6,000)
- Brownie ($7,000)
- Cheesecake ($9,000)

#### Pastelería de Sal
- Sandwich de Pavo ($12,000)
- Wrap Vegetariano ($11,000)
- Quiche ($10,000)

**Nota**: Precios de referencia, sujetos a actualización por el cliente

### 14.3 Mockups de Referencia
*(En producción real, incluir imágenes de mockups)*

**Pantallas clave**:
1. Pantalla de inicio
2. Vista de categorías
3. Detalle de producto con personalizaciones
4. Carrito de compras
5. Selección de modalidad
6. Plano de mesas
7. Resumen de pedido
8. Confirmación con número de orden
9. Pantalla de seguimiento

### 14.4 Contactos del Proyecto

**Cliente**: Rodson Coffee  
**Instagram**: @rodsoncoffee  
**Ubicación**: Carrera 20 #53-35, Bogotá, Colombia

**Desarrollador**: Visual SC  
**GitHub**: https://github.com/Visual-SC  
**Concurso**: Hackabox - Sandbox Fundación

**FIN DEL DOCUMENTO**
