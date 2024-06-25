# **<p align="center"> Documentación TP Final - Desarrollo de un Cubo OLAP </p>**
## _<p align="center"> Algoritmos 1, ECyT UNSAM </p>_
**Autores:** Jeronimo Fernandez, Nicolás Cirulli, Marcos Achaval.

## Análisis
#### Objetivo
Desarrollar una librería en el lenguaje Java que permita manipular y analizar datos
multidimensionales mediante un cubo OLAP. Llevar a cabo este desarrollo
implementando una metodología de trabajo “_incremental_”.

#### Alcance
El cubo debe poder visualizarse en diferentes dimensiones. Además debe soportar las
operaciones de `Roll-Up`, `Drill-Down`, `Slice` y `Dice`. Cada celda del cubo va a
representar un hecho o una medida en el caso que no se muestre el cubo completo. Y
debe poder realizar reducciones o aplicar medidas a los hechos. Por último y no
menos importante, debe poder adoptar posibles extensiones futuras, minimizando el
impacto ante alguna modificación.

#### Descripción de alto nivel del sistema
El sistema implementa un Cubo OLAP de 3 dimensiones utilizando el lenguaje de
programación Java. El cubo se construye a partir de múltiples archivos CSV que
contienen datos relevantes sobre ventas de diferentes productos.

#### Definition of Ready (DoR)
- Que la User Story sea comprensible y que la mayoría del equipo entienda lo mismo.
- El grupo tiene que estar de acuerdo.
- Los criterios de aceptación deben estar bien definidos.
- Que las User Stories abarquen una sola función.
- Que la User Story sea pertinente para los problemas que plantee el trabajo práctico y se mantenga dentro de las restricciones.
- Priorización de User Stories en el backlog respecto de su importancia y afecto (dado por su grado de abstracción o grado de detalle, últimas las de grado alto en detalle).

#### User Stories
_Como todas las User Stories son requisitos para el TP no vimos necesidad de ordenarlas en orden de prioridad._

- **Como usuario**, quiero poder subir (`Roll-Up`) el nivel de jerarquía de cualquier dimensión, para poder visualizar la información de manera más abstracta y con menos miembros de la dimensión. <br>
_Criterios de aceptación:_ <br>
  - Los resultados de subir un nivel y luego bajarlo deben dar un cubo igual al original. <br>
  - Los resultados tienen que coincidir con al menos un grupo. <br>
  - Se deben poder seguir aplicando las funciones del cubo original al resumido.

- **Como usuario**, quiero poder bajar (`Drill-Down`) el nivel de jerarquía de cualquier dimensión, para poder visualizar la información de manera más detallada y con más miembros de la dimensión. <br>
_Criterios de aceptación:_ <br>
  - Los resultados de bajar un nivel y luego subirlo deben dar un cubo igual al original. <br>
  - Los resultados tienen que coincidir con al menos un grupo. <br>
  - Se deben poder seguir aplicando las funciones del cubo original al resumido.

- **Como usuario**, quiero poder filtrar por un valor específico de una dimensión (`Slice`), así poder ver el resultado de cierta variable en los hechos. <br>
_Criterios de aceptación:_ <br>
  - La salida debe ser un subcubo solo con la información que coincida con ese valor. <br>
  - Los resultados tienen que coincidir con al menos un grupo.

- **Como usuario**, quiero poder seleccionar varias dimensiones de corte y filtrar por `n` valores específicos en cada una (`Dice`), así poder ver el resultado pero filtrando por los valores de las dimensiones solicitadas. <br>
_Criterios de aceptación:_ <br>
  - La salida debe ser un subcubo solo con la información que coincida con esos valores de las dimensiones solicitadas. <br>
  - Los resultados tienen que coincidir con al menos un grupo.

- **Como usuario**, quiero poder visualizar por una o dos dimensiones y que estas se muestran en el nivel que corresponde, así poder visualizar de una forma más personalizada. <br>
_Criterios de aceptación:_ <br>
  - La salida debe ser un subcubo solo con la información que coincida con lo solicitado.

- **Como usuario**, quiero poder crear una dimensión a partir de un archivo CSV. <br>
_Criterios de aceptación:_ <br>
  - Los valores del archivo deben dividirse por el separador que corresponda, sea `,` o `;`. <br>
  - Se debe lanzar una excepción si no coincide el número de columnas con el de los valores de dicha fila.

- **Como usuario**, quiero consultar la información básica del cubo, para conocer su nombre, la cantidad y nombres de dimensiones, y la lista de hechos y medidas. <br>
_Criterios de aceptación:_ <br>
  - Cuando se solicita información básica del cubo, entonces se muestran todos los datos mencionados anteriormente.

- **Como usuario**, quiero poder decidir qué hecho del cubo proyectar, para poder hacer un análisis de los distintos valores. <br>
_Criterios de aceptación:_ <br>
  - Se pueden proyectar todos los hechos que tiene el cubo, no hay mensajes de error. <br>
  - Los resultados tienen que coincidir con al menos un grupo.

- **Como usuario**, quiero poder decidir qué medida aplicar a los hechos del cubo a la hora de hacer la proyección, para hacer un análisis de diferentes valores. <br>
_Criterios de aceptación:_ <br>
  - Puedo aplicar cuatro medidas diferentes sin obtener errores: `Count`, `Maximo`, `Mínimo`, `Suma`. <br>
  - Los resultados tienen que coincidir con al menos un grupo.

- **Como usuario**, quiero poder decidir la cantidad máxima de filas y columnas del cubo a proyectar, para poder hacer más cómoda la lectura de los datos. <br>
_Criterios de aceptación:_ <br>
  - Puedo elegir cualquier cantidad para ambos parámetros, siempre y cuando sean números positivos.

