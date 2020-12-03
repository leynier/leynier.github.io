# Características del Sistema de Inferencia

La biblioteca contiene implementados los métodos de inferencia *Mamdani* y *Larsen*. Pero es posible implementar partiendo de una base común otros métodos de inferencia.

Los métodos de inferencia reciben una función de *defuzzificación*. La biblioteca contiene implementadas *Centroide*, *Bisectriz*, *Máximo Central*, *Máximo más pequeño* y *Máximo más grande*.

Durante el proceso de definición de los conjuntos difusos esto requieren una función de membresía que puede ser implementada o utilizar una de las disponibles en la biblioteca.

Funciones de membresía implementadas en **Inferfuzzy**:

* Función Gamma
* Función Lambda o Triangular
* Función Pi o Trapezoidal
* Función S
* Función Z
* Función Gaussiana

La *T-conorm* y *T-norm* utilizadas en las reglas de inferencia, así como el método de agregación de los conjuntos son posibles de sobrescribir, por defecto, son *mínimo*, *máximo* y *máximo* respectivamente.

Es posible definir más de una variable de salida para el sistema de inferencia difusa implementado en la biblioteca.
