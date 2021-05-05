---
title: (poquitas) Experiencias aprendiendo a programar en LISP
layout: page
lang: es
---

Recientemente (¡rayos!, se supone que terminaría esta entrada hace cuatro días) se celebró el Festival Latinoamericano de Instalación de Software Libre[^0] (FLISoL) con sede en el Rancho Electrónico[^1] de la CeDeMequis donde tuve la idea de participar junto con [Diego](https://github.com/umoqnier) compartiendo la [charla](https://youtu.be/RtjIwGFmBJ4?t=3605) (y un pequeño taller) _Experiencias aprendiendo a programar en LISP (o cómo comenzar a preocuparte por balancear tus paréntesis)_ (la cual se preparó unas horas antes de su presentación), lo que sigue es un intento por plasmar parte de lo que vagamente se dijo en esa charla, específicamente la parte de las experiencias. 

## ¿Qué rayos es LISP?

**LIS**t **P**rocessor es un sistema de programación desarrollado por John McCarthy. Si bien en un inicio no era necesariamente un lenguaje de programación, conforme ganó exposición las personas comenzaron a crear lenguajes de programación basados en el documento presentado por McCarthy en 1960 _Recursive Functions of Symbolic Expressionsand Their Computation by Machine_ [^2] (estas fueron las primeras _familias_ o _dialectos_ de LISP). Hoy en día las familias de LISP más populares son Common Lisp, Scheme, Racket, Guile, Emacs Lisp, Julia, entre muchas otras.

Creo que no tengo mucho que compartir en este aspecto, pero definitivamente revisaría la [wiki de LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) para leer un poco del aspecto histórico.

## ¿Por qué aprender LISP?

Hace (casi) 4 años que Gomezcaña lo mencionó, dijo que _si quería aprender a programar debería de revisar LISP_. Ahora sé que el lenguaje apenas influye en el proceso de "aprender a programar", sin embargo, LISP te hace pensar de manera diferente a lo habitual. Quizá usando el siguiente problema como ejemplo todo sea un poco más claro:

**Ejercicio 1.3**[^3]: Define un procedimiento que toma tres números como argumentos y devuelve la suma de los cuadrados de los dos números más grandes.

---
```ruby
def procedimiento (x, y, z)
  return [x, y, z].max(2).map{|n| n*n}.reduce(:+)
end
```
---

Intentando desempolvar lo poco que sé de Ruby llegué a este código (con ayuda de StackOverflow, claro, no recordaba cómo usar los operadores `map` y `reduce`, sobre todo esa cosa rara `:`, que si no me equivoco, representa a los _objetos iterativos_ por sí mismos). Desglosando un poco el código tenemos:

La primera y última línea únicamente se encargan de definir el cuerpo del procedimiento (función), denotando que recibe tres argumentos, `x`, `y` y `z`.

La línea con el operador `return` hace exactamente lo que pide el problema, es decir, `[x, y, z]` es un arreglo creado con los tres argumentos que recibe `procedimiento`, `.max(2)` selecciona los dos números más grandes para que `.map{|n| n*n}` multiplique cada uno de ellos por sí mismo (o sea elevar al cuadrado) y finalmente `.reduce(:+)` pasa a sumar cada uno de los dos números en el arreglo, para así devolver el resultado. Compacto pero algo esotérico...cierto?.

_Y en la otra esquina, damas y damos_...

---
```scheme
(define (cuadrado x)
  (* x x))

(define (suma-de-cuadrados x y)
  (+ (cuadrado x) (cuadrado y)))

(define (devuelve-mayor x y)
  (if (> x y) x y))

(define (procedimiento x y z) 
  (suma-de-cuadrados (devuelve-mayor x y) 
                     (devuelve-mayor y z)))
```
---

Usando Scheme escribí esta solución (sí, la primera vez que intenté resolver este ejercicio, hace casi un año, tuve un error), cuatro veces más lineas que en Ruby, sin embargo, no hubo una búsqueda en StackOverflow, pues, el desarrollo de cada una de las funciones fue algo _"intuitivo"_ (a lo sumo erré en preceder el paréntesis izquierdo en los argumentos en lugar del nombre de la función) y _"natural"_.

A lo que me refiero es que desde **mi** punto de vista, la solución en Scheme muestra una gran sencillez, incluso para aquellas personas que no están relacionadas con la programación. Pero, la razón principal por la que considero que el proceso de solución es más comprensivo se debe a que el `procedimiento` se crea desde cero, esto incluye desarrollar cada una de las funciones de apoyo, a diferencia de Ruby donde se sigue un desarrollo meramente estructurado, empleando métodos que apenas podía hacer funcionar.

## Experiencias

### Libros fallidos

![If you ask me...](/assets/eaplm-1.jpg)

El seminario de LISP vio sus inicios intentando leer Structure and Interpretation of Computer Programs (SICP), (un libro con un gran renombre en la comunidad, debido a que durante algún tiempo fue el texto predilecto para enseñar fundamentos de programación, modularidad, recursividad, etc., en el MIT), durante poco más de un mes leímos las primeras secciones de este, sin embargo, el libro despegó bastante rápido, dejando huecos en el aprendizaje, razón por la que decidimos posponer su lectura y comenzar inmediatamente con The Little Schemer.

> No quiero decir que no hay que leer este libro, más bien, considerar su lectura para el futuro (si es que vamos comenzando en el mundo de LISP).

### Notación prefija

Incluso después de casi un año el uso de notación prefija sigue siendo un confusa en ocasiones, principalmente con los operadores aritméticos y de relación (o mejor dicho, en todas las operaciones _no conmutativas_). En LISP (generalmente) el orden de los argumentos sí importa, pues no es lo mismo evaluar `(> 1 2)` a `(> 2 1)`, ya que equivalen a `1 > 2` y `2 > 1` respectivamente, de aquí que no les sorprenda ver errores de este tipo en el código que puedo llegar a escribir. Claro que existen excepciones como las funciones `+` o `*`.

### Recursión

La recursión es uno de los aspectos fundamentales y característicos de LISP, sin embargo, sigue sorprendiéndome cada ocasión en la que logramos resolver algún ejercicio siguiendo esta metodología.

Para quienes van empezando, recursión en términos muy informales, es realizar una y otra vez un procedimiento dentro del mismo procedimiento. 🤯. Confuso, lo sé. Mejor tomemos esta función como ejemplo:

---
```lisp
(defun cuenta-rebanadas (lista-de-rebanadas)
  (cond
    ((null lista-de-rebanadas) 0)
    (t (+ 1
          (cuenta-rebanadas (cdr lista-de-rebanadas))))))
```
---

La primera línea define la función `cuenta-rebanadas` y los argumentos que recibe, en este caso una único argumento en forma de lista (`lista-de-rebanadas`).

Le sigue una macro `cond` que es  algo similar a las estructuras `if ... else` en otros lenguajes, la tercera línea contiene el predicado `(null lista-de-rebanadas)` que pregunta si `lista-de-rebanadas` es vacía (`()` o `NIL`), si es cierto se devuelve el número `0` porque recibimos una lista sin elementos.

Finalmente, en las últimas dos líneas es donde encontramos la _"llamada recursiva"_. En caso de que `lista-de-rebanadas` no sea vacía, asumimos que debe de contener al menos una rebanada, así que con el símbolo `t` (verdadero) representamos el equivalente a la cláusula `else`, donde se realiza una suma `(+ 1 NUM-DESCONOCIDO)` donde `NUM-DESCONOCIDO` es el resultado que nos devolverá la llamada recursiva a la misma función `cuenta-rebanadas` con el argumento `(cdr lista-de-rebanadas)`, es decir `lista-de-rebanadas` menos el primer elemento, de tal forma que `(cdr '(r r))` retorna la lista `(r)`... (¡Que locura!, ¿cierto?, quizá me emociono demasiado, pero me parece muy interesante resolver problemas así).

Quizá con ejemplos sea un poquito más comprensible:

```lisp
(cuenta-rebanadas '())
```

Devolverá el número `0` porque `lista-de-rebanadas` es la lista vacía, sin elementos.

```lisp
(cuenta-rebanadas '(x))
```
Después de verificar que `(x)` no es una lista vacía se suma uno al resultado que devuelva `(cuenta-rebanadas (cdr '(x)))`, que se convierte en `(cuenta-rebanadas '())` el cual es el caso anterior, por lo que se nos devuelve `0` y se suma `1` a este. Representado de otra forma:

```lisp
(+ 1 (cuenta-rebanadas (cdr '(x))))
(+ 1 (cuenta-rebanadas '()))
(+ 1 0)
=> 1
```

🤔...

```lisp
(cuenta-rebanadas '(rebanada rebanada)
```
Similarmente para este caso, donde tendremos algo parecido a `(+ 1 (+ 1 0))`, que se traduce en `(+ 1 1) ; => 2`.

La misma idea se sigue para cualquier número de rebanadas entero positivo. Bueno... Suficiente recursión (por ahora).

## Conclusiones

Parece mentira que un año pasó, incluso, me es difícil creer que la primera ocasión en la que intenté aprender LISP todo resultó ser esotérico e inaccesible, a pesar de esto el camino ha sido interesante, lento, pero muy fructífero. Definitivamente recomiendo al menos una vez en la vida se debería de aprender LISP, sólo por la mera curiosidad o placer mental al resolver problemas. (:

### ¿Por dónde empiezo a aprender LISP?

Si estas leyendo esto, intenta unirte al seminario de lisp[^4], a la fecha de esta publicación la Facultad de Ingeniería de la UNAM ha detenido actividades debido a la situación de pagos para académicas y académicos (la UNAM no paga, mucho menos aún en la pandemia, es lo que entiendo de la situación), nos reunimos miércoles y sábado de 19:00 a 21:00 (UTC-5) en la sala de jitsi **seminariolisplidsol**, puedes encontrar más detalles en el repositorio o en la página de [LIDSoL](https://lidsol.org) (el laboratorio que organiza el seminario).

Ahora, basándonos en experiencias podemos recomendar (no necesariamente en orden):

* **A Gentle Introduction to Symbolic Computation**, excelente punto de partida.
* **Practical Common Lisp** (en la lista de libros pendientes).
* **The (Little Seasoned Reasoned) Schemer**, la trilogía explora conceptos interesantes, sobre todo en recursión.
* **How to Design Programs** (también en la lista).
* **Structure and Interpretation of Computer Programs** (SICP) (igual está en los pendientes).

La mayoría (¿casi todos?) pueden encontrarse en [Library Genesis](https://libgen.is/) o [Z-Library](https://z-lib.org/).

Desafortunadamente (como muchos textos en computación) los libros anteriores sólo se encuentran en inglés, a excepción de las (pocas) notas del seminario[^4].

## Enlaces

[^0]: https://flisol.info/
[^1]: https://ranchoelectronico.org/
[^2]: http://www-formal.stanford.edu/jmc/recursive.pdf
[^3]: https://sarabander.github.io/sicp/html/
[^4]: https://gitlab.com/lidsol/seminario-lisp/
