# Práctica 06. Búsqueda de patrones

### Objetivos
Los objetivos de esta práctica son: 

* Ejecutar comandos de Linux en la máquina virtual (VM) de la asignatura
* Ser capaz de desarrollar, editar de forma remota usando VSC, compilar y ejecutar programas escritos en C++ en su VM
* Que el alumnado codifique sus programas siguiendo lo estipulado en la Guía de Estilo de código de Google
* Que el alumno sea capaz de formatear su códgo en VSC siguiendo la guia de Estilo de Google
* Que el alumnado utilice la utilidad make y ficheros Makefile en sus proyectos
* Poner en práctica los conocimientos de programación estructurada
* Practicar conocimientos de programación Orientada a Objetos en C++
* Practicar operaciones de entrada/salida (E/S) en ficheros de texto

### Rúbrica de evaluacion de esta práctica
Se señalan a continuación los aspectos más relevantes (la lista no es exhaustiva)
que se tendrán en cuenta a la hora de evaluar esta práctica:

* El alumnado ha de acreditar capacidad para editar ficheros remotos en la VM de la asignautra usando VSC
* El alumnado ha de acreditar conocimientos para trabajar con la shell de GNU/Linux en su VM
* El código ha de estar escrito de acuerdo al estándar de la [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html).
* El programa desarrollado deberá compilarse utilizando la herramienta `make` y un fichero `Makefile`
* El comportamiento del programa debe ajustarse a lo solicitado en este documento.
* Ha de acreditarse capacidad para introducir cambios en el programa desarrollado.
* Modularidad: el programa ha de escribirse de modo que las diferentes funcionalidades que se precisen sean encapsuladas en funciones y/o métodos cuya extensión textual se mantenga acotada.
* El programa ha de ser fiel al paradigma de programación orientada a objetos.

Si el alumnado tiene dudas respecto a cualquiera de estos aspectos, debiera acudir al
foro de discusiones de la asignatura para plantearlas allı́. 
Se espera que, a través de ese foro, el alumnado intercambie experiencias y conocimientos, ayudándose mutuamente
a resolver dichas dudas. 
También el profesorado de la asignatura intervendrá en las discusiones que pudieran suscitarse, si fuera necesario.
    
