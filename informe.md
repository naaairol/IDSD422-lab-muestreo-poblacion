# Informe — Clasificación de variables y población vs. muestra

**Para:** Gerencia de RetailAndina S.A. · **De:** Equipo de Business Intelligence
**Fecha:** 23/10/2026 · **Asunto:** Base de fidelización: tipos de variables y estimación por muestreo

---

## Resumen ejecutivo

Comprobé cada una de las variables registradas en la base de fidelización, además de clasificar a que tipo pertenece cada una; numérica continua/discreta, categórica binaria/ordinal/nominal. Quise estimar cual es el mejor método de muestreo entre: conveniencia, aleatorio simple y estratificado. La gerencia debe de conocer que método permite obtener la muestra que realmente representa a la población.

## Parte A — Clasificación de las variables


| Variable | Tipo | Justificación (criterio) | Verificación en código |
|---|---|---|---|
| `cliente_id` | categórica ordinal | Es un identificador del cliente | object |
| `edad` | numérica discreta | Se mide en años enteros | int64 |
| `numero_hijos` | numérica discreta | No es posible describirlo con decimales | int64 |
| `genero` | categórica nominal | Hombre/Mujer/Otro. No puede ser binario | object |
| `nivel_educacion` | categórica ordinal | Existe jerarquía | object |
| `ciudad` | categórica nominal | No existe orden natural entre ciudades | object |
| `ingresos_mensuales` | numérica continua | Es posible indicarlo con decimales y así se hace | int64 |
| `satisfaccion` | categorica ordinal | Mide percepción mediante una escala numérica | int64 |

Como en la columna 'satisfacion' solo se manejan números, era obvio obtener int64 tras aplicar dtype, sin embargo, esto no basta para clasificarlo pues ignora el significado real de la columna. Esto se lo puede ver al asignar una 'etiqueta' a cada número. Sigue expresando la satisfacción del cliente, pero no es operable; no puedo decir que 2 ('insatisfecho'), sea el doble de 4 ('satisfecho').

Por otro lado, si el orden no es declarado en una columna que mantiene datos categóricos ordinales, el análisis se vuelve impreciso. Al quererlas ordenar, sucedería de forma alfabetic. Rompiendo la lógica.

## Parte B — Población vs. muestra

### La población y su parámetro

| Concepto | Valor |
|---|---|
| Población (N) | 400 |
| Variable de interés | ingresos mensuales |
| Parámetro poblacional (media) | ingresos mensuales (1330.72 USD) |

Se aplica muestreo porque no siempre es posible contar con el parámetro, en este caso, los ingresos mensuales de toda la población. Para una población mucho más grande resulta lento, costoso e inviable.

### Los tres diseños

| Diseño | n | Media estimada (USD) | Composición por ciudad | Diferencia vs. parámetro |
|---|---|---|---|---|
| Conveniencia (`head`) | 20 | 1348.50 | Quito: 20 | +17.78 |
| Aleatorio simple (`sample`) | 20 | 1336.00 | Cuenca: 8, Quito: 8, Guayaquil: 4 | +5.28 |
| Estratificado (`groupby` + 5 %) | 20 | 1287.50 | Quito: 10, Guayaquil: 6, Cuenca: 4  | -43.22 |

A continuación incluyo también otras medias obtenidas mediante el método de muestreo aleatorio simple, obviamente, cada resultado con un diferente conjunto aleatorio.

| Semilla | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| Media | 1336.00 | 1405.00 | 1235.00 | 1491.50 | 1288.00 |

**P2.** ¿Cuánto vale el parámetro poblacional y cuánto estimó cada diseño? ¿Cuál quedó más cerca y por qué eso **no** alcanza para declarar un ganador?

El parámetro vale 1330.72 USD, sin embargo, no todos los muestreos quedaron cerca de ese valor. El más proximo fue el muestreo aleatorio simple utilizando seed = 1. Pero, esto pudo ser simplemente una coincidencia, de forma que no podría ser replicable.

**P3.** La muestra por conveniencia también tiene 20 filas. ¿Qué defecto tiene y cómo lo delatas en la composición por ciudad? ¿Se corrige tomando más filas?

La muestro por conveniencia considera los registros a medida que van llegando, en este caso, eso provoca que la ciudad de la que provienen todos los clientes sea Quito; no se consideran clientes de otras ciudades. Existe un alto sesgo. Aumentar el número de filas no corrige el problema pues solo ampliaría los registros de esa ciudad.

**P4.** El sorteo aleatorio dio un número distinto en cada semilla. ¿Qué implica esa variabilidad para una empresa que va a decidir con un solo estudio?

En este caso se distingue la 'mejor' semilla pues se conoce la media poblacional, sin embargo, no se puede dejar al azar. Si una empresa toma sus decisiones con la primera semilla que obtuvo, las decisiones que se puedan tomar con ese análisis pueden estar totalmente errados.

**P5.** ¿Cuándo usarías cada diseño en la práctica? Relaciona cada uno con un uso real (piloto, subgrupos que deben aparecer, conclusión sobre toda la población).

Utilizaría el muestreo por conveniencia si se tratara de una prueba piloto, algo muy rápido para tener una idea general. El muestreo estratificado lo utilizaría cuando sea necesario incluir secciones fundamentales de la población para el análisis. Y, finalmente, el muestreo aleatorio simple lo usaría sobre poblaciones homogéneas o en el caso de requerir una conclusión rápida.

## Conclusiones

Por últmo, aprendí que lo importante en una muestra no es el tamaño, sino como está diseñada y si, realmente, representa de manera adecuada a la población.

Finalmente, mi recomendación para la gerencia sería acompañar el análisis con su ficha técnica. De forma que también es posible estar al tanto de que tan confiable fue el análisis propuesto.