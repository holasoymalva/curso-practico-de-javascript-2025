# Módulo 3: Arrays, Objetos y Almacenamiento Local (Todo App - Parte 2)

## Introducción

En este módulo profundizaremos en el manejo de arrays y objetos en JavaScript, además de implementar el almacenamiento local en nuestra Todo App. Aprenderemos a persistir datos y a manipular colecciones de información de manera eficiente.

## Contenido

- [1. Arrays y sus Métodos](#1-arrays-y-sus-métodos)
- [2. Objetos en JavaScript](#2-objetos-en-javascript)
- [3. LocalStorage](#3-localstorage)
- [4. Implementación en Todo App](#4-implementación-en-todo-app)

## 1. Arrays y sus Métodos

### Métodos de Transformación

```javascript
// map - Transforma cada elemento del array
const numeros = [1, 2, 3, 4, 5];
const duplicados = numeros.map(numero => numero * 2);
console.log(duplicados); // [2, 4, 6, 8, 10]

// filter - Filtra elementos según una condición
const mayoresQueTres = numeros.filter(numero => numero > 3);
console.log(mayoresQueTres); // [4, 5]

// reduce - Reduce el array a un único valor
const suma = numeros.reduce((acumulador, actual) => acumulador + actual, 0);
console.log(suma); // 15
```

### Métodos de Búsqueda y Verificación

```javascript
// find - Encuentra el primer elemento que cumple una condición
const usuarios = [
    { id: 1, nombre: 'Ana' },
    { id: 2, nombre: 'Juan' },
    { id: 3, nombre: 'María' }
];

const usuario = usuarios.find(u => u.id === 2);
console.log(usuario); // { id: 2, nombre: 'Juan' }

// some - Verifica si al menos un elemento cumple la condición
const tieneAna = usuarios.some(u => u.nombre === 'Ana');
console.log(tieneAna); // true

// every - Verifica si todos los elementos cumplen la condición
const todosConId = usuarios.every(u => u.id > 0);
console.log(todosConId); // true
```

### Métodos de Modificación

```javascript
// splice - Modifica el array original
const frutas = ['manzana', 'pera', 'uva', 'naranja'];
frutas.splice(1, 2, 'kiwi', 'banana');
console.log(frutas); // ['manzana', 'kiwi', 'banana', 'naranja']

// slice - Crea una copia de una porción del array
const primeras = frutas.slice(0, 2);
console.log(primeras); // ['manzana', 'kiwi']

// sort - Ordena el array
const desordenados = [3, 1, 4, 1, 5];
desordenados.sort((a, b) => a - b);
console.log(desordenados); // [1, 1, 3, 4, 5]
```

## 2. Objetos en JavaScript

### Manipulación de Objetos

```javascript
// Creación y acceso a propiedades
const persona = {
    nombre: 'Juan',
    edad: 25,
    direccion: {
        calle: 'Principal',
        numero: 123
    }
};

// Diferentes formas de acceder a propiedades
console.log(persona.nombre);              // Juan
console.log(persona['edad']);             // 25
console.log(persona.direccion.calle);     // Principal

// Object.keys - Obtiene las claves del objeto
const claves = Object.keys(persona);
console.log(claves); // ['nombre', 'edad', 'direccion']

// Object.values - Obtiene los valores del objeto
const valores = Object.values(persona);
console.log(valores); // ['Juan', 25, { calle: 'Principal', numero: 123 }]
```

### Métodos y Propiedades Avanzadas

```javascript
// Spread operator con objetos
const datosBase = { id: 1, nombre: 'Producto' };
const productoCompleto = {
    ...datosBase,
    precio: 99.99,
    stock: 50
};

// Object.assign
const objetivoVacio = {};
const objetivoCompleto = Object.assign(objetivoVacio, datosBase, { color: 'rojo' });

// Getters y Setters
const cuenta = {
    _balance: 0,
    get balance() {
        return `$${this._balance}`;
    },
    set balance(valor) {
        this._balance = valor;
    }
};
```

## 3. LocalStorage

### Operaciones Básicas

```javascript
// Guardar datos
localStorage.setItem('usuario', 'Juan');
localStorage.setItem('config', JSON.stringify({ tema: 'oscuro', notificaciones: true }));

// Obtener datos
const usuario = localStorage.getItem('usuario');
const config = JSON.parse(localStorage.getItem('config'));

// Eliminar datos
localStorage.removeItem('usuario');

// Limpiar todo el localStorage
localStorage.clear();

// Verificar si existe una clave
const existeConfig = localStorage.getItem('config') !== null;
```

### Ejemplo Práctico con LocalStorage

```javascript
// Clase para manejar el almacenamiento
class Storage {
    constructor(key) {
        this.key = key;
    }

    save(data) {
        localStorage.setItem(this.key, JSON.stringify(data));
    }

    load() {
        const data = localStorage.getItem(this.key);
        return data ? JSON.parse(data) : null;
    }

    remove() {
        localStorage.removeItem(this.key);
    }
}
```

## 4. Implementación en Todo App

### Estructura de Datos Mejorada

```javascript
// Modelo de datos para las tareas
class TodoList {
    constructor() {
        this.storage = new Storage('todos');
        this.todos = this.storage.load() || [];
    }

    addTodo(text) {
        const todo = {
            id: Date.now(),
            text,
            completed: false,
            createdAt: new Date().toISOString()
        };
        
        this.todos.push(todo);
        this.save();
        return todo;
    }

    removeTodo(id) {
        this.todos = this.todos.filter(todo => todo.id !== id);
        this.save();
    }

    toggleTodo(id) {
        const todo = this.todos.find(todo => todo.id === id);
        if (todo) {
            todo.completed = !todo.completed;
            this.save();
        }
    }

    save() {
        this.storage.save(this.todos);
    }

    getTodos() {
        return this.todos;
    }

    getCompletedTodos() {
        return this.todos.filter(todo => todo.completed);
    }

    getPendingTodos() {
        return this.todos.filter(todo => !todo.completed);
    }
}
```

### Implementación de la Interfaz

```javascript
// Controlador de la UI
class TodoUI {
    constructor(todoList) {
        this.todoList = todoList;
        this.form = document.getElementById('todo-form');
        this.input = document.getElementById('todo-input');
        this.list = document.getElementById('todo-list');
        
        this.setupEventListeners();
        this.renderTodos();
    }

    setupEventListeners() {
        this.form.addEventListener('submit', (e) => {
            e.preventDefault();
            const text = this.input.value.trim();
            if (text) {
                this.addTodo(text);
                this.input.value = '';
            }
        });

        this.list.addEventListener('click', (e) => {
            const li = e.target.closest('li');
            if (!li) return;

            const id = Number(li.dataset.id);

            if (e.target.classList.contains('delete-btn')) {
                this.removeTodo(id);
            } else if (e.target.type === 'checkbox') {
                this.toggleTodo(id);
            }
        });
    }

    addTodo(text) {
        const todo = this.todoList.addTodo(text);
        this.renderTodo(todo);
    }

    removeTodo(id) {
        this.todoList.removeTodo(id);
        document.querySelector(`[data-id="${id}"]`).remove();
    }

    toggleTodo(id) {
        this.todoList.toggleTodo(id);
    }

    renderTodo(todo) {
        const li = document.createElement('li');
        li.dataset.id = todo.id;
        li.innerHTML = `
            <input type="checkbox" ${todo.completed ? 'checked' : ''}>
            <span class="${todo.completed ? 'completed' : ''}">${todo.text}</span>
            <button class="delete-btn">Eliminar</button>
        `;
        this.list.appendChild(li);
    }

    renderTodos() {
        this.list.innerHTML = '';
        this.todoList.getTodos().forEach(todo => this.renderTodo(todo));
    }
}

// Inicialización
const todoList = new TodoList();
const todoUI = new TodoUI(todoList);
```

### CSS Actualizado

```css
.completed {
    text-decoration: line-through;
    color: #888;
}

.todo-list li {
    display: flex;
    align-items: center;
    padding: 10px;
    margin: 5px 0;
    background-color: #f9f9f9;
    border-radius: 4px;
}

.todo-list li:hover {
    background-color: #f0f0f0;
}

.delete-btn {
    margin-left: auto;
    padding: 5px 10px;
    background-color: #ff4444;
    color: white;
    border: none;
    border-radius: 3px;
    cursor: pointer;
}

.delete-btn:hover {
    background-color: #cc0000;
}
```

## Recursos Adicionales

- [MDN - Arrays](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [MDN - Objects](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [MDN - Web Storage API](https://developer.mozilla.org/es/docs/Web/API/Web_Storage_API)
- [JavaScript.info - Objects](https://javascript.info/object)
- [JavaScript.info - Arrays](https://javascript.info/array)
