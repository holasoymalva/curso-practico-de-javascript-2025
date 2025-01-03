# Módulo 4: Asincronía y APIs (E-commerce - Parte 1)

## Introducción

En este módulo aprenderemos sobre la programación asíncrona en JavaScript y cómo consumir APIs REST. Comenzaremos a construir nuestra aplicación de E-commerce implementando un catálogo de productos dinámico.

## Contenido

- [1. Asincronía en JavaScript](#1-asincronía-en-javascript)
- [2. Promises](#2-promises)
- [3. Async/Await](#3-asyncawait)
- [4. Fetch API](#4-fetch-api)
- [5. Implementación del E-commerce](#5-implementación-del-e-commerce)

## 1. Asincronía en JavaScript

### Callbacks

```javascript
// Ejemplo básico de callback
function obtenerDatos(callback) {
    setTimeout(() => {
        const datos = { id: 1, nombre: 'Producto' };
        callback(datos);
    }, 1000);
}

obtenerDatos((datos) => {
    console.log(datos); // { id: 1, nombre: 'Producto' }
});

// Callback Hell (ejemplo de lo que queremos evitar)
obtenerUsuario(function(usuario) {
    obtenerPedidos(usuario.id, function(pedidos) {
        obtenerDetalles(pedidos[0].id, function(detalles) {
            console.log(detalles);
        });
    });
});
```

### Event Loop y Call Stack

```javascript
console.log('1');

setTimeout(() => {
    console.log('2');
}, 0);

Promise.resolve('3').then(console.log);

console.log('4');

// Output:
// 1
// 4
// 3
// 2
```

## 2. Promises

### Creación y Manejo de Promesas

```javascript
// Crear una promesa
const miPromesa = new Promise((resolve, reject) => {
    const exito = true;
    
    if (exito) {
        resolve('¡Operación exitosa!');
    } else {
        reject('¡Algo salió mal!');
    }
});

// Usar la promesa
miPromesa
    .then(resultado => console.log(resultado))
    .catch(error => console.error(error));

// Encadenamiento de promesas
function obtenerUsuario(id) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({ id, nombre: 'Juan' });
        }, 1000);
    });
}

function obtenerPedidos(usuario) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve([
                { id: 1, userId: usuario.id, producto: 'Laptop' }
            ]);
        }, 1000);
    });
}

obtenerUsuario(1)
    .then(usuario => obtenerPedidos(usuario))
    .then(pedidos => console.log(pedidos))
    .catch(error => console.error(error));
```

### Promise.all y Promise.race

```javascript
// Promise.all - espera que todas las promesas se resuelvan
const promesa1 = Promise.resolve(3);
const promesa2 = new Promise(resolve => setTimeout(() => resolve(2), 2000));
const promesa3 = Promise.resolve(1);

Promise.all([promesa1, promesa2, promesa3])
    .then(valores => console.log(valores)) // [3, 2, 1]
    .catch(error => console.error(error));

// Promise.race - devuelve la primera promesa que se resuelva
Promise.race([promesa1, promesa2, promesa3])
    .then(valor => console.log(valor)) // 3
    .catch(error => console.error(error));
```

## 3. Async/Await

### Sintaxis Básica

```javascript
// Función asíncrona básica
async function obtenerDatos() {
    try {
        const response = await fetch('https://api.ejemplo.com/datos');
        const datos = await response.json();
        return datos;
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}

// Uso de async/await con Promise.all
async function obtenerMultiplesDatos() {
    try {
        const [usuarios, productos, pedidos] = await Promise.all([
            fetch('https://api.ejemplo.com/usuarios').then(r => r.json()),
            fetch('https://api.ejemplo.com/productos').then(r => r.json()),
            fetch('https://api.ejemplo.com/pedidos').then(r => r.json())
        ]);

        return { usuarios, productos, pedidos };
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}
```

## 4. Fetch API

### Métodos HTTP

```javascript
// GET Request
async function obtenerProductos() {
    const response = await fetch('https://api.ejemplo.com/productos');
    return response.json();
}

// POST Request
async function crearProducto(producto) {
    const response = await fetch('https://api.ejemplo.com/productos', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(producto)
    });
    return response.json();
}

// PUT Request
async function actualizarProducto(id, producto) {
    const response = await fetch(`https://api.ejemplo.com/productos/${id}`, {
        method: 'PUT',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(producto)
    });
    return response.json();
}

// DELETE Request
async function eliminarProducto(id) {
    const response = await fetch(`https://api.ejemplo.com/productos/${id}`, {
        method: 'DELETE'
    });
    return response.json();
}
```

### Manejo de Errores

```javascript
async function manejoDeErrores() {
    try {
        const response = await fetch('https://api.ejemplo.com/productos');
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const datos = await response.json();
        return datos;
    } catch (error) {
        if (error instanceof TypeError) {
            console.error('Problema de red:', error);
        } else {
            console.error('Otro tipo de error:', error);
        }
        throw error;
    }
}
```

## 5. Implementación del E-commerce

### Estructura del Proyecto

```plaintext
e-commerce/
├── index.html
├── styles/
│   └── main.css
├── js/
│   ├── api.js
│   ├── productos.js
│   └── app.js
└── assets/
    └── images/
```

### HTML Base

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>E-commerce</title>
    <link rel="stylesheet" href="styles/main.css">
</head>
<body>
    <header>
        <nav>
            <h1>Mi Tienda</h1>
            <div class="cart-icon">
                <span class="cart-count">0</span>
            </div>
        </nav>
    </header>
    
    <main>
        <section class="productos-grid" id="productos-container">
            <!-- Los productos se cargarán dinámicamente aquí -->
        </section>
    </main>

    <script src="js/api.js"></script>
    <script src="js/productos.js"></script>
    <script src="js/app.js"></script>
</body>
</html>
```

### API Service (api.js)

```javascript
class APIService {
    constructor(baseURL) {
        this.baseURL = baseURL;
    }

    async get(endpoint) {
        try {
            const response = await fetch(`${this.baseURL}${endpoint}`);
            if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
            return await response.json();
        } catch (error) {
            console.error('Error en GET request:', error);
            throw error;
        }
    }

    async post(endpoint, data) {
        try {
            const response = await fetch(`${this.baseURL}${endpoint}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });
            if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
            return await response.json();
        } catch (error) {
            console.error('Error en POST request:', error);
            throw error;
        }
    }
}
```

### Productos Service (productos.js)

```javascript
class ProductosService {
    constructor() {
        this.api = new APIService('https://api.ejemplo.com');
    }

