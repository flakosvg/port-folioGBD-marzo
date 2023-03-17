# Portfolio Marzo GBD

Terminando parte de las unidades sobre consultas a las tablas,en marzo hemos empezados las unidades que tienen que ver con las consultas hechas usando varias tablas.

## RESUMEN

Este mes podríamos resumirlo en la obtención de conecimientos sobre el uso de varias tablas para hacer consultas en los cuales has de haber obtenido conocimiento para leer los esquemas de las tablas y enternder bien las relacions, también has de manejar las consultas y enternderlas, y saber sobre los datos que estás consultando.

***

## REFLEXIONES PERSONALES

Este tema se me ha hecho más sencillo que los temas de consultas anteriores a estas unidades sobre la consulta de varias tablas.
Como punto negativo tampoco puedo encontrar mucho por ejemplo se podría dificultar si no entiendes bien el esquema de tablas de la base en la que estes realizando las consultas.
***

## EJERCICIOS SIGNIFICATIVOS

### CONSULTAS USANDO JOIN ON O USING

El ***JOIN ON*** o ***USING***

* Con JOIN ON lo que hacemos es unir dos tablas cuya relaciones tengan nombres distintos en sus tablas.
* Con USING lo que podemos hacer es relacionar dos tablas indicando solo la columna que forman la relación.

#### CONSULTA EJERCICIO 13

En esta consulta tenemos que relacionar los datos de dos tablas en este caso queremos sacar el máximo de espetadores que hubo en una corrida para ello buscamos unir la tabla de plazas puesto que contiene los datos sobre las plazas de toros y su aforo máximo y relaccionarla con la de corridas que tiene los datos sobre el evento en este caso el número de espectaores que acudieron a este y la fecha de cuando tuvo lugar.
Para ello empleamos el ***JOIN*** y el ***USING*** con el ***JOIN*** indicamos con que tabla tiene la relación y con  el ***USING*** le indicamos la columna que queremos recoger que tienen en común o cuyo esquema coninciden en este caso ***id_plaza***.

```.
SELECT  nombre, fecha_corrida, aforo, espectadores
FROM    plazas
JOIN    corridas    USING   (id_plaza)
WHERE   aforo=espectadores
    ;
```

#### RESULTADO EJERCICIO 13

El resultado de esta consulta es:

| Nombre | Fecha corrida | Aforo | Espectadores |
| ------ | ------------- | ----- | ------------ |
| La Maestranza | 2022-04-22 18:00:00 | 15000 | 15000 |
| Las Ventas | 2022-05-19 18:00:00 | 17000 | 17000 |
| Las Ventas | 2022-05-21 18:00:00 | 17000 | 17000 |
| La Monumental | 2022-06-11 17:00:00 | 18000 | 18000 |

***

### CONSULTAS QUE POSEAN UN CROSS JOIN

Con ***CROSS JOIN*** conseguiremos forzar cruces entre la misma tabla permitiendonos hacer una consultas más compleja.

#### CONSULTA EJERCICIO 14

Esta es una consulta que es algo más compleja pero cuyo fin es sacar los toreros que tienen el mismo número de orejas cortadas, para ello los que buscamos es renferenciar dos tablas y forzar que se relacionen también consigo mismo dando una relación reflexiba.
Para ello usamos el ***CROOS JOIN*** el cual nos permite hacer la relación reflesiva.
Con esta consulta también buscamos realizar la suma de las orejas que el torero cortó por ello nos vemos obligados a usar otro ***JOIN USING*** ya que el dato de las orejas se almacena en la tabla de toros.
Por último para que nos saque los que son iguales tenemos con un ***WHERE*** que condicione para reflejar solo los que el número de orejas es el mismo y con un ***AND*** estaremos obligado a que el nombre sea distinto ya que sino nos relacionaria el mismo torero con el mismo.

```.
SELECT t1.nombre,t1.orejas,t2.nombre,t2.orejas
FROM
  (SELECT r.nombre,
          SUM   (orejas)    as  orejas
          FROM  toreros r   JOIN toros t    USING(id_torero)
          GROUP BY          r.nombre
  ) t1
CROSS JOIN
  (SELECT r.nombre,
          SUM   (orejas)    as      orejas
          FROM  toreros r   JOIN    toros t    USING(id_torero)
          GROUP BY          r.nombre
  ) t2
WHERE t1.orejas=t2.orejas
AND t1.nombre>t2.nombre
    ;
```

