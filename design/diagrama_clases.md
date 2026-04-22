# Diagrama de clases — Chef-Costos (Corte 2)

Modelo **solo de clases de dominio**: **tres jerarquías** (cada una con **tres subclases**), encapsulación con atributos `__` y acceso mediante **getters/setters**

---

## Diagrama principal

```mermaid
---
title: Chef-Costos — clases de negocio (POO)
---
classDiagram
    direction TB

    %% Jerarquía 1: Insumos
    class Ingrediente {
        <<entity>>
        -__id
        -__nombre
        -__precio_base
        -__precio_mercado
        +__init__(nombre, precio_base, precio_mercado, identificador)
        +get_id()
        +set_id(valor)
        +get_nombre()
        +set_nombre(valor)
        +get_precio_base()
        +set_precio_base(valor)
        +get_precio_mercado()
        +set_precio_mercado(valor)
        +categoria_codigo()
        +factor_merma_sugerido()
        +mercado_supera_umbral()
    }

    class IngredienteProteina {
        +categoria_codigo()
        +factor_merma_sugerido()
    }
    class IngredienteCarbohidrato {
        +categoria_codigo()
        +factor_merma_sugerido()
    }
    class IngredienteVerdura {
        +categoria_codigo()
        +factor_merma_sugerido()
    }

    %% Jerarquía 2: Carta
    class Plato {
        <<entity>>
        -__id
        -__nombre
        -__margen_ganancia
        +__init__(nombre, margen_ganancia, identificador)
        +get_id()
        +set_id(valor)
        +get_nombre()
        +set_nombre(valor)
        +get_margen_ganancia()
        +set_margen_ganancia(valor)
        +tipo_plato_codigo()
        +tiempo_preparacion_minutos_referencia()
    }

    class PlatoEntrada {
        +tipo_plato_codigo()
        +tiempo_preparacion_minutos_referencia()
    }
    class PlatoPrincipal {
        +tipo_plato_codigo()
        +tiempo_preparacion_minutos_referencia()
    }
    class PlatoPostre {
        +tipo_plato_codigo()
        +tiempo_preparacion_minutos_referencia()
    }

    %% Jerarquía 3: Trazabilidad
    class RegistroOperacion {
        <<audit>>
        -__fecha_hora
        -__descripcion
        -__referencia_id
        -__monto_relacionado
        +__init__(descripcion, referencia_id, monto_relacionado)
        +get_fecha_hora()
        +get_descripcion()
        +set_descripcion(valor)
        +get_referencia_id()
        +set_referencia_id(valor)
        +get_monto_relacionado()
        +set_monto_relacionado(valor)
        +codigo_tipo()
        +resumen()
    }

    class RegistroAltaIngrediente {
        +codigo_tipo()
        +resumen()
    }
    class RegistroCosteoPlato {
        +codigo_tipo()
        +resumen()
    }
    class RegistroAlertaPrecio {
        +codigo_tipo()
        +resumen()
    }

    Ingrediente <|-- IngredienteProteina
    Ingrediente <|-- IngredienteCarbohidrato
    Ingrediente <|-- IngredienteVerdura

    Plato <|-- PlatoEntrada
    Plato <|-- PlatoPrincipal
    Plato <|-- PlatoPostre

    RegistroOperacion <|-- RegistroAltaIngrediente
    RegistroOperacion <|-- RegistroCosteoPlato
    RegistroOperacion <|-- RegistroAlertaPrecio

    note for Ingrediente "Regla MVP: alerta si precio_mercado > precio_base × 1.20 (constantes del módulo)."
    note for Plato "Margen por defecto 0.35; cada subclase define tipo y tiempo de preparación de referencia."
```

---

## Asociación conceptual: plato e ingrediente en la receta

Relación de negocio (un plato se compone de varios ingredientes con una cantidad por línea).

```mermaid
classDiagram
    direction LR
    class Plato
    class Ingrediente
    class LineaReceta {
        <<entity>>
        -__cantidad
        +get_cantidad()
        +set_cantidad(valor)
    }
    Plato "1" --> "*" LineaReceta : compone
    Ingrediente "1" --> "*" LineaReceta : aporta
```

En el código actual del taller, las líneas de receta se persisten en tablas; la clase `LineaReceta` es **opcional** como evolución del modelo de objetos.

---

## Leyenda

| Símbolo / estereotipo | Significado |
|----------------------|-------------|
| `<<entity>>` | Entidad de negocio con estado encapsulado |
| `<<audit>>` | Registro / trazabilidad de operaciones |
| `<\|--` | Herencia (especialización) |

