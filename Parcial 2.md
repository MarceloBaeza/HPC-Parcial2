# Segundo parcial High Performance Computing
## CPU (MPI) v/s GPU
### Multiplicación de matrices utilizando CUDA + MPI y MPI

													Marcelo Baeza ([GITHUB](https://github.com/MarceloBaeza/))
# Introducción

Hoy en día existen una infinidad de aplicaciones o implementaciones que requieren de un buen desempeño, muchas de estas a nivel matemático (como por ejemplo algebra lineal). En este caso se estudiará el desempeño de la multiplicación de matrices aplicando técnicas de procesamiento tanto en GPU como también en CPU, utilizando hasta 4 computadores de alto desempeño mediante la conexión a un servidor remoto.
Para cálculos en CPU se utilizará la especificación para paso de mensajes MPI. Y desarrollo en GPU se utilizará la especificación para paso de mensajes MPI junto a CUDA. En el presente caso de estudio se presentarán comparaciones tales como:

- MPI vs MPI + CUDA
- Rendimiento
- Aceleración

## Términos utilizados:

- Nodo: Un máquina perteneciente a un cluster.
- Cluster: Conjunto de máquinos unidos, trabajando en el procesamiento de datos juntos.
- CPU: Es la unidad central de procesamiento del sistema.
- GPU: Es la unidad central de procesamiento gráfico del sistema. 

## Herramientas de trabajo:

- CUDA: Es una librería de computación la cual permite la programación en paralela la cual hace uso de los recursos de las GPU.
- MPI: Es una librería de paso de mensaje la cual permite la distribución 
- SLURM: Programa de trabajo que administra las tareas que se están ejecutando en el cluster, además manejo el uso de recursos.

# Desarollo

Para este caso de estudio, se realizó un análisis con los siguientes tamaños de matrices:
- 10 x 10
- 100 x 100
- 500 x 500
- 1.000 x 1.000
- 2.500 x 2.500
- 5.000 x 5.000
- 7.500 x 7.500
- 10.000 x 10.000                                                   

# Caso de estudio
En el presente informe, se realizará un estudio, donde se hará una comparación de MPI por sí solo, y Cuda-Aware MPI, comparando una multiplicación de matriz. Este caso de estudio, no sólo se verá involucrado el tamaño de la matriz, sino también la cantidad de nodos a trabajar, ya que se trabajará en un clúster.
Se realizará un estudio con 1, 2, 3 y 4 nodos, los tamaños de las matrices serán 10 x 10, 100 x 100, 500 x 500, 1.000 x 1.000, 2.500 x 2.500, 5.000 x 5.000, 7.500 x 7.500, 10.000 x 10.000, 12.000 x 12.000 y 15.000 x 15.000.

## Entorno de estudio

Se trabajo en un clúster que posee seis nodos y un nodo maestro.

### Datos de un nodo

| CPU     | Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz |
|---------|------------------------------------------|
| GPU     | GeForce GTX 980 4GB                      |
| Memoria | 32 GB. 4200MHz.                          |

## Resultados CUDA + MPI

| Tamaño Matriz / Nodos | 1         | 2        | 3        | 4        |
|-----------------------|-----------|----------|----------|----------|
| 10x10                 | 0,0901621 | 0,10589  | 0,11085  | 0,12297  |
| 100x100               | 0,101514  | 0,104993 | 0,115029 | 0,134996 |
| 500x500               | 0,100611  | 0,156246 | 0,170789 | 0,191734 |
| 1000x1000             | 0,115178  | 0,329425 | 0,362239 | 0,424527 |
| 2500x2500             | 0,427575  | 1,69629  | 1,77587  | 2,17234  |
| 5000x5000             | 2,53864   | 7,65739  | 7,32445  | 8,58999  |
| 7500x7500             | 8,18159   | 19,6849  | 17,5947  | 19,5146  |
| 10000x10000           | 19,5806   | 40,0781  | 33,7344  | 36,26    |
| 12000x12000           | ---       | ---      | ---      | ---      |
| 15000x15000           | ---       | ---      | ---      | ---      |

En la tabla presentada es posible ver una comparación de tiempos en segundos del caso de estudios. Es posible apreciar que hasta el tamaño 500x500, la diferencia de tiempo entre los nodos, no crece en gran medida; esto se puede deber a que el tamaño de las matrices es pequeño, en cambio, cuando la matriz ya aumenta desde el tamaño 1000x1000, la diferencia comienza aumentar. El tiempo de operación esta influencia no solo en el hecho de multiplicar, si no también pasar los datos del host al device y viceversa.
Es interesante ver que en la tabla que las operaciones de dos nodos supera en tiempo a los demás nodos.  Es más, en la tabla que muestra los tiempos de la operación de Send de cuda, sigue siendo mayor para el mismo nodo. Es interesante ya que se podría esperar algo “lógico” respecto a una proyección lineal de tiempo; por ejemplo, si en un nodo se demora 20 segundos, en 2 nodos 10 segundos, y así. Pero esta lógica se puede ver afectada por diversas razones, la principal la implementación, la forma en la que se está distribuyendo las tareas, que está trabajándose un paquete o grupo de filas por matriz. Pasar la matriz completa y unos paquetes completos, es trabajo arduo y no trivial que se debe realizar y a la vez pensando en el mejor caso que la memoria GPU, no se llene.

## Resultados MPI

| Tamaño Matriz / Nodos | 1           | 2         | 3        | 4        |
|-----------------------|-------------|-----------|----------|----------|
| 10x10                 | 0,000355977 | 0,080488  | 0,160657 | 0,240758 |
| 100x100               | 0,000938036 | 0,0815809 | 0,131962 | 0,242132 |
| 500x500               | 0,0330854   | 0,191427  | 0,373495 | 0,52484  |
| 1000x1000             | 0,262273    | 0,738164  | 1,36097  | 2,00283  |
| 2500x2500             | 8,05751     | 5,86035   | 8,13415  | 11,5169  |
| 5000x5000             | 66,8057     | 39,2538   | 43,8076  | 51,6715  |
| 7500x7500             | 232,902     | 141,551   | 128,943  | 139,884  |
| 10000x10000           | 627,255     | 341,462   | 287,556  | 290,712  |
| 12000x12000           | 2072.65     | ---       | ---      | ---      |
| 15000x15000           | 2830.97     | ---       | ---      | ---      |

En la tabla resultado MPI, si se compara los nodos 1 y 3 en la multiplicación 10000x10000, están muy cerca de cumplir lo mencionado anteriormente, si un nodo se demora x tiempo, x/3 se demoraría si se trabaja con tres nodos. Pero en cambio con los 4 nodos, no sigue cumpliéndose lo que, mencionado, no es la cuarta parte de los primeros nodos.
También los tiempos de en comparación con CUDA, han aumentado, dado a los beneficios que entrega esta, por el poder procesamiento que da

## Resultados CUDA + MPI Send Time

| Tamaño Matriz / Nodos | 1          | 2         | 3          | 4          |
|-----------------------|------------|-----------|------------|------------|
| 10x10                 | 2,36E-03   | 3,23E-003 | 1,43E-003  | 1,57E-003  |
| 100x100               | 0,00016055 | 0,0013622 | 0,00179193 | 0,00185929 |
| 500x500               | 0,0012471  | 0,033159  | 0,0263342  | 0,0235861  |
| 1000x1000             | 0,00236907 | 0,137071  | 0,104685   | 0,0921201  |
| 2500x2500             | 0,0113244  | 0,855902  | 0,643702   | 0,572147   |
| 5000x5000             | 0,0451733  | 3,42843   | 2,57285    | 2,28516    |
| 7500x7500             | 0,0961002  | 7,71293   | 5,78554    | 5,14052    |
| 10000x10000           | 0,168805   | 13,7087   | 10,2852    | 9,14032    |
| 12000x12000           | ---        | ---       | ---        | ---        |
| 15000x15000           | ---        | ---       | ---        | ---        |

## Resultados CUDA + MPI Recev Time

| Tamaño Matriz / Nodos | 1           | 2          | 3           | 4           |
|-----------------------|-------------|------------|-------------|-------------|
| 10x10                 | 6,89E-07    | 3,08E-003  | 1,42E-003   | 1,19E-003   |
| 100x100               | 0,000183981 | 0,00197511 | 0,000535078 | 0,000448998 |
| 500x500               | 0,00126689  | 0,0350306  | 0,024818    | 0,0214011   |
| 1000x1000             | 0,00239621  | 0,138577   | 0,102533    | 0,0900081   |
| 2500x2500             | 0,0113522   | 0,858032   | 0,641546    | 0,571508    |
| 5000x5000             | 0,0452046   | 3,43021    | 2,57087     | 2,28521     |
| 7500x7500             | 0,0961308   | 7,71555    | 5,78371     | 5,1419      |
| 10000x10000           | 0,168833    | 13,7109    | 10,2807     | 9,13665     |
| 12000x12000           | ---         | ---        | ---         | ---         |
| 15000x15000           | ---         | ---        | ---         | ---         |

En las dos tablas anteriores se decidió a obtener estos datos, ya que permite obtener una relación respecto al tiempo que se demora enviar las filas por la matriz, con el tamaño de la matriz, ya que si aumenta, debe aumentar el tiempo de send() y recev(). 

## Resultados MPI Send Time

| Tamaño Matriz / Nodos | 1          | 2           | 3           | 4           |
|-----------------------|------------|-------------|-------------|-------------|
| 10x10                 | 4,40E-007  | 9,88E-07    | 1,03E-03    | 1,23E-06    |
| 100x100               | 9,48E-05   | 0,000264577 | 0,000259548 | 0,000264528 |
| 500x500               | 0,00066522 | 0,0168564   | 0,0173982   | 0,0176625   |
| 1000x1000             | 0,0023027  | 0,0721996   | 0,070007    | 0,0698751   |
| 2500x2500             | 0,00941014 | 0,456599    | 0,446267    | 0,441586    |
| 5000x5000             | 0,0481816  | 1,82807     | 1,78655     | 1,76865     |
| 7500x7500             | 0,129952   | 4,11256     | 4,02338     | 3,98119     |
| 10000x10000           | 0,246834   | 7,31165     | 7,15577     | 7,15577     |
| 12000x12000           | 0.365057   | ---         | ---         | ---         |
| 15000x15000           | 0.592066   | ---         | ---         | ---         |

## Resultados MPI Recev Time

| Tamaño Matriz / Nodos | 1           | 2           | 3          | 4          |
|-----------------------|-------------|-------------|------------|------------|
| 10x10                 | 5,51E-007   | 2,73E-03    | 1,30E-03   | 3,36E-06   |
| 100x100               | 5,10E-05    | 0,000992916 | 0,00135929 | 0,00127343 |
| 500x500               | 0,000679956 | 0,0189556   | 0,0191069  | 0,0187525  |
| 1000x1000             | 0,00204115  | 0,0736831   | 0,0721444  | 0,0714017  |
| 2500x2500             | 0,010019    | 0,457943    | 0,447864   | 0,443005   |
| 5000x5000             | 0,0481248   | 1,82952     | 1,79064    | 1,77149    |
| 7500x7500             | 0,129897    | 4,11593     | 4,02383    | 3,98242    |
| 10000x10000           | 0,246777    | 7,3133      | 7,15455    | 7,15455    |
| 12000x12000           | 0.365057    | ---         | ---        | ---        |
| 15000x15000           | 0.592008    | ---         | ---        | ---        |

## GPU vs CPU (Gráficas)

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gSW5pZGtRczhiR1E "Resultado con un nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gZnNGVjctU2xobzg "Resultado con dos nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gUUlvbXBWVGdWVGc "Resultado con tres nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gRFQ5TWFpRTR0d1k "Resultado con cuatro nodo")


## Aceleración por Hardware

En informática, la aceleración por hardware es el uso del hardware para realizar alguna función más rápido de lo que es posible usando software ejecutándose en una CPU de propósito general.
El hardware que realiza la aceleración, cuando se encuentra en una unidad separada de la CPU, es denominado acelerador por hardware, o a menudo más específicamente como un acelerador gráfico o unidad de coma flotante, etc. 
Ahora bien, para obtener la aceleración respectiva en este caso de estudio solo basta dividir la velocidad del tiempo de procesamiento en CPU por tiempo de procesamiento en GPU.

| Nodos/Tamaño Matriz | 10x10        | 100x100      | 500x500      | 1000x1000    | 2500x2500     |
|---------------------|--------------|--------------|--------------|--------------|---------------|
| Nodo1               | 0,0039481889 | 0,0092404594 | 0,3288447585 | 2,277110212  | 18,8446705256 |
| Nodo2               | 0,7601095476 | 0,7770127532 | 1,2251641642 | 2,2407649693 | 3,4548043082  |
| Nodo3               | 1,4493188994 | 1,1472063567 | 2,1868797171 | 3,7571051157 | 4,5803746896  |
| Nodo4               | 1,9578596406 | 1,7936235148 | 2,7373340148 | 4,7177918012 | 5,3016102452  |

| Nodos/Tamaño Matriz | 5000x5000    | 7500x7500     | 10000x10000   | 12000x12000 | 15000x15000 |
|---------------------|--------------|---------------|---------------|-------------|-------------|
| Nodo1               | 26,315546907 | 28,4665939017 | 32,0345137534 | ---         | ---         |
| Nodo2               | 5,1262636486 | 7,1908417112  | 8,5199148662  | ---         | ---         |
| Nodo3               | 5,9810088129 | 7,3285137001  | 8,5241178145  | ---         | ---         |
| Nodo4               | 6,0153155009 | 7,1681715229  | 8,0174296746  | ---         | ---         |

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gM1VYQ1RlOU5HTlU "Aceleración con un nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0geHNZdEhYVkNnTHc "Aceleración con dos nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gdUlSWE9hSjItaVk "Aceleración con tres nodo")

![alt text] (https://drive.google.com/uc?export=view&id=0B4lOvtntuj0gSHpMVHk3ZmhWQnM "Aceleración con cuatro nodo")

Es posible apreciar que, en las últimas cuatro gráficas, la aceleración siempre creciente después de que la matriz es mayor a 1.000 x 1.000, con esto es posible entender que existe una gran diferencia entre el desempeño realizado por la CPU y CUDA, dejando en claro que CUDA en el mayor número de las pruebas, es mejor. Pero cuando el tamaño de las matrices es menor posee aceleraciones negativas. En donde la relación de CUDA y CPU, no fue la esperada.

## Conclusión

Dado a los resultados obtenidos en el presente estudio, es posible concluir que MPI es una herramienta de administración de trabajo/mensaje muy útil. Aplicando en este caso, el concepto de “divide y conquista”, en donde al dividir estas tareas está permitiendo agilizar la carga que sólo un proceso antes debía realizar. 
Existe un dato analizado en otro estudio ([GITHUB (https://github.com/MarceloBaeza/HPC/blob/master/parcialhpc.md)]) donde la multiplicación de matriz de 5000x2500, toma 664,4 s. Donde en el peor caso de mpi es de 620 s, y con una matriz que casi es el doble de grande. 
Pero a pesar de esta gran diferencia entre una medida secuencial y una de mpi, en donde este último gana considerablemente, aún no sigue siendo rival para Cuda + MPI. 
El peor caso que trabajo cuda fue de 40 segundos, una aplastante diferencia con el peor caso de MPI. En este caso es posible concluir que Cuda + MPI, es considerablemente mejor que MPI por sí sólo, ya sea por la enorme cantidad de núcleos internos que poseen las gpu. 
Pero a pesar de este análisis, en el pasado informe, Cuda se demoró 3,5 sgd en hacer una matriz de 5000x2500, cuando en este informe el menor caso de esa línea de tamaño es de 2,5 sgd.
Cuda + Mpi, posee sus beneficios, pero a la vez también hay que tener en cuenta, no solo la habilidad de distribuir tareas, si no también considerar el tiempo que se emplea para asignar las tareas, como también para recibirlas.