#### RESULTADO EJERCICIO 14

La consulta se devuelve como:
| Toreros | Orejas | Torero | Orejas |
| ------- | ------ | ------ | ------ |
|Principe Gitano|3|El niño de los clavos|3|
|Carlitos de La Puebla|2|Bibilitano|2|
|Pucelanito|0|Juliano|0|
|Pistoletita|5|El chulapón|5|

***

### CONSULTAS LEFT OUTER JOIN

Con ***LEFT OUTER JOIN*** lo que hacemos es relacionar una columna de nuestra tabla y sus relaciones con otra.

#### CONSULTA EJERCICIO 10

En esta cosulta lo que buscamos es sacar los datos que no tienen realacionadas corridas es decir que corrida sea igual a nulo, pora ello empleamos el ***LEFT OUTER JOIN*** con el que relacionamos las plazasde toros y buscamos que nos devuelva los que son nulos en este caso en otra tabla llamada corridas.
Pero solo buscamos la plaza por lo que no queremos que salga la columna de corridas.

```.
SELECT  nombre
FROM    plazas
LEFT OUTER JOIN     corridas    USING (id_plaza)
WHERE   id_corrida
        IS NULL
    ;
```

#### RESULTADO EJERCICIO 10

| Nombre |
| ------ |
| La Malagueta |
| Campo Pequeño |
| La Monumental |

***

### CONSULTAS CON SUM, AVG,COUNT, MAX O MIN

Con este tipo de consultas buscamos hacer calculos y obtener el resultado de este.

* sum sumará las columnas de dos tablas y nos devuelve el resultado de esta.
* avg
* count cuenta la cantidad de filas que contiene una columna.
* max hace una máxima devolviendo la mayor cantidad de veces.
* min realia una mínima devolviendo cuantas veces como mínimo aparece un dato.

#### CONSULTA EJERCICIO 5

Para esta consulta buscamos el número máximo de orejas cortadas, para ello empleamos ***MAX*** que buscará el numero más alto, pero para sacar ese numero tenemos que calcular ese número para ello necesitamos saber que torero y cuantas orejas a cortado para ello hacemos un ***sum(orejas)*** y luego relacionamos el torero con los datos de las orejas que se almacenan en la tabla toros.

```.
SELECT max(
            sum(orejas)
          )
FROM    toreros r   JOIN toros t    USING(id_torero)
GROUP BY        r.nombre
    ;
```

#### RESULTADO EJERCICIO 5

| MAX(SUM(Orejas)) |
| ---------------- |
| 12 |

***

### CONSULTAS USANDO HAVING

Con ***HAVING*** lo que hacemos es poner una condición para que realice dicho cálculo

#### CONSULTA EJERCICIO 7

En esta consulta buscamos que nos devuelva los cumplan la condición de tener relacionado más de una vez en otra tabla o también cuantas veces se repite ese dato en otras tablas.
Para esa condición empleamos ***HAVING***, la cual nos permite poener la condición que queramos en este caso que su conteo de veces que aparece sea mayor a uno, el asterisco ***\(\*)*** indica que puede coger cualquier dato de la columna propietario de la tabla ganaderias.

```.
SELECT      propietario, count(*)
FROM        ganaderias
GROUP BY    propietario
HAVING      count(*)>1
    ;
```

#### RESULTADO EJERCICIO 7

| Torero | Plazas toreadas |
| ------ | --------------- |
| Jacitín de la Peña | 3 |
| Pistoletita | 2 |
| Arletano | 3 |
| Bibilitano | 2 |
| Jaimito el de los tebeos | 2 |
| Currito de sotomonte | 3 |
| El gorrión | 2 |
| El chulapón| 3 |

***

## CONCLUSIONES

En mi caso solo he cogido la práctica que usa la base de datos de toros habiendo otras  prácticas donde podría encontrar consultas incluso mejores, debido a que por mi padre que tiene una aficción por estos eventos he decido usarla.
Me ha gustado el tema y no me ha supuesto mucha dificultad en entenderlo en su mayoría comparandolo con las unidades anteriores de consultas que si me ha sido más complicado entender.