### Introducción
La búsqueda de patrones (https://es.wikipedia.org/wiki/B%C3%BAsqueda_de_patrones) es un problema muy importante y de gran aplicación práctica en el ámbito de las Ciencias de la Computación. El problema surge cuando se quiere comprobar si un patrón está presente en una determinada secuencia de elementos. A modo de ejemplo, esto suele ocurrir cuando queremos buscar una determinada palabra o cadena de caracteres dentro de un fichero de texto. En muchas herramientas como los editores o procesadores de texto, a menudo se describen los patrones mediante el uso de expresiones regulares. Sin embargo, nosotros nos centraremos en casos en los que el patrón de búsqueda sea una única cadena. Por otro lado, se han diseñado múltiples algoritmos para resolver el problema de la búsqueda de patrones pero, en este caso, aplicaremos un algoritmo de búsqueda de patrones basado en un autómata finito. Para aplicar este algoritmo es necesario construir un autómata finito a partir del patrón. Dicho autómata será luego utilizado para analizar las cadenas de entrada y determinar si en ellas se encuentra o no el patrón buscado.

### Ejercicio práctico

Se debe diseñar un programa que reciba por línea de comandos tres argumentos: el patrón de búsqueda, un fichero de entrada y un fichero de salida:

./pattern_search pattern infile.txt outfile.txt

En pattern especificaremos el patrón de búsqueda, en el fichero de entrada tendremos una línea por cada cadena que queramos analizar en busca del patrón, y en el fichero de salida escribiremos una línea con Sí o No en función de si hemos encontrado el patrón en la cadena correspondiente a esa línea en el fichero de entrada. 
 
Teniendo en cuenta que el conjunto de símbolos que utilizaremos como alfabeto serán las letras minúsculas (sin incluir la ñ), entonces pattern representará una cadena formada por n letras minúsculas. Partiendo de este patrón construiremos un autómata finito que tenga n+1 estados que irán desde q0 hasta qn. En el estado qi se debe reconocer el símbolo que se encuentre en la posición i del patrón de búsqueda (los símbolos de un patrón de búsqueda se enumeran de 0 a n - 1, de izquierda a derecha). En cada estado qi del autómata tendremos dos comportamientos: transitar al estado q_{i + 1} con el símbolo pattern[i] o transitar al estado q0 y parar con cualquier otro símbolo. Cuando esto ocurre se sabe que es porque no se ha detectado el patrón en la subcadena de entrada. En el momento en el que se alcance el estado final q_n sabremos que el patrón ha sido encontrado. 

A modo de ejemplo, si pattern = abcb, deberemos construir un autómata finito con 5 estados, desde q0 a q4. 
Estado 0:  
Con el símbolo a se transita al estado q1
Con cualquier otro símbolo se transita a q0 y se para
Estado 1:  
Con el símbolo b se transita al estado q2
Con cualquier otro símbolo se transita a q0 y se para
Estado 2:  
Con el símbolo c se transita al estado q3
Con cualquier otro símbolo se transita a q0 y se para
Estado 3:  
Con el símbolo b se transita al estado q4
Con cualquier otro símbolo se transita a q0 y se para
Estado 4:  
Si se ha alcanzado este estado es que se ha detectado el patrón

Una vez se haya construido el autómata de reconocimiento, será necesario iniciar el proceso de reconocimiento. Dicho proceso deberá realizarse por cada una de las cadenas de entrada (cada cadena de entrada se encuentra en una línea del fichero de entrada). Dada una cadena de entrada w con m símbolos, el primer análisis de la cadena se realizará desde la posición j = 0. Si al analizar la cadena desde esa posición, y siguiendo las transiciones del autómata, alcanzamos el estado final, entonces podremos indicar en la línea correspondiente del fichero de salida que sí hemos detectado el patrón de búsqueda en la cadena de entrada. Si, por el contrario, el reconocimiento nos lleva a transitar al estado inicial q0 y a parar, tendremos que mover j una posición a la derecha j = j + 1 y reiniciar de nuevo el proceso de reconocimiento. Si hacemos todos los recorridos posibles de la cadena y seguimos sin detectar el patrón, entonces podremos indicar en el fichero de salida que el patrón no ha sido detectado en nuestra cadena de entrada.

Cuando finalicemos el reconocimiento sobre una cadena, ya sea determinando que sí que contiene el patrón buscado o no, pasaremos a realizar el reconocimiento sobre la siguiente cadena especificada en el fichero de entrada. En el caso de que detectemos cadenas con símbolos no pertenecientes a nuestro alfabeto (letras minúsculas) no realizaremos el proceso de reconocimiento; en este caso, escribiremos directamente en la línea correspondiente del fichero de salida la palabra Error.
NOTA: Es importante tener en cuenta que para realizar el proceso de reconocimiento es imprescindible utilizar el algoritmo que se ha detallado en este guión y que se basa en la definición de un autómata finito. Es decir, NO se podrá utilizar los métodos de búsqueda en cadenas de texto que proporciona la clase string ni similares.
 
NOTA!!! Esto no lo tengo claro pero igual deberíamos decirles que el alfabeto de símbolos debería ser un conjunto en el que cada letra se represente por su código ASCII (simplemente añadiendo un offset)... y así de paso usan lo de la práctica anterior…

### Referencias
* [Wikipedia: búsqueda de patrones] (https://es.wikipedia.org/wiki/B%C3%BAsqueda_de_patrones)
* [Standard Template Library](http://www.cplusplus.com/reference/stl/)
* [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) Guía de estilo de código 
