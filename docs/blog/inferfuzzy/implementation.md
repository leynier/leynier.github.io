# Estructura de la Implementación

La implementación se sostiene sobre 7 clases fundamentales:

* `Membership`
* `BaseSet`
* `BaseVar`
* `BaseRule`
* `Predicate`
* `VarSet`
* `InferenceSystem`

## Membership

Es la clase encargada de representar una función de membresía junto a los puntos (llamados `items` internamente)

```python
class Membership:
    def __init__(self, function: Callable[[Any], Any], items: list):
        self.function = function
        self.items = items

    def __call__(self, value: Any):
        return self.function(value)
```

## BaseSet

Es la clase encargada de representar un conjunto difuso. Recibe como parámetros un objeto de tipo `Membership` representando la función de membresía del conjunto y un método de agregación.

```python
class BaseSet:
    def __init__(
        self,
        name: str,
        membership: Membership,
        aggregation: Callable[[Any, Any], Any],
    ):
        self.name = name
        self.membership = membership
        self.aggregation = aggregation

    def __add__(self, arg: "BaseSet"):
        memb = Membership(
            lambda x: self.aggregation(
                self.membership(x),
                arg.membership(x),
            ),
            self.membership.items + arg.membership.items,
        )
        return BaseSet(
            f"({self.name})_union_({arg.name})",
            memb,
            aggregation=self.aggregation,
        )
```

## BaseVar

Es la clase encargada de representar una variable lingüística. Recibe como parámetros una función de unión, una función de intercepción y una lista de objetos de tipo `BaseSet` representando los conjuntos difusos de la variable.

```python
class BaseVar:
    def __init__(
        self,
        name: str,
        union: Callable[[Any, Any], Any],
        inter: Callable[[Any, Any], Any],
        sets: Optional[List[BaseSet]] = None,
    ):
        self.name = name
        self.sets = {set.name: set for set in sets} if sets else {}
        self.union = union
        self.inter = inter

    def into(self, set: Union[BaseSet, str]) -> VarSet:
        set_name = set.name if isinstance(set, BaseSet) else set
        if set_name not in self.sets:
            raise KeyError(f"Set {set_name} not found into var {self.name}")
        temp_set = self.sets[set_name]
        return VarSet(self, temp_set, self.union, self.inter)
```

## BaseRule

Es la clase encargada de representar una regla de inferencia. Recibe como parámetro un objeto de tipo `Predicate` representando el antecedente de la regla.

```python
class BaseRule:
    def __init__(self, antecedent: Predicate):
        self.antecedent = antecedent

    def __call__(self, values: dict):
        raise NotImplementedError()
```

`BaseRule` no contiene consecuencias porque las consecuencias de todos los tipos de reglas no son de la misma estructura. La clase `Rule` hereda de `BaseRule` y representa las reglas en los que el sistema produce un conjunto o más como resultado.

```python
class Rule(BaseRule):
    def __init__(self, antecedent: Predicate, consequences: List[VarSet]):
        super(Rule, self).__init__(antecedent)
        self.consequences = consequences

    def aggregate(self, set: BaseSet, value: Any) -> BaseSet:
        raise NotImplementedError()

    def __call__(self, values: dict):
        value = self.antecedent(values)
        return {
            consequence.var.name: self.aggregate(
                consequence.set,
                value,
            )
            for consequence in self.consequences
        }
```

## Predicate

Es la clase encargada de representar a los antecedentes. De ella heredan cuatro clases: `AndPredicate`, `OrPredicate`, `NotPredicate` y `VarSet`. Las primeras tres para representar las relaciones lógicas de unión, intercepción y negación; y la última representa la inclusión de una variable en un determinado conjunto, siendo esta la clase básica para representar a los antecedentes.

```python
class Predicate:
    def __init__(
        self,
        union: Callable[[Any, Any], Any],
        inter: Callable[[Any, Any], Any],
    ) -> None:
        self.union = union
        self.inter = inter

    def __call__(self, values: dict):
        raise NotImplementedError()

    def __and__(self, other: "Predicate"):
        return AndPredicate(self, other, self.union, self.inter)

    def __or__(self, other: "Predicate"):
        return OrPredicate(self, other, self.union, self.inter)

    def __invert__(self):
        return NotPredicate(self, self.union, self.inter)
```

### VarSet

```python
class VarSet(Predicate):
    def __init__(
        self,
        var: "BaseVar",
        set: BaseSet,
        union: Callable[[Any, Any], Any],
        inter: Callable[[Any, Any], Any],
    ):
        super(VarSet, self).__init__(union, inter)
        self.var = var
        self.set = set

    def __call__(self, values: dict):
        return self.set.membership(values[self.var.name])
```

## InferenceSystem

Es la clase encargada de representar el sistema de inferencia. Recibe como parámetros las reglas y una función de defuzzificación y con el método `infer` permite realizar la inferencia según los valores proveídos.

```python
class InferenceSystem:
    def __init__(
        self,
        rules: Optional[List[BaseRule]] = None,
        defuzz_func: Optional[Callable[[BaseSet], Any]] = None,
    ):
        self.rules = rules if rules else []
        self.defuzz_func = defuzz_func

    def infer(
        self,
        values: dict,
        defuzz_func: Optional[Callable[[BaseSet], Any]] = None,
    ) -> Dict[str, Any]:
        if not self.rules:
            raise Exception("Empty rules")
        if self.defuzz_func is None and defuzz_func is None:
            raise Exception("Defuzzification not found")
        func = self.defuzz_func if defuzz_func is None else defuzz_func
        set: Dict[str, BaseSet] = self.rules[0](values)
        for rule in self.rules[1:]:
            temp: Dict[str, BaseSet] = rule(values)
            for key in temp:
                set[key] += temp[key]
        result: Dict[str, Any] = {}
        for key in set:
            result[key] = func(set[key])
        return result
```
