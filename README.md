# Trabajo en grupo: FreeCAD (conv. extraordinaria)

El objetivo del trabajo en grupo es realizar un programa en Python que genere de forma automática un modelo 3D en FreeCAD, generado mediante un sistema L como el que aparece en la figura 1.25 de [este documento](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf).

Con las siguientes características:

* Debe poder especificarse el tamaño inicial del segmento internodal, el ángulo delta y el número de iteraciones.
* Deben poder especificarse el sistema L a generar utilizando el mismo vocabulario que utiliza la figura referida arriba.
* Debe ir acompañado de un archivo `README.md` que describe qué ha hecho cada miembro del grupo y cómo funciona el programa.

Puede realizarse como una aplicación independiente que importa el módulo `FreeCAD` o como una macro. En cualquier caso el funcionamiento debe estar correctamente descrito en el archivo `README.md`.

## Sistemas L

La definición formal de un sistema L (ver pág. 4 del documento) es como sigue:

> Sea V un alfabeto, V* el conjunto de todas las palabras construidas con el alfabeto V y V+ el conjunto de todas las palabras no vacías construidas con el alfabeto V.  Un sistema L determinasta y libre de contexto se define como una tripleta G = (V, w, P) donde V es el alfabeto, w es una palabra de V+ denominada axioma y P es un subconjunto de V x V* que conforma el conjunto de producciones del sistema.  Una producción (a, X) se escribe con la notación a --> X.  Si para una letra a' no existe una producción a' --> X' se asume la producción identidad a' --> a'

### Derivación de palabras

La operación básica de los sistemas L es la derivación de palabras.  Partiendo del axioma se sustituyen cada una de las letras por la correspondiente palabra del conjunto de producciones.  Por ejemplo:

V: {F, f, +, -}
w: F-F-F-F
P: F --> F-F+F+FF-F-F+F

En la primera iteración (n=1) sustituiríamos las letras F de w por as que se indican en P.

Inicial:

> F-F-F-F

Resultado de la derivación (n=1):

> F-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F 

En la siguiente iteración volveríamos a sustituir todas las F del resultado anterior por la secuencia que se indica en P.

Resultado de la derivación (n=2):

> F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F+F-F+F+FF-F-F+FF-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F+F-F+F+FF-F-F+FF-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F+F-F+F+FF-F-F+FF-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F+F-F+F+FF-F-F+FF-F+F+FF-F-F+F-F-F+F+FF-F-F+F-F-F+F+FF-F-F+F+F-F+F+FF-F-F+F 

### Interpretación geométrica

Probablemente la interpretación más conocida de los sistemas L es la geométrica.  A cada símbolo del vocabulario se le proporciona un significado que tiene que ver con el control de un lápiz, de forma similar a como se hace en la tortuga de Logo, o la biblioteca `turtle` de Python.  Su extensión a 3D está basada en esta interpretación pero es algo más compleja (ver sección 1.5 del [capítulo](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf)).

El control de la tortuga o lápiz se hace ahora en relación a tres vectores unitarios (**H**, **L**, **U**) que indican la orientación.  El vocabulario debe, por tanto, representar los cambios de orientación en cada sentido de cada uno de los ejes. Se hace simplemente multiplicando por matrices de rotación **Rh**, **Rl** y **Ru** (ver página 19 del [capítulo](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf)).

La ramificación se suele realizar mediante la ayuda de una pila en los sistemas L con corchetes (*bracketed L-systems*).  Un símbolo especial `[` se utiliza para guardar el estado del lápiz o tortuga en la pila (posición, orientación, diámetro de segmento, color) y otro símbolo `]` se utiliza para recuperar el último estado guardado en la pila.

Además, se dispone de otros tres símbolos que describen morfológicamente la planta. Mediante una `F` se representa un segmento internodal (*internode*), mediante una `L` se representa una hoja y mediante una `A` se representa un ápice.

Para la representación gráfica se empleará la letra `F` que ya hemos comentado, como cilindro de un grosor determinado y la letra `f` para representar un segmento.  Varios de estos segmentos se pueden componer en un polígono utilizando llaves para agruparlos (`{` y `}`).

Por último, consideraremos en el vocabulario símbolos para cambiar el grosor y el color.  Mediante el símbolo `!` se puede decrementar el diámetro de los siguientes segmentos internodales.  Mediante el símbolo `'` se puede cambiar al siguiente color de una tabla predefinida.

Dado un ángulo constante *delta*, éste es el vocabulario completo que debe contemplar.

Símbolo | Cambios de orientación
--------|------------
\+ |  Aplicar la rotación **Ru(delta)**
\- |  Aplicar la rotación **Ru(-delta)**
&  |  Aplicar la rotación **Rl(delta)**
^  |  Aplicar la rotación **Rl(-delta)**
\\ |  Aplicar la rotación **Rh(delta)**
/  |  Aplicar la rotación **Rh(-delta)**
\|  |  Aplicar la rotación **Ru(pi)**


Símbolo | Estructuras de ramificación y composición
--------|------------
[ | Mete el estado del lápiz en la pila
] | Recupera el último estado del lápiz de la pila 
{ | Empieza a definir un polígono
} | Termina de definir un polígono

Símbolo | Elementos morfológicos de la planta
--------|------------
F | Segmento internodal
L | Hoja
A | Ápice (puede no representarse)

Símbolo | Elementos gráficos
--------|------------
F | Cilindro (segmento internodal)
f | Segmento de un polígono
! | Reduce el diámetro de los siguientes segmentos
' | Cambia el color actual por el siguiente de una tabla


## Competencias a desarrollar

Este trabajo, como indica la guía, es un trabajo autónomo, no dirigido.  Pretende desarrollar las competencias de trabajo en grupo, así como competencias complementarias como el dominio de una lengua extranjera.  También pretende desarrollar los aspectos relacionados con análisis y síntesis en la competencia básica de la asignatura.

Por tanto el problema es relativamente abierto y los alumnos deben tomar sus propias decisiones.  No existe una única respuesta posible a cada decisión.  Por otro lado, el trabajo de un ingeniero debe ser sistemático, debe seguir un sistema.  Eso debería quedar reflejado en la memoria del trabajo (archivo `README.md`).

El problema planteado claramente incluye elementos muy diferentes que deberían facilitar la división del trabajo.  La derivación de palabras y la interpretación de las cadenas del sistema L no tienen nada que ver con el estudio de FreeCAD y de cómo combinar mallas cilíndricas y poligonales, así como del coloreado.

Como consejos generales se recomienda:

- Hacer una lectura general del [capítulo](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf) y del [FreeCAD scripting tutorial](https://wiki.freecadweb.org/Python_scripting_tutorial).
- Diseñar en grupo la arquitectura global del sistema.
- Repartir los módulos entre los integrantes del grupo.  Más de dos personas por módulo no suele tener ningún beneficio.
- Ejecutar el trabajo de forma muy incremental con reuniones semanales para planificar el siguiente paso.

## Documentación complementaria

* [Documentación de FreeCAD](https://wiki.freecadweb.org/)
* [Graphical modeling using L-systems](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf). Primer capítulo del libro *The Algorithmic Beauty of Plants*, Springer-Verlag 1990. Todo el libro se puede descargar de [aquí](http://algorithmicbotany.org/papers/#abop) aunque no es necesario para la práctica.
* [Documentación de Python](https://docs.python.org/3/)
