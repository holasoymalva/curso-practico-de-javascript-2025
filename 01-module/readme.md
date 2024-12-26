# Módulo 1: Fundamentos de JavaScript y Configuración del Entorno

## Introducción

Bienvenido al primer módulo del Curso Práctico de JavaScript. En esta sección, estableceremos las bases necesarias para comenzar a programar en JavaScript y configuraremos nuestro entorno de desarrollo.

## Contenido

- [1. ¿Qué es JavaScript?](#1-qué-es-javascript)
- [2. Configuración del Entorno](#2-configuración-del-entorno)
- [3. Variables y Tipos de Datos](#3-variables-y-tipos-de-datos)
- [4. Operadores](#4-operadores)
- [5. Control de Flujo](#5-control-de-flujo)

## 1. ¿Qué es JavaScript?

JavaScript es un lenguaje de programación interpretado, orientado a objetos y de alto nivel. Es uno de los tres pilares del desarrollo web junto con HTML y CSS.

### Características principales:
- Lenguaje dinámico
- Tipado débil
- Multiplataforma
- Orientado a objetos
- Basado en prototipos
- Interpretado (Just-in-time compilation)

### ¿Dónde se ejecuta JavaScript?
```javascript
// En el navegador (Frontend)
console.log("Hola desde el navegador");

// En el servidor (Backend con Node.js)
console.log("Hola desde Node.js");
```

## 2. Configuración del Entorno

### Git y GitHub

1. Instala Git desde [git-scm.com](https://git-scm.com/)
2. Configura tu identidad en Git:
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

### Node.js y npm

1. Instala Node.js desde [nodejs.org](https://nodejs.org/)
2. Verifica la instalación:
```bash
node --version
npm --version
```

### Visual Studio Code

1. Instala VS Code desde [code.visualstudio.com](https://code.visualstudio.com/)
2. Extensiones recomendadas:
   - Live Server
   - JavaScript (ES6) code snippets
   - ESLint
   - Prettier

## 3. Variables y Tipos de Datos

### Declaración de Variables

```javascript
// Usando let (recomendado para variables que cambiarán)
let edad = 25;

// Usando const (para valores que no cambiarán)
const PI = 3.14159;

// var (no recomendado en código moderno)
var nombre = "Juan";
```

### Tipos de Datos

```javascript
// Números
let entero = 42;
let decimal = 3.14;

// Strings (cadenas de texto)
let nombre = "María";
let apellido = 'González';
let presentacion = `Hola, soy ${nombre}`; // Template literal

// Booleanos
let esMayorDeEdad = true;
let estaCansado = false;

// Undefined
let variableSinDefinir;
console.log(variableSinDefinir); // undefined

// Null
let valorNulo = null;

// Symbol
let simbolo = Symbol('descripción');

// BigInt
let numeroGrande = 9007199254740991n;

// Objetos
let persona = {
    nombre: "Ana",
    edad: 28
};

// Arrays
let colores = ["rojo", "verde", "azul"];
```

## 4. Operadores

### Operadores Aritméticos
```javascript
let a = 5;
let b = 3;

console.log(a + b);  // Suma: 8
console.log(a - b);  // Resta: 2
console.log(a * b);  // Multiplicación: 15
console.log(a / b);  // División: 1.666...
console.log(a % b);  // Módulo: 2
console.log(a ** b); // Exponenciación: 125
```

### Operadores de Comparación
```javascript
console.log(5 == "5");   // true (comparación débil)
console.log(5 === "5");  // false (comparación estricta)
console.log(5 != "5");   // false
console.log(5 !== "5");  // true
console.log(5 > 3);      // true
console.log(5 >= 5);     // true
console.log(3 < 5);      // true
console.log(3 <= 3);     // true
```

### Operadores Lógicos
```javascript
let esAdulto = true;
let tieneIdentificacion = false;

console.log(esAdulto && tieneIdentificacion);  // AND: false
console.log(esAdulto || tieneIdentificacion);  // OR: true
console.log(!esAdulto);                        // NOT: false
```

## 5. Control de Flujo

### Condicionales

#### if...else
```javascript
let edad = 18;

if (edad >= 18) {
    console.log("Eres mayor de edad");
} else if (edad >= 13) {
    console.log("Eres adolescente");
} else {
    console.log("Eres menor de edad");
}
```

#### switch
```javascript
let dia = "Lunes";

switch (dia) {
    case "Lunes":
        console.log("Inicio de semana");
        break;
    case "Viernes":
        console.log("¡Por fin viernes!");
        break;
    default:
        console.log("Otro día de la semana");
}
```

### Bucles

#### for
```javascript
// Bucle tradicional
for (let i = 0; i < 5; i++) {
    console.log(i); // 0, 1, 2, 3, 4
}

// for...of (para arrays)
let frutas = ["manzana", "pera", "uva"];
for (let fruta of frutas) {
    console.log(fruta);
}

// for...in (para objetos)
let persona = { nombre: "Juan", edad: 25 };
for (let propiedad in persona) {
    console.log(`${propiedad}: ${persona[propiedad]}`);
}
```

#### while y do...while
```javascript
// while
let contador = 0;
while (contador < 3) {
    console.log(contador);
    contador++;
}

// do...while
let numero = 1;
do {
    console.log(numero);
    numero++;
} while (numero <= 3);
```

## Ejercicio Práctico

Crear un programa que simule una calculadora básica:

```javascript
function calculadora(operacion, a, b) {
    switch (operacion) {
        case 'suma':
            return a + b;
        case 'resta':
            return a - b;
        case 'multiplicacion':
            return a * b;
        case 'division':
            if (b === 0) {
                return "No se puede dividir por cero";
            }
            return a / b;
        default:
            return "Operación no válida";
    }
}

// Ejemplos de uso
console.log(calculadora('suma', 5, 3));         // 8
console.log(calculadora('resta', 10, 4));       // 6
console.log(calculadora('multiplicacion', 3, 4)); // 12
console.log(calculadora('division', 15, 3));     // 5
console.log(calculadora('division', 8, 0));      // "No se puede dividir por cero"
```

## Recursos Adicionales

- [MDN Web Docs - JavaScript](https://developer.mozilla.org/es/docs/Web/JavaScript)
- [JavaScript.info](https://javascript.info/)
- [Node.js Docs](https://nodejs.org/docs)
- [Git Documentation](https://git-scm.com/doc)