    async obtenerProductos() {
        return await this.api.get('/productos');
    }

    async obtenerProductoPorId(id) {
        return await this.api.get(`/productos/${id}`);
    }

    async obtenerCategorias() {
        return await this.api.get('/categorias');
    }
}
```

### Aplicación Principal (app.js)

```javascript
class EcommerceApp {
    constructor() {
        this.productosService = new ProductosService();
        this.productosContainer = document.getElementById('productos-container');
        this.init();
    }

    async init() {
        try {
            await this.cargarProductos();
        } catch (error) {
            console.error('Error al inicializar la app:', error);
            this.mostrarError('No se pudieron cargar los productos');
        }
    }

    async cargarProductos() {
        const productos = await this.productosService.obtenerProductos();
        this.renderizarProductos(productos);
    }

    renderizarProductos(productos) {
        this.productosContainer.innerHTML = productos.map(producto => `
            <div class="producto-card" data-id="${producto.id}">
                <img src="${producto.imagen}" alt="${producto.nombre}">
                <h3>${producto.nombre}</h3>
                <p class="precio">$${producto.precio}</p>
                <button class="agregar-carrito">Agregar al carrito</button>
            </div>
        `).join('');
    }

    mostrarError(mensaje) {
        this.productosContainer.innerHTML = `
            <div class="error-mensaje">
                <p>${mensaje}</p>
                <button onclick="location.reload()">Reintentar</button>
            </div>
        `;
    }
}

// Inicializar la aplicación
const app = new EcommerceApp();
```

### Estilos CSS (main.css)

```css
.productos-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    padding: 20px;
}

.producto-card {
    border: 1px solid #ddd;
    padding: 15px;
    border-radius: 8px;
    text-align: center;
}

.producto-card img {
    width: 100%;
    height: 200px;
    object-fit: cover;
    border-radius: 4px;
}

.producto-card .precio {
    font-size: 1.2em;
    color: #2c3e50;
    margin: 10px 0;
}

.agregar-carrito {
    background-color: #3498db;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 4px;
    cursor: pointer;
}

.error-mensaje {
    text-align: center;
    padding: 20px;
    background-color: #fff3f3;
    border-radius: 8px;
}
```

## Recursos Adicionales

- [MDN - Promesas](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - Async/Await](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN - Fetch API](https://developer.mozilla.org/es/docs/Web/API/Fetch_API)
- [JavaScript.info - Async/await](https://javascript.info/async-await)
- [JavaScript.info - Fetch](https://javascript.info/fetch)