## Diseño <br> <p align="center"> 
![diagrama_de_clases](https://github.com/MarcosACH/tp-algoritmos1/blob/main/TP_A1%20-%20Diagrama%20de%20Clases%20(sin%20getters).png) 
</p>

#### Descripción del Diagrama de Clases
Arriba podemos ver una vista general estática del sistema representada por un _diagrama de clases_ que nos permite identificar las diferentes relaciones que hay entre los módulos del sistema. <br>
Por un lado podemos ver una relación de **realización** entre la interfaz `DatasetReader` y la clase que la implementa, `CSVReader`. Aquí también se
cumple un **polimorfismo de inclusión o subtipo**. <br>
Por otro lado, existe una relación de **generalización** entre la clase abstracta `Medida` y todas sus subclases que la extienden. Hay una clara herencia y por lo tanto nuevamente se cumple un 
**polimorfismo de inclusión o subtipo**. Podemos decir también, que las clases `Maximo`, `Suma`, `Minimo` y `Count` son una medida, modelando así jerarquías de tipo “_es un_”. <br>
Luego, mirando más arriba en el diagrama, podemos ver una relación de **asociación de agregación** entre `ConfigCubo` con `ConfigHechos` y `ConfigDimension`. Esto
es así ya que `ConfigHechos` y `ConfigDimension` son parte de `ConfigCubo` y estas dos existen independientemente de `ConfigCubo`. Es decir, las instancias de `ConfigDimension` y `ConfigHechos` son creadas externamente y 
luego pasadas al constructor de `ConfigCubo`. Entonces la vida útil de estas dos no está estrictamente relacionada con la de `ConfigCubo`. Además podemos aclarar que `ConfigCubo` puede
tener 0 o más objetos de `ConfigDimension` y también puede tener uno y solo un objeto de `ConfigHechos` (multiplicidad marcada en el diagrama). La misma relación hay entre `Proyección` y `Cubo`. 
`Cubo` es parte de `Proyección` y no depende de la existencia de `Proyección`. <br>
Por último podemos identificar una relación de **asociación de composición** entre `Cubo` con `Dimension`, `Celda` y `Medida`. Esto se cumple ya que `Cubo` está
compuesto por todas estas clases mencionadas. Vale aclarar que debido a que además `Cubo` es el responsable de la creación y gestión de estas clases, si `Cubo` deja de
existir, todas sus clases que lo componen también dejarán de existir. Para finalizar, `Cubo` puede estar compuesto por 0 o más objetos de todas esas 3 clases que lo componen. <br>
No hay que olvidar las pocas relaciones restantes que son relaciones de **dependencia** (o de “_uso_”) que hay entre `Dimension` y `ConfigDimension`, y `ConfigDimension` y `ConfigHechos` con `CSVReader`. <br> <br> <p align="center"> 
![diagrama_de_clases_subsistema](https://github.com/MarcosACH/tp-algoritmos1/blob/main/TP_A1%20-%20Diagrama%20de%20Clases%20(Subsistema).png) 
</p>

#### Descripción del Diagrama de Clases - Subsistema “Cubo” - “Dimensión”
El diagrama de clases que se ve por encima representa el comportamiento y la relación entre la clase `Cubo` y `Dimension`. `Cubo` tiene un método público `rollUp()` que
toma a una dimensión y permite aumentar el nivel de jerarquía de ella (se sube el nivel de abstracción), siempre y cuando esta dimensión se encuentre en el cubo. Si esta
condición se cumple, se ejecuta el método `aumentarJerarquia()` del módulo `Dimension`. Caso contrario se libera una excepción de tipo
`IllegalArgumentException`. Además no es posible hacer un `rollup()` más allá de la agrupación o reducción total ya que si queremos hacerlo, el método
`aumentarJerarquia()` libera una excepción de tipo `IllegalStateException` (si el nivel actual de la jerarquía ya es 0). <br>
Por otro lado podemos identificar al método `drillDown()` que hace la acción opuesta al método anterior. En este caso se ejecuta el método `disminuirJerarquia()` de
`Dimension`. Hay que tener en cuenta que no se puede hacer esta acción más allá del nivel más detallado de la dimensión, es decir, se puede hacer hasta llegar a hechos sin
agrupar para esa dimensión. Si el usuario quiere seguir disminuyendo en jerarquías más allá de la cantidad que tiene la dimensión menos una, se libera una excepción de
tipo `IllegalStateException`. <br>
Luego en `Cubo` hay un método `slice()` que genera un nuevo cubo que tiene únicamente los hechos que cumplan que en la dimensión pasada como argumento
tengan el valor especificado. Nuevamente, siempre y cuando la dimensión buscada se encuentre en el cubo, de lo contrario también se libera una excepción de tipo `IllegalArgumentException`. <br>
Por último el método `dice()` es similar al método `slice()` pero permite seleccionar varias dimensiones de corte y además un conjunto de valores a filtrar en cada una. En
este caso, se devuelve un subcubo con la misma cantidad de dimensiones pero filtrando los hechos correspondientes a los solicitados. También se libera una
excepción de tipo `IllegalArgumentException` si alguna de las dimensiones pasadas no existe en el cubo.

## Implementación
En este [directorio](https://github.com/MarcosACH/tp-algoritmos1/tree/main/src) se puede visualizar todo el código que compone el total de la librería desarrollada durante todo este trabajo práctico. <br>
Además generamos una documentación de los módulos y métodos más importantes y relevantes del sistema. Esta documentación fue creada con la herramienta de Java llamada `JavaDoc` que permite generar documentaciones de proyectos. 
Se puede visualizar en este [archivo](https://github.com/MarcosACH/tp-algoritmos1/blob/main/index.html).
