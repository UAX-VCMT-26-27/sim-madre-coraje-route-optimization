# Madre Coraje | Route Optimization

![Course](https://img.shields.io/badge/course-SIM-blue)
![Year](https://img.shields.io/badge/year-2026-lightgrey)
![Data](https://img.shields.io/badge/data-synthetic-orange)

Proyecto académico de optimización de rutas de recogida y logística inversa aplicado al caso de la Fundación Madre Coraje.

## 1. Contexto

Madre Coraje es una organización sin ánimo de lucro que desarrolla proyectos de cooperación internacional, acción social y educación. Una parte importante de sus recursos procede de la recogida, reutilización y reciclaje de materiales como ropa y aceite de cocina usado.

En este caso, los equipos asumirán el papel del departamento de operaciones y datos de la delegación de Jerez de la Frontera, con el objetivo de mejorar su sistema de recogida de residuos.

Para ello, se trabajará con una red ficticia formada por:

- 60 contenedores de textil.
- 30 contenedores de aceite.
- 14 barrios.
- Una base logística situada en Guadalcacín.
- Un camión de recogida de textil.
- Una furgoneta de recogida de aceite.

Actualmente, la recogida se realiza mediante rutas fijas organizadas por sectores. Este sistema provoca que algunos contenedores se desborden antes de ser recogidos, mientras que otros se visitan cuando todavía están prácticamente vacíos.

Se pretende sustituir este sistema por una planificación dinámica que permita determinar qué contenedores deben recogerse, en qué momento y en qué orden.

## 2. Objetivos

El objetivo principal del proyecto es diseñar, implementar y evaluar una solución que optimice las rutas de recogida de textil y aceite, considerando las restricciones operativas y económicas del problema.

Los objetivos específicos son:

- Analizar el funcionamiento del sistema actual de recogida.
- Identificar patrones de llenado de los contenedores.
- Diseñar un sistema de selección de contenedores.
- Desarrollar un modelo de estimación del llenado.
- Formular matemáticamente el problema de optimización de rutas.
- Implementar un algoritmo de resolución.
- Evaluar y validar los resultados obtenidos.
- Comparar los costes de los diferentes escenarios.
- Realizar un análisis de sensibilidad.
- Desarrollar una interfaz que permita visualizar y utilizar la solución.

## 3. Enunciado

El enunciado completo del caso se encuentra en el directorio [`docs/assignment/`](docs/assignment/).

> Se recomienda leer detenidamente el documento antes de comenzar el desarrollo, ya que contiene las condiciones del problema, las restricciones, los parámetros operativos y los requisitos de la entrega.

## 4. Escenarios de trabajo

### Escenario 1 — Contenedores con sensores

Se dispone de sensores que proporcionan diariamente el porcentaje de llenado de cada contenedor.

A partir de las lecturas disponibles, los equipos deberán determinar:

- Qué contenedores deben recogerse.
- Cuáles pueden esperar hasta la siguiente recogida.
- Cómo organizar los viajes de cada vehículo.
- En qué orden deben visitarse los contenedores.
- Cuál es el coste de la operación.

Las lecturas anteriores podrán utilizarse para estudiar el comportamiento y el ritmo de llenado de cada contenedor.

### Escenario 2 — Contenedores sin sensores

En este escenario no se dispone de información directa sobre el nivel de llenado de los contenedores.

Los equipos deberán desarrollar un modelo que permita estimar el llenado a partir del histórico de recogidas.

El modelo deberá considerar, entre otros aspectos:

- Ritmos de llenado.
- Estacionalidad.
- Tendencias temporales.
- Datos ausentes.
- Datos censurados.
- Contenedores con poco histórico.
- Incertidumbre de las estimaciones.

Las lecturas de los sensores no podrán utilizarse para construir, entrenar o ajustar el modelo de este escenario. Únicamente podrán emplearse posteriormente para comprobar la calidad de las estimaciones obtenidas.

### Fecha de planificación

Ambos escenarios deberán resolver la planificación correspondiente al **lunes 23 de noviembre de 2026**.

| Tipo de residuo | Fecha de planificación | Siguiente oportunidad de recogida |
|---|---|---|
| Textil | 23/11/2026 | 25/11/2026 |
| Aceite | 23/11/2026 | 30/11/2026 |

## 5. Datos disponibles

Los conjuntos de datos originales se encuentran en el directorio [`data/raw/`](data/raw/).

| Archivo | Descripción | Escenario |
|---|---|---|
| `contenedores.csv` | Inventario de contenedores y base logística. | 1 y 2 |
| `historico_recogidas.csv` | Histórico de visitas y recogidas realizadas. | 2 y diagnóstico |
| `lecturas_sensores.csv` | Lecturas diarias de los sensores de llenado. | 1 y comprobación posterior del 2 |
| `matriz_distancias.csv` | Distancias entre los puntos de la red. | 1 y 2 |

### 5.1. Inventario de contenedores

**Archivo:** [`contenedores.csv`](data/raw/contenedores.csv)

Contiene la información de los 90 contenedores y de la base logística.

| Variable | Descripción |
|---|---|
| `id` | Identificador único del contenedor o de la base. |
| `tipo` | Tipo de residuo: `TEX`, `ACE` o `BASE`. |
| `barrio` | Barrio en el que se encuentra el contenedor. |
| `ubicacion` | Tipo de ubicación del contenedor. |
| `lat` | Latitud. |
| `lon` | Longitud. |
| `capacidad_kg` | Capacidad útil en kilogramos. |
| `zona_restringida` | Indica si existen restricciones horarias de acceso. |
| `sector_actual` | Sector asignado en el sistema actual de recogida. |
| `fecha_alta` | Fecha de instalación del contenedor. |

El identificador `BASE` corresponde a la base logística de Guadalcacín.

### 5.2. Histórico de recogidas

**Archivo:** [`historico_recogidas.csv`](data/raw/historico_recogidas.csv)

Contiene 3.689 visitas realizadas entre el 4 de noviembre de 2024 y el 20 de noviembre de 2026.

Cada fila representa una visita a un contenedor.

| Variable | Descripción |
|---|---|
| `fecha` | Fecha de la recogida. |
| `id` | Identificador del contenedor. |
| `tipo` | Tipo de residuo. |
| `kg_recogidos` | Kilogramos recogidos durante la visita. |
| `estado_observado` | Estado del contenedor en el momento de la recogida. |
| `dias_desde_ultima` | Días transcurridos desde la recogida anterior. |

Consideraciones:

- Los pesos ausentes corresponden a visitas en las que la báscula no registró el peso.
- Cuando un contenedor está desbordado, los kilogramos registrados no representan necesariamente todo el material depositado.
- Los intervalos entre recogidas pueden variar debido a festivos y cancelaciones.
- Algunos contenedores se instalaron durante el periodo analizado y disponen de menos observaciones.
- Todos los contenedores existentes al inicio de la simulación se vaciaron el 1 de noviembre de 2024.

### 5.3. Lecturas de sensores

**Archivo:** [`lecturas_sensores.csv`](data/raw/lecturas_sensores.csv)

Contiene las lecturas diarias de los sensores entre el 28 de septiembre y el 23 de noviembre de 2026, realizadas a las 06:00.

| Variable | Descripción |
|---|---|
| `fecha_hora` | Fecha y hora de la lectura. |
| `id` | Identificador del contenedor. |
| `tipo` | Tipo de residuo. |
| `nivel_pct` | Porcentaje de llenado registrado. |

Los sensores presentan un margen de error de ±4 puntos porcentuales. Aproximadamente un 2 % de las lecturas diarias no llega.

Una lectura ausente no implica que el contenedor esté vacío.

### 5.4. Matriz de distancias

**Archivo:** [`matriz_distancias.csv`](data/raw/matriz_distancias.csv)

Contiene las distancias, expresadas en kilómetros, entre todos los puntos de la red, incluyendo los contenedores y la base logística.

Las distancias se han calculado aplicando un factor de 1,35 a la distancia en línea recta.

Este factor ya está incorporado en los datos y no debe aplicarse nuevamente.

### 5.5. Naturaleza de los datos

Los datos operativos utilizados en este proyecto son sintéticos y han sido generados con fines exclusivamente docentes.

Las coordenadas, ubicaciones, tasas de llenado y costes simulados no representan necesariamente la operación real de Madre Coraje.

Los archivos originales deberán conservarse sin modificaciones. Cualquier transformación, limpieza o procesamiento deberá realizarse mediante código reproducible.

## 6. Parámetros operativos

### Vehículos y contenedores

| Parámetro | Textil | Aceite |
|---|---:|---:|
| Capacidad del contenedor | 220 kg | 130 kg |
| Capacidad del vehículo | 1.400 kg | 600 kg |
| Coste por kilómetro | 0,42 € | 0,26 € |
| Tiempo de vaciado | 6 min | 4 min |
| Tiempo de descarga en la base | 20 min | 15 min |
| Coste por desbordamiento | 30 € | 20 € |

### Parámetros generales

| Parámetro | Valor |
|---|---|
| Velocidad media | 25 km/h |
| Coste del conductor | 21 €/hora |
| Inicio de jornada | 08:00 |
| Fin de jornada | 15:00 |
| Horario de acceso a la zona centro | 08:00–11:00 |
| Recogida dinámica de textil | Lunes, miércoles y viernes |
| Recogida dinámica de aceite | Lunes |

### Sensores

| Parámetro | Valor |
|---|---:|
| Coste de instalación por sensor | 165 € |
| Coste mensual por sensor | 1,80 € |
| Vida útil | 5 años |
| Precisión | ±4 puntos porcentuales |
| Lecturas diarias ausentes | 2 % |

## 7. Restricciones del problema

La solución desarrollada deberá respetar las siguientes condiciones:

1. **Capacidad:** ningún viaje podrá superar la carga útil del vehículo.
2. **Jornada:** los vehículos deberán salir de la base a las 08:00 y regresar, habiendo descargado, antes de las 15:00.
3. **Ventanas horarias:** los contenedores situados en la zona centro únicamente podrán atenderse entre las 08:00 y las 11:00.
4. **Nivel de servicio:** se deberá minimizar el riesgo de que los contenedores se desborden antes de la siguiente oportunidad de recogida.
5. **Prioridad:** cuando no sea posible recoger todos los contenedores seleccionados, deberán aplazarse los menos urgentes e identificarse en los resultados.

Cada vehículo podrá realizar varios viajes durante una misma jornada, regresando a la base para descargar antes de iniciar un nuevo viaje.

## 8. Desarrollo del proyecto

El trabajo deberá abordar los siguientes bloques:

### 8.1. Diagnóstico del sistema actual

Analizar el histórico correspondiente al año 2025 y calcular, para cada tipo de residuo:

- Número de visitas.
- Kilogramos recogidos.
- Porcentaje de visitas a contenedores casi vacíos.
- Porcentaje de desbordamientos.
- Kilómetros aproximados recorridos.
- Coste por kilogramo recogido.

### 8.2. Optimización con sensores

Diseñar una regla de selección de contenedores y formular matemáticamente el problema de optimización de rutas, incluyendo:

- Variables de decisión.
- Función objetivo.
- Restricciones.
- Método de resolución.
- Justificación del algoritmo seleccionado.
- Resultados de la planificación.

### 8.3. Estimación sin sensores

Desarrollar y validar un algoritmo de estimación del llenado basado en el histórico de recogidas.

La validación deberá respetar la separación temporal indicada en el enunciado:

| Conjunto | Periodo |
|---|---|
| Entrenamiento | Hasta el 31/08/2026 |
| Validación | Recogidas posteriores al 31/08/2026 |

Se deberá evaluar el error medio en kilogramos, los desbordamientos anticipados y las falsas alarmas.

### 8.4. Comparación económica

Comparar los escenarios con y sin sensores, considerando:

- Costes de recogida.
- Costes de desplazamiento.
- Costes de personal.
- Desbordamientos esperados.
- Costes de instalación y mantenimiento de sensores.
- Proyección económica anual.

Se deberá justificar si resulta conveniente sensorizar todos los contenedores, únicamente una parte de ellos o ninguno.

### 8.5. Producto e interfaz

Desarrollar una interfaz que permita utilizar y visualizar la solución obtenida.

La interfaz deberá incluir:

- Mapa de contenedores.
- Representación del nivel de llenado medido o estimado.
- Visualización de las rutas diferenciadas por viajes.
- Selector de escenario.
- Selector de tipo de residuo.
- Indicadores operativos y económicos.
- Desbordamientos esperados.
- Hoja de ruta descargable para el conductor.

La hoja de ruta deberá mostrar el orden de visita, el identificador del contenedor, el barrio, la hora estimada de llegada y los kilogramos esperados.

También deberá considerarse cómo adaptar la solución a una red logística de mayor tamaño.

### 8.6. Análisis de sensibilidad

Evaluar cómo afectan los cambios en los parámetros del problema a las decisiones obtenidas.

Se deberá identificar al menos un parámetro cuyo cambio pueda modificar la decisión final y determinar el valor a partir del cual se produce dicho cambio.

## 9. Estructura inicial del repositorio

```text
sim-madre-coraje-route-optimization/
├── data/
│   └── raw/
│       ├── contenedores.csv
│       ├── historico_recogidas.csv
│       ├── lecturas_sensores.csv
│       └── matriz_distancias.csv
├── docs/
│   └── assignment/
│       └── ...
├── .gitattributes
├── .gitignore
└── README.md
```

Este repositorio proporciona únicamente la estructura inicial, el enunciado y los datos necesarios para resolver el caso.

Cada equipo será responsable de diseñar y ampliar la estructura de su propio proyecto.

Se recomienda separar adecuadamente el código fuente, los datos procesados, las configuraciones, los experimentos, las pruebas, los resultados y la interfaz de usuario.
