# Jerga de la Programación Funcional

La programación funcional ofrece muchas ventajas, de ahí el auge de su popularidad. Sin embargo, cada paradigma de programación trae consigo una jerga única, y la programación funcional no es la excepción. Al proveer un glosario, creemos que podemos ayudar a aprender programación funcional de una manera más fácil.

Los ejemplos presentados están escritos en JavaScript (ES2015). [Por qué JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

_Este es un trabajo en progreso; siéntase libre en enviar PR._

__Traducciones__

 * [Inglés](https://github.com/hemanth/functional-programming-jargon)
 * [Portugués](https://github.com/alexmoreno/jargoes-programacao-funcional)

__Tabla de Contenido__
<!-- RM(noparent,notop) -->

* [Aridad](#aridad)
* [Funciones de Orden Superior (FOS)](#funciones-de-orden-superior-fos)
* [Aplicación Parcial](#aplicaci%C3%B3n-parcial)
* [Currying](#currying)
* [Auto Currying](#auto-currying)
* [Composición de Funciones](#composici%C3%B3n-de-funciones)
* [Pureza](#pureza)
* [Efectos secundarios](#efectos-secundarios)
* [Idempotente](#idempotente)
* [Estilo Point-Free](#estilo-point-free)
* [Predicado](#predicado)
* [Contratos](#contratos)
* [Guarded Functions](#guarded-functions)
* [Categorías](#categor%C3%ADas)
* [Valor](#valor)
* [Constante](#constante)
* [Funtor](#funtor)
* [Pointed Funtor](#pointed-funtor)
* [Transparencia referencial](#transparencia-referencial)
* [Razonamiento Ecuacional](#razonamiento-ecuacional)
* [Lambda](#lambda)
* [Cálculo lambda](#c%C3%A1lculo-lambda)
* [Evaluación perezosa](#evaluaci%C3%B3n-perezosa)
* [Monoide](#monoide)
* [Mónada](#m%C3%B3nada)
* [Co-mónada](#co-m%C3%B3nada)
* [Funtor aplicativo](#funtor-aplicativo)
* [Semigrupo](#semigrupo)
* [Firma de tipo](#firma-de-tipo)
* [Tipo de unión](#tipo-de-uni%C3%B3n)
* [Producto de tipo](#producto-de-tipo)
* [Opción](#opci%C3%B3n)
* [Librerías en JavaScript de programación funcional](#librer%C3%ADas-en-javascript-de-programaci%C3%B3n-funcional)


<!-- /RM -->

## Aridad

El número de argumentos que acepta una función. Derivada de unaria, binaria, ternaria, etc… Esta palabra tiene la distinción de estar compuesta de dos sufijos, “-aria” y “-idad”. Por ejemplo, la suma, acepta dos argumentos, y por lo tanto se define como una función binaria o una función de aridad dos. De igual manera, una función que acepta un número variable de argumentos es llamada de aridad variable.

```js
const suma = (a, b) => a + b

const aridad = suma.length
console.log(aridad) // 2

// La aridad de la suma es 2
```


## Funciones de Orden Superior (FOS)

Función que acepta una función como argumento y/o retorna otra función.

```js
const filtro = (predicado, xs) => {
  const resultado = []
  for (let idx = 0; idx < xs.length; idx++) {
    if (predicado(xs[idx])) {
      resultado.push(xs[idx])
    }
  }
  return resultado
}
```

```js
const es = (tipo) => (x) => Object(x) instanceof tipo
```

```js
filtro(es(Number), [0, '1', 2, null]) // [0, 2]
```

## Aplicación Parcial

Aplicar parcialmente una función significa crear una nueva función al llamar la función original fijando algunos de sus argumentos.


```js
// Helper para crear funciones aplicadas parcialmente
// Acepta una función y algunos argumentos
const parcial = (f, ...args) =>
  // retorna una función que acepta el resto de los argumentos
  (...masArgs) =>
    // y llama la función original con todos ellos
    f(...args, ...masArgs)

// Algo para aplicar
const suma3 = (a, b, c) => a + b + c

// Aplicar parcialmente `2` y `3` a `sumar3` retorna una función que acepta un argumento
const cincoMas = parcial(suma3, 2, 3) // (c) => 2 + 3 + c

cincoMas(4) // 9
```

También podemos utilizar `Function.prototype.bind` para aplicar parcialmente una función en JS:

```js
const suma1mas = suma3.bind(null, 2, 3) // (c) => 2 + 3 + c
```

La aplicación parcial permite crear funciones simples a partir de funciones complejas a medida que uno va obteniendo datos. La funciones [“currying”](#currying) son parcialmente aplicada de manera automática.

## Currying

Es el proceso de convertir una función que acepta múltiples argumentos a una función que acepta dichos argumentos uno a la vez.

Cada vez que la función es invocada solo acepta un solo argumento y retorna otra función que a su vez acepta un solo argumento hasta que todos los argumentos hayan sido pasados.

```js
const suma = (a, b) => a + b

const sumaCurried = (a) => (b) => a + b

sumaCurried(40)(2) // 42.

const suma2 = sumaCurried(2) // (b) => 2 + b

suma2(10) // 12

```

## Auto Currying

Transformar una función que acepta múltiples argumentos en una que al recibir menos argumentos que el número total que acepta, retorna una función que acepta el resto de los argumentos. Cuando la función recibe el número correcto de argumentos, entonces es evaluada.

Underscore, lodash, y ramda tienen una función `curry` que funciona de esta manera.

```js
const suma = (x, y) => x + y

const sumaCurried = _.curry(suma)
sumaCurried(1, 2) // 3
sumaCurried(1) // (y) => 1 + y
sumaCurried(1)(2) // 3
```

__Lecturas Adicionales (en inglés)__

 * [Favoring Curry](http://fr.umio.us/favoring-curry/)
 * [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Composición de Funciones

El acto de unir dos funciones para formar una tercera función donde la salida de una función es la entrada de la otra.

```js
const componer = (f, g) => (a) => f(g(a)) // Definición
const redondearYtoString = componer((val) => val.toString(), Math.floor) // Uso
redondearYtoString(121.212121) // '121'
```

## Pureza

Una función es pura si el valor de retorno está solamente determinado por sus valores de entrada, y no produce efectos secundarios.

```js
const saludar = (nombre) => 'Hola ' + nombre

saludar('Amelie') // 'Hola Amelie'

```

Contrario a:

```js

let saludo

const saludar = () => {
  saludo = 'Hola, ' + window.name
}

saludar() // "Hola Amelie"

```

## Efectos secundarios

Se dice que una función o expresión tiene efectos secundarios si aparte de retornar un valor, esta interactúa (lee o escribe) con algún estado mutable externo.

```js
const diferenteCadaVez = new Date()
```

```js
console.log('E/S (entrada y salida) es un efecto secundario!')
```

## Idempotente

Una función es idempotente cuando al ser invocada con su resultado no produce un resultado diferente.

```
f(f(x)) ≍ f(x)
```

```js
Math.abs(Math.abs(10))
```

```js
sort(sort(sort([2, 1])))
```

## Estilo Point-Free

Escribir funciones donde su definición no  identifica explícitamente los argumentos usados. Este estilo usualmente requiere [currying](#currying) u otras [Funciones de Orden Superior (FOS)](#funciones-de-orden-superior-fos). También conocido como Programación Tácita.

```js
// Dado
const map = (fn) => (lista) => lista.map(fn)
const suma = (a) => (b) => a + b

// Entonces

// No es points-free - `números` es un argumento explicito
const incrementarTodos = (números) => map(suma(1))(números)

// Es Points-free - la lista de números es un argumento implícito
const incrementarTodos2 = map(suma(1))
```

`incrementarTodos` identifica y usa el parámetro `números`, por lo tanto no es point-free.  `incrementarTodos2` esta escrita mediante la simple combinación de funciones y valores, sin hacer mención de sus argumentos. Por lo tanto __es__ points-free.

Las definiciones de funciones escritas utilizando el estilo Point-free se ven justo como las asignaciones normales sin la utilización de `function` o `=>`.


## Predicado

Un predicado es una función que retorna verdadero o falso para un valor dado. Un uso común de un predicado es en el callback del método filter de un arreglo.

```js
const predicado = (a) => a > 2

;[1, 2, 3, 4].filter(predicado) // [3, 4]
```

## Contratos

Pendiente

## Guarded Functions

Pendiente

## Categorías

Objetos con funciones asociadas que siguen ciertas reglas.


## Valor

Todo lo que puede ser asignado a una función.

```js
5
Object.freeze({nombre: 'John', edad: 30}) // La función `freeze` hace que el objeto sea inmutable.
;(a) => a
;[1]
undefined
```


## Constante

Una variable que no puede ser reasignada una vez definida.

```js
const cinco = 5
const juan = {nombre: 'Juan', edad: 30}
```

Las constantes poseen transparencia referencial. Esto quiere decir que pueden ser reemplazadas por el valor que representan sin afectar el resultado.

Con las dos constantes de arriba la siguiente expresión siempre retornara `true`.

```js
juan.edad + cinco === ({name: 'Juan', edad: 30}).edad + (5)
```

## Funtor

Un objeto que implementa una función `map` la cual, mientras se ejecuta sobre cada valor del objeto para producir un nuevo objeto, se adhiere a dos reglas:

```js
// preserva la identidad
object.map(x => x) === object
```

y

```js
// composición
object.map(x => f(g(x))) === object.map(g).map(f)
```

(Sea `f`, `g` funciones arbritarias)

Un funtor muy común en JavaScript es `Array` ya que cumple las dos reglas de funtor:

```js
[1, 2, 3].map(x => x) // = [1, 2, 3]
```

y

```js
const f = x => x + 1
const g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]
```
## Pointed Funtor

Un objeto con una función `of` que acepta cualquier valor y lo pone dentro.

ES2015 introduce `Array.of` haciendo que los arreglos sean “pointed funtor”.

```js
Array.of(1) // [1]
```

<!--
## Lift

Lifting is when you take a value and put it into an object like a [functor](#pointed-functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

Some implementations have a function called `lift`, or `liftA2` to make it easier to run functions on functors.

```js
const liftA2 = (f) => (a, b) => a.map(f).ap(b)

const mult = a => b => a * b

const liftedMult = liftA2(mult) // this function now works on functors like array

liftedMult([1, 2], [3]) // [3, 6]
liftA2((a, b) => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```

Lifting a one-argument function and applying it does the same thing as `map`.

```js
const increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```
-->

## Transparencia referencial

Una expresión que puede ser reemplazada por su valor sin alterar el comportamiento del programa se dice que es referencialmente transparente.

Digamos que tenemos la función saludar:

```js
const saludar = () => 'Hola Mundo!'
```

Cualquier invocación de `saludar()` puede ser reemplazada por `Hola Mundo!` , por lo tanto `saludar` es referencialmente transparente.


##  Razonamiento Ecuacional

Cuando una aplicación esta compuesta de expresiones y es libre de efectos secundarios, verdades acerca del sistema pueden ser derivadas a partir de sus partes.


## Lambda

Una función anónima que puede tratarse como un valor.

```js
;(function (a) {
  return a + 1
})

;(a) => a + 1
```

Lambdas a menudo se pasan como argumentos de funciones de Orden Superior.

```js
[1, 2].map((a) => a + 1) // [2, 3]
```

Se puede asignar un lambda a una variables.

```js
const add1 = (a) => a + 1
```

## Cálculo lambda

Una rama de las matemáticas que utiliza funciones para crear un [modelo universal de computación](https://es.wikipedia.org/wiki/Modelo_de_computaci%C3%B3n).

## Evaluación perezosa

La evaluación perezosa es un mecanismo de evaluación llamada por necesidad que retrasa la evaluación de una expresión hasta que su valor sea necesario. En lenguajes funcional, esto permite estructuras de datos como listas infinitas, que normalmente no estarían disponibles en un lenguaje imperativo donde la secuencia de comandos es significativa.

```js
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```js
const randIter = rand()
randIter.next() // Cada ejecución da un valor aleatorio, la expresión se evalúa en necesidad.
```

## Monoide

Un objeto con una función que "combina" ese objeto con otro del mismo tipo.

Un monoide simple es la suma de números:

```js
1 + 1 // 2
```

En este caso el número es el objeto y `+` es la función.

Un valor "identidad" también debe existir, el cual al combinarse con un valor no lo cambia.

El valor identidad de la suma es `0`.

```js
1 + 0 // 1
```

También se requiere que el agrupamiento de operaciones no afecte el resultado (asociatividad):

```js
1 + (2 + 3) === (1 + 2) + 3 // verdadero
```

La concatenación de arreglos también forma un monoide.

```js
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

El valor identidad es un arreglo vacío `[]`

```js
;[1, 2].concat([]) // [1, 2]
```

Si las funciones de identidad y composición son provistas, las funciones en si forman un monoide:

```js
const identidad = (a) => a
const componer = (f, g) => (x) => f(g(x))
```
`foo` es cualquier función que acepte un argumento.
```
componer(foo, identidad) ≍ componer(identidad, foo) ≍ foo
```

## Mónada

Una mónada es un objeto con funciones [`of`](#pointed-functor) y `chain`. `chain` es como [`map`](#funtor) excepto que desanida el objeto anidado resultante.

```js
// Implementación
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Uso
;['cat,dog', 'fish,bird'].chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Contraste de map
;['cat,dog', 'fish,bird'].map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` también se conoce como `return` en otros lenguajes funcional. `chain` también se conoce como `flatmap` y `bind` en otros lenguajes funcional.

## Co-mónada

Un objeto que tiene funciones `extract` y `extend`.

```js
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

`extract` toma un valor de un funtor.

```js
CoIdentity(1).extract() // 1
```

`extend` ejecuta una función en la co-mónada. La función debe devolver el mismo tipo que la co-mónada.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Funtor aplicativo

Un funtor aplicativo es un objeto con una función `ap`. `ap` aplica una función en el objeto a un valor en otro objeto del mismo tipo.

```js
// Implementación
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Ejemplo de uso
;[(a) => a + 1].ap([1]) // [2]
```

Esto es útil si se tiene dos objetos y se quiere aplicar una función binaria a sus contenidos.

```js
// Arreglos que se quieren combinar
const arg1 = [1, 3]
const arg2 = [4, 5]

// función de combinación - debe ser currificada para que funcione
const suma = (x) => (y) => x + y

const sumasParcialmenteAplicadas = [suma].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```
Esto nos da un arreglo de funciones sobre el cual se puede llamar `ap` para obtener el resultado:

```js
sumasParcialmenteAplicadas.ap(arg2) // [5, 6, 7, 8]
```

<!--
## Morphism

A transformation function.


### Endomorphism

A function where the input type is the same as the output.

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as an array `[2,3]` or object `{x: 2, y: 3}`.

```js
// Providing functions to convert in both directions makes them isomorphic.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```



## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```js
Array.prototype.equals = (arr) => {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```
-->

## Semigrupo

Un objeto que posee una función `concat` que lo combina con otro objeto del mismo tipo.

```js
;[1].concat([2]) // [1, 2]
```

<!--
## Foldable

An object that has a `reduce` function that can transform that object into some other type.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Traversable

TODO
-->
## Firma de tipo

A menudo, las funciones en JavaScript incluirán comentarios que indican los tipos de sus argumentos y valores de retorno.

Hay un poco de variación en la comunidad, pero a menudo siguen los siguientes patrones:

```js
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
```

Si la función acepta otra función como argumento, se encierra en parentesis.

```js
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

Las letras `a`, `b`, `c`, `d` se utilizan para indicar que el argumento puede ser de cualquier tipo. La siguiente versión de `map` toma una función que transforma un valor de algún tipo `a` en otro tipo `b`, un arreglo de valores de tipo `a`, y devuelve un arreglo de valores de tipo `b`.

```js
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

__Lectura adicional__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Tipo de unión

Un tipo de unión es la combinación de dos tipos juntos en otro.

JS no tiene tipos estáticos, pero digamos que inventamos un tipo `NumOrString` que es una suma de `String` y `Number`.

El operador `+` en JS funciona en cadenas y números de modo que podemos utilizar este nuevo tipo para describir sus entradas y salidas:

```js
// add :: (NumOrString, NumOrString) -> NumOrString
const add = (a, b) => a + b

add(1, 2) // Devuelve numero 3
add('Foo', 2) // Devuelve cadena "Foo2"
add('Foo', 'Bar') // Devuelve cadena "FooBar"
```
Tipos de unión también se conoce como tipos algebraicos, uniones etiquetadas, o tipos de suma.

Hay un [par](https://github.com/paldepind/union-type) de [bibliotecas](https://github.com/puffnfresh/daggy) en JS que ayuda con definir y uso de tipos de unión.

## Producto de tipo

Un **producto** de tipo combina los tipos de datos de una manera que probablemente usted este más familiarizado:

```js
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({x: x, y: y})
```
Se llama un producto porque el total de los valores posibles de la estructura de datos es el producto de los diferente valores.

Ver también [Teoría de conjuntos](https://es.wikipedia.org/wiki/Teor%C3%ADa_de_conjuntos).

## Opción
Opción es un [tipo de unión](#tipo-de-uni%C3%B3n) con dos casos llamados `Alguno` y `Ninguno`.

Opción es útil para funciones compuestas que pueden no devolver un valor.

```js
// Definición nativa

const Alguno = (v) => ({
  val: v,
  map (f) {
    return Alguno(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

const Ninguno = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Opción a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? Ninguno() : Alguno(obj[key])
```
Use `chain` para secuenciar las funciones que devuelven `Opciones`
```js

// getItem :: Cart -> Opción CartItem
const getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Opción Number
const getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Opción a
const getNestedPrice = (cart) => getItem(obj).chain(getPrice)

getNestedPrice({}) // Ninguno()
getNestedPrice({item: {foo: 1}}) // Ninguno()
getNestedPrice({item: {price: 9.99}}) // Alguno(9.99)
```

`Opción` es también conocido como `Talvez`. `Alguno` es también llamado `Sólo`. `Ninguno` a veces es llamado 'Nada'.

## Librerías en JavaScript de programación funcional

* [Ramda](https://github.com/ramda/ramda)
* [Folktale](http://folktalejs.org)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)

---

__Nota:__ Este repositorio ha sido un éxito gracias a las [contribuciones de los colaboradores](https://github.com/rmariuzzo/jerga-programacion-funcional/graphs/contributors)!
