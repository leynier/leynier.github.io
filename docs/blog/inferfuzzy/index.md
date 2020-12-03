# Inferfuzzy

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Test](https://github.com/leynier/inferfuzzy/workflows/CI/badge.svg)](https://github.com/leynier/inferfuzzy/actions?query=workflow%3ACI)
[![Version](https://img.shields.io/pypi/v/inferfuzzy?color=%2334D058&label=Version)](https://pypi.org/project/inferfuzzy)
[![Last commit](https://img.shields.io/github/last-commit/leynier/inferfuzzy.svg?style=flat)](https://github.com/leynier/inferfuzzy/commits)
[![GitHub commit activity](https://img.shields.io/github/commit-activity/m/leynier/inferfuzzy)](https://github.com/leynier/inferfuzzy/commits)
[![Github Stars](https://img.shields.io/github/stars/leynier/inferfuzzy?style=flat&logo=github)](https://github.com/leynier/inferfuzzy/stargazers)
[![Github Forks](https://img.shields.io/github/forks/leynier/inferfuzzy?style=flat&logo=github)](https://github.com/leynier/inferfuzzy/network/members)
[![Github Watchers](https://img.shields.io/github/watchers/leynier/inferfuzzy?style=flat&logo=github)](https://github.com/leynier/inferfuzzy)
[![Website](https://img.shields.io/website?up_message=online&url=https%3A%2F%2Fleynier.github.io/inferfuzzy)](https://leynier.github.io/inferfuzzy)
[![GitHub contributors](https://img.shields.io/github/contributors/leynier/inferfuzzy)](https://github.com/leynier/inferfuzzy/graphs/contributors)

**Inferfuzzy** es una biblioteca de **Python** para implementar **Sistemas de Inferencia Difusa**.

## Empezando

### Instalación

```bash
pip install inferfuzzy
```

### Uso

Creación de variables lingüísticas y sus conjuntos difusos asociados.

```python
variable_1 = Var("variable_name_1")
variable_1 += "set_name_1", ZMembership(1, 2)
variable_1 += "set_name_2", GaussianMembership(3, 2)
variable_1 += "set_name_3", SMembership(4, 6)

variable_2 = Var("variable_name_2")
variable_2 += "set_name_4", GammaMembership(70, 100)
variable_2 += "set_name_5", LambdaMembership(40, 60, 80)
variable_2 += "set_name_6", LMembership(30, 50)
```

Declarar las reglas semánticas y el método de inferencia a utilizar.

```python
mamdani = MamdaniSystem(defuzz_func=centroid_defuzzification)
mamdani += variable_1.into("set_name_1") | variable_1.into("set_name_3"), variable_2.into("set_name_5")
mamdani += variable_1.into("set_name_2"), variable_2.into("set_name_4")
```

Usando el método de inferencia difusa para valores ingresados por el usuario.

```python
variable_1_val = float(input())
mamdani_result: float = mamdani.infer(variable_name_1=variable_1_val)["variable_name_2"]
```
