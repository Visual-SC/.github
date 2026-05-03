# Historias de Usuario

## HU-001 - Mostrar productos por categoría

### Descripción

> Cómo **Usuario**,
> quiero **ver los productos organizados por categoría, cuando de click sobre alguno de los links de categoría**
> para **poder elegir los productos de acuerdo a su categoría**

### Puntos de nivel de esfuerzo

- Puntos de esfuerzo **5**

### Criterios de aceptación

#### Escenario 1: El usario puede seleccionar productos por categoría

> - **Dado** que el usuario se encuentra en el Home
> - **Y** da click sobre alguno de los links de la categoría
> - **Entonces** La `grilla de productos` muestra los productos relacionados a la categoría
> - **Y** el usuario puede dar click sobre uno de los productos para `Ver card de información completa del producto`

## HU-002 - Ver información completa de producto

### Descripción

> Cómo **Usuario**,
> quiero **Ver una interfaz flotante con la información completa del producto**
> para **agregar el producto al carrito de compras**

### Puntos de nivel de esfuerzo

- Puntos de esfuerzo **5**

### Criterios de aceptación

#### Escenario 1: El usuario puede ver la información del producto

> - **Dado** que el usuario se encuentra en el Home
> - **Y** da click sobre uno de los productos de la grilla de productos
> - **Entonces** Se muestra un pop-up con la información completa del producto, y puede ver
> - Pop-up de producto
>   - Nombre
>   - Categoría
>   - Descripción
>   - Cantidad
>   - Precio total (Precio unitario * cantidad)
> - **Y** el usuario puede elegir la cantidad de unidades para agregar al carrito de compras
> - **Entonces** el usuario puede `agregar` el producto para llevarlo al `carrito de compras`
> - **Y** el usuario ve la confirmación de `producto agregado`

## HU-003 - Ver lista de productos elegidos en el Carrito de Compras

### Descripción

> Cómo **Usuario**,
> quiero **Ver en una interfaz la lista de productos que he agregado al carrito de compras**
> para **Modificar productos por cantidad y confirmar la orden**

### Puntos de nivel de esfuerzo

> Puntos de esfuerzo **5**

### Criterios de aceptación

#### Escenario 1: El usuario ha elegido productos y los agrega al carrito de compras

> - **Dado** que el usuario se encuentra en el Home
> - **Y** el usuario ha agregado  `ícono de carrito de compra`
> - **Y** da click sobre el `ícono de carrito de compra`
> - **Entonces** Se muestra una interfaz lateral con la lista 
> - Pop-up de producto
>   - Nombre
>   - Categoría
>   - Descripción
>   - Cantidad
>   - Precio total (Precio unitario * cantidad)
> - **Y** el usuario puede elegir la cantidad de unidades para agregar al carrito de compras
> - **Entonces** el usuario puede `agregar` el producto para llevarlo al `carrito de compras` 

#### Escenario 2: El usuario no ha elegido productos y da click sobre el carrito de compras

> - **Dado** que el usuario se encuentra en el Home
> - **Y** da click sobre el `ícono de carrito de compra`
> - **Y** el usuario `aún no ha elegido productos`
> - **Entonces** Se muestra una interfaz lateral con la lista 
> - Pop-up de producto
>   - Imágen mostrando que el `carrito de compras` está `vacío`
>   - Texto descriptivo para invitar a comprar
>   - Botón con link para `Ver menú`
> - **Y** el usuario da click sobre el botón
> - **Entonces** el usuario retorna al hom para volver a ver los productos 

## HU-004 - Hacer confirmación de la orden

### Descripción

> Cómo **Usuario**,
> quiero **ver la interfaz de Carrito de Compras**
> para **confirmar la orden, una vez he elegido la cantidad de productos**

### Puntos de nivel de esfuerzo

> Puntos de esfuerzo **5**

### Criterios de aceptación

#### Escenario 1: El usuario modifica o confirma productos para hacer la orden

> - **Dado** que el usuario se encuentra en el Home
> - **Y** el usuario ha agregado productos en el side lateral de `carrito de compras`
> - **Entonces** observa la lista de `productos elegidos` con la siguiente información
> - Tu orden
> - Producto no. 1
>   - Nombre
>   - Categoría
>   - Descripción
>   - Cantidad
>   - botones para modificar cantidad del producto
>   - botón para quitar el producto
>   - Precio total (Precio unitario * cantidad)
> - Producto no. 2
>   - Nombre
>   - Categoría
>   - Descripción
>   - Cantidad
>   - botones para modificar cantidad del producto
>   - botón para quitar el producto
>   - Precio total (Precio unitario * cantidad)
> - Total de la orden
> - Botón de `Confirmar Orden`
> - **Y** el usuario revisa la orden y se encuentra satistecho con el producto da click
> - **Entonces** el usuario `Confirma la orden`
