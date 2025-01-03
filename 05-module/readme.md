# Módulo 5: ES6+ y Patrones Modernos (E-commerce - Parte 2)

## Introducción

En este módulo profundizaremos en las características modernas de JavaScript (ES6+) y los patrones de diseño más utilizados, implementándolos en nuestro E-commerce. Nos centraremos especialmente en el carrito de compras y la gestión del estado de la aplicación.

## Contenido

- [1. Características ES6+](#1-características-es6)
- [2. Patrones de Diseño Modernos](#2-patrones-de-diseño-modernos)
- [3. Implementación del Carrito](#3-implementación-del-carrito)
- [4. Gestión del Estado](#4-gestión-del-estado)

## 1. Características ES6+

### Destructuring

```javascript
// Destructuring de objetos
const producto = {
    id: 1,
    nombre: 'Laptop',
    precio: 999,
    specs: {
        ram: '16GB',
        procesador: 'i7'
    }
};

const { nombre, precio, specs: { ram } } = producto;
console.log(nombre, precio, ram); // 'Laptop' 999 '16GB'

// Destructuring de arrays
const colores = ['rojo', 'verde', 'azul'];
const [primario, secundario] = colores;
console.log(primario, secundario); // 'rojo' 'verde'

// Destructuring en parámetros de función
function procesarPedido({ id, total, productos = [] }) {
    console.log(`Procesando pedido ${id} con ${productos.length} productos`);
}
```

### Spread y Rest Operators

```javascript
// Spread con arrays
const numerosBase = [1, 2, 3];
const numerosExtendidos = [...numerosBase, 4, 5];
console.log(numerosExtendidos); // [1, 2, 3, 4, 5]

// Spread con objetos
const productoBase = {
    id: 1,
    nombre: 'Producto'
};

const productoDetallado = {
    ...productoBase,
    precio: 99.99,
    stock: 50
};

// Rest parameter
function sumarTodos(...numeros) {
    return numeros.reduce((suma, num) => suma + num, 0);
}
console.log(sumarTodos(1, 2, 3, 4)); // 10
```

### Optional Chaining y Nullish Coalescing

```javascript
const usuario = {
    nombre: 'Juan',
    direccion: {
        calle: 'Principal'
    }
};

// Optional chaining
console.log(usuario?.direccion?.codigoPostal); // undefined
console.log(usuario?.contacto?.email); // undefined

// Nullish coalescing
const nombre = usuario.nombre ?? 'Anónimo';
const edad = usuario.edad ?? 18;
```

### Map y Set

```javascript
// Map
const carritoMap = new Map();
carritoMap.set('prod1', { cantidad: 2, precio: 10 });
carritoMap.set('prod2', { cantidad: 1, precio: 20 });

// Iteración sobre Map
for (const [id, detalles] of carritoMap) {
    console.log(id, detalles);
}

// Set para productos únicos
const productosVistos = new Set();
productosVistos.add('prod1');
productosVistos.add('prod2');
productosVistos.add('prod1'); // No se duplica

console.log([...productosVistos]); // ['prod1', 'prod2']
```

## 2. Patrones de Diseño Modernos

### Observer Pattern

```javascript
class EventEmitter {
    constructor() {
        this.events = {};
    }

    on(event, callback) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(callback);
    }

    emit(event, data) {
        if (this.events[event]) {
            this.events[event].forEach(callback => callback(data));
        }
    }
}

// Uso en el carrito
class CarritoEventos extends EventEmitter {
    constructor() {
        super();
        this.items = new Map();
    }

    agregarItem(producto) {
        this.items.set(producto.id, producto);
        this.emit('itemAgregado', producto);
    }
}
```

### Singleton Pattern

```javascript
class Carrito {
    constructor() {
        if (Carrito.instance) {
            return Carrito.instance;
        }
        
        this.productos = new Map();
        Carrito.instance = this;
    }

    static getInstance() {
        if (!Carrito.instance) {
            Carrito.instance = new Carrito();
        }
        return Carrito.instance;
    }
}
```

### Module Pattern

```javascript
const CarritoModule = (function() {
    const items = new Map();
    const eventos = new EventEmitter();

    function agregarItem(producto) {
        items.set(producto.id, producto);
        eventos.emit('cambio', getItems());
    }

    function eliminarItem(id) {
        items.delete(id);
        eventos.emit('cambio', getItems());
    }

    function getItems() {
        return Array.from(items.values());
    }

    return {
        agregarItem,
        eliminarItem,
        getItems,
        eventos
    };
})();
```

## 3. Implementación del Carrito

### Estado del Carrito (CarritoState.js)

```javascript
class CarritoState {
    constructor() {
        this.items = new Map();
        this.eventos = new EventEmitter();
    }

    agregarProducto(producto, cantidad = 1) {
        const itemActual = this.items.get(producto.id);
        
        if (itemActual) {
            itemActual.cantidad += cantidad;
        } else {
            this.items.set(producto.id, {
                ...producto,
                cantidad
            });
        }

        this.eventos.emit('carritoActualizado', this.obtenerResumen());
    }

    eliminarProducto(id) {
        this.items.delete(id);
        this.eventos.emit('carritoActualizado', this.obtenerResumen());
    }

    actualizarCantidad(id, cantidad) {
        const item = this.items.get(id);
        if (item) {
            item.cantidad = cantidad;
            this.eventos.emit('carritoActualizado', this.obtenerResumen());
        }
    }

    obtenerResumen() {
        const items = Array.from(this.items.values());
        const total = items.reduce((sum, item) => sum + (item.precio * item.cantidad), 0);
        const cantidadTotal = items.reduce((sum, item) => sum + item.cantidad, 0);

        return {
            items,
            total,
            cantidadTotal
        };
    }
}
```

### Componente del Carrito (CarritoUI.js)

```javascript
class CarritoUI {
    constructor(carritoState) {
        this.state = carritoState;
        this.carritoContainer = document.getElementById('carrito-container');
        this.init();
    }

    init() {
        this.state.eventos.on('carritoActualizado', (resumen) => {
            this.actualizarUI(resumen);
        });

        this.setupEventListeners();
    }

    setupEventListeners() {
        this.carritoContainer.addEventListener('click', (e) => {
            if (e.target.classList.contains('eliminar-item')) {
                const id = e.target.dataset.id;
                this.state.eliminarProducto(id);
            }

            if (e.target.classList.contains('actualizar-cantidad')) {
                const id = e.target.dataset.id;
                const cantidad = parseInt(e.target.value);
                this.state.actualizarCantidad(id, cantidad);
            }
        });
    }

    actualizarUI(resumen) {
        this.carritoContainer.innerHTML = `
            <div class="carrito-items">
                ${resumen.items.map(item => `
                    <div class="carrito-item" data-id="${item.id}">
                        <img src="${item.imagen}" alt="${item.nombre}">
                        <div class="item-detalles">
                            <h3>${item.nombre}</h3>
                            <p>Precio: $${item.precio}</p>
                            <input type="number" 
                                   class="actualizar-cantidad" 
                                   data-id="${item.id}" 
                                   value="${item.cantidad}"
                                   min="1">
                            <button class="eliminar-item" data-id="${item.id}">
                                Eliminar
                            </button>
                        </div>
                    </div>
                `).join('')}
            </div>
            <div class="carrito-resumen">
                <p>Total Items: ${resumen.cantidadTotal}</p>
                <p>Total: $${resumen.total.toFixed(2)}</p>
                <button class="proceder-checkout">
                    Proceder al Checkout
                </button>
            </div>
        `;
    }
}
```

### Estilos del Carrito (carrito.css)

```css
.carrito-container {
    position: fixed;
    right: 0;
    top: 0;
    width: 400px;
    height: 100vh;
    background: white;
    box-shadow: -2px 0 5px rgba(0,0,0,0.1);
    padding: 20px;
    transform: translateX(100%);
    transition: transform 0.3s ease;
}

.carrito-container.visible {
    transform: translateX(0);
}

.carrito-item {
    display: flex;
    padding: 10px;
    border-bottom: 1px solid #eee;
    align-items: center;
}

.carrito-item img {
    width: 80px;
    height: 80px;
    object-fit: cover;
    margin-right: 15px;
}

.item-detalles {
    flex: 1;
}

.actualizar-cantidad {
    width: 60px;
    padding: 5px;
    margin: 5px 0;
}

.carrito-resumen {
    position: sticky;
    bottom: 0;
    background: white;
    padding: 15px;
    border-top: 1px solid #eee;
}

.proceder-checkout {
    width: 100%;
    padding: 10px;
    background: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.proceder-checkout:hover {
    background: #45a049;
}
```

## 4. Gestión del Estado

### Store Central (store.js)

```javascript
class Store {
    constructor() {
        this.state = {
            productos: [],
            carrito: new CarritoState(),
            usuario: null,
            filtros: {
                categoria: null,
                precioMin: null,
                precioMax: null
            }
        };
        this.eventos = new EventEmitter();
    }

    dispatch(accion, payload) {
        switch (accion) {
            case 'SET_PRODUCTOS':
                this.state.productos = payload;
                break;
            case 'SET_FILTROS':
                this.state.filtros = { ...this.state.filtros, ...payload };
                break;
            case 'SET_USUARIO':
                this.state.usuario = payload;
                break;
        }

        this.eventos.emit('stateChange', this.state);
    }

    getState() {
        return this.state;
    }
}

// Uso del store
const store = new Store();

store.eventos.on('stateChange', (state) => {
    console.log('Nuevo estado:', state);
    // Actualizar UI correspondiente
});
```

## Recursos Adicionales

- [MDN - JavaScript Reference](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference)
- [ES6 Features](https://github.com/lukehoban/es6features)
- [JavaScript Design Patterns](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)
- [JavaScript.info - Modern JavaScript](https://javascript.info/)
