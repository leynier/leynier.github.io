# Ejemplo de como utilizar Inferfuzzy

Como ejemplo se utilizará el siguiente problema.

Se desea inferir el por ciento de la cantidad de un determinado producto que se ha vendido en un día en un restaurante, cafetería, etc.

Por ejemplo, el producto *Pollo*, se desea conocer bajo determinadas condiciones que por ciento del *Pollo* sacado del almacén dispuesto para venderse ese día se termina vendiendo.

Para la implementación se seleccionaron `4` variables lingüísticas. Las primeras `3` de entrada y la última de salida.

1. Cantidad de platos o derivados del producto que se vende. Por ejemplo, retomando el ejemplo del *Pollo*, si se vendería *Pollo Frito* y *Pollo Asado*, la variable valdría `2`. A esta variable le llamaremos `variety`.
    * Baja: `low <= 2`. Función de Membresía: Z
    * Normal: `1 <= normal <= 5`. Función de Membresía: Gaussiana
    * Alta: `high >= 4`. Función de Membresía: S
2. Por ciento que representa la variable `variety` del total de platos o derivados de productos que se vende. Por ejemplo, si se vende *Pollo Frito*, *Pollo Asado*, *Pescado* y *Cerdo* la variable valdría `50`. A esta variable se le llamará `diversity`.
    * Baja: `low >= 70`. Función de Membresía: Gamma
    * Normal: `40 <= normal <= 80`. Función de Membresía: Lambda
    * Alta: `high <= 50`. Función de Membresía: L
3. Por ciento de la utilización del local, si es `100` es que el local siempre está lleno, si es `0` es que no asiste ningún cliente al establecimiento. A esta variable se le llamará `clients`.
    * Baja: `low <= 40`. Función de Membresía: L
    * Normal: `30 <= normal <= 90`. Función de Membresía: Lambda
    * Alta: `high >= 80`. Función de Membresía: Gamma
4. Por ciento de la cantidad del producto que se vendió en el día, si es `100` fue se vendió todo al final del día, si es `50` fue que no se vendió la mitad de la cantidad. A esta variable se le llamará `sales`.
    * Baja: `low <= 60`. Función de Membresía: L
    * Normal: `30 <= normal <= 90`. Función de Membresía: Lambda
    * Alta: `high >= 90`. Función de Membresía: Gamma

## Declaración de las variables lingüísticas y sus conjuntos difusos en Inferfuzzy

```python
variety_var = Var("variety")
variety_var += "low", ZMembership(1, 2)
variety_var += "normal", GaussianMembership(3, 2)
variety_var += "high", SMembership(4, 6)

diversity_percent_var = Var("diversity")
diversity_percent_var += "low", GammaMembership(70, 100)
diversity_percent_var += "normal", LambdaMembership(40, 60, 80)
diversity_percent_var += "high", LMembership(30, 50)

clients_percent_var = Var("clients")
clients_percent_var += "low", LMembership(20, 40)
clients_percent_var += "normal", LambdaMembership(30, 60, 90)
clients_percent_var += "high", GammaMembership(80, 100)

sales_percent_var = Var("sales")
sales_percent_var += "low", LMembership(20, 60)
sales_percent_var += "normal", LambdaMembership(30, 60, 90)
sales_percent_var += "high", GammaMembership(90, 100)
```

## Gráficos de pertenencia de los conjuntos por cada variable

![Gráfico de la variable `variety`](images/var_variety.png)

![Gráfico de la variable `diversity`](images/var_diversity.png)

![Gráfico de la variable `clients`](images/var_clients.png)

![Gráfico de la variable `sales`](images/var_sales.png)

## Reglas de Inferencia

| variety | diversity | clients |  sales |
|:-------:|:---------:|:-------:|:------:|
|   low   |    low    |   low   |   low  |
|   low   |    low    |  normal | normal |
|   low   |    low    |   high  |  high  |
|   low   |   normal  |   low   |   low  |
|   low   |   normal  |  normal |   low  |
|   low   |   normal  |   high  | normal |
|   low   |    high   |   low   |   low  |
|   low   |    high   |  normal |   low  |
|   low   |    high   |   high  | normal |
|  normal |    low    |   low   |   low  |
|  normal |    low    |  normal | normal |
|  normal |    low    |   high  |  high  |
|  normal |   normal  |   low   |   low  |
|  normal |   normal  |  normal | normal |
|  normal |   normal  |   high  | normal |
|  normal |    high   |   low   |   low  |
|  normal |    high   |  normal |   low  |
|  normal |    high   |   high  | normal |
|   high  |    low    |   low   |   low  |
|   high  |    low    |  normal | normal |
|   high  |    low    |   high  |  high  |
|   high  |   normal  |   low   |   low  |
|   high  |   normal  |  normal |   low  |
|   high  |   normal  |   high  |  high  |
|   high  |    high   |   low   |   low  |
|   high  |    high   |  normal |   low  |
|   high  |    high   |   high  | normal |

## Declaración de las Reglas de Inferencia en Inferfuzzy

```python
mamdani = MamdaniSystem(
    defuzz_func=centroid_defuzzification,
)
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("normal")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("high")
), sales_percent_var.into("high")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("normal")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("high")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("normal")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("low")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("high")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("normal")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("high")
), sales_percent_var.into("high")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("normal")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("high")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("normal")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("normal")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("high")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("normal")
), sales_percent_var.into("normal")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("low")
    & clients_percent_var.into("high")
), sales_percent_var.into("high")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("normal")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("normal")
    & clients_percent_var.into("high")
), sales_percent_var.into("high")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("low")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("normal")
), sales_percent_var.into("low")
mamdani += (
    variety_var.into("high")
    & diversity_percent_var.into("high")
    & clients_percent_var.into("high")
), sales_percent_var.into("normal")
```

De manera análoga sería para Larsen utilizando la clase `LarsenSystem`.

## Resultados

```bash
Variety Value: 10
Diversity Percent: 50
Clients Percent: 50
Mamdani: 35.11%
Larsen 32.82%

Variety Value: 2
Diversity Percent: 100
Clients Percent: 100
Mamdani: 96.22%
Larsen 100.00%

Variety Value: 4
Diversity Percent: 40
Clients Percent: 100
Mamdani: 60.00%
Larsen 60.00%
```

### Análisis de los Resultados

De los resultados, se puede observar que los métodos de Mamdani y Larsen obtienen resultados similares. A primera vista no es posible validar si los resultados se asemejan a la realidad, para esto es imprescindible la colaboración de un experto en el tema para la correcta definición de las variables, la asignación de las funciones de membresía más correctas así́ como la definición de las reglas asociadas.
