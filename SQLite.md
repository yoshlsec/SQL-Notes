MySQL es un sistema de gestión de bases de **datos relacionales** (RDBMS, por sus siglas en inglés) de código abierto amplíamente utilizado. Siendo un sistema de gestión de bases de datos relacionales que se utiliza para almacenar, administrar y recuperar datos.

Tiene una estructura relacional que significa que se organiza mediante filas y columnas, es tiene mucha **escalabílidad**, **multiplátaforma** y **rendimiento**

## Diagrama ER
El diagrama **entidad-relación** es una herramienta para representar de manera visual la estructura de una base de datos relacional. Mostrando **entidades** y **atributos**. 
El objetivo de este diagrama es ser: **conciso**, **preciso** y **no** ser muy **redundante**

#### Ejemplo

- Entidad: _Persona_
- Atributos comunes (_Ciudad_)
- Atributo compuesto (_Información de contacto_)
- Atributo multi-valor (_Habilidades_)
- Atributo derivado (_Edad_)

![[./images/Pasted image 20230825005117.png]]

--- 
## Instalación sqlite

```bash
sudo apt install sqlite3
```

```bash
sudo apt install sqlitebrowser
```

```bash
sudo apt-get update --fix-missing && sudo apt install sqlitebrowser
```


## Practica con la teoría básica

![[./images/Pasted image 20230825012527.png]]

### Creando base de datos

```mysql
CREATE DATABASE "myDB";
```

## Primera tabla

El **id** como vemos es la **primera clave** (facilitando la consulta de la tabla) y es **autoincrement**, haciendo un contando que no es modificable. Solo puede haber una primery key y autoincrementable
```mysql
CREATE TABLE "users" (
	"id"           INTEGER PRIMARY KEY AUTOINCREMENT
	"name"         TEXT
	"age"          INTEGER DEFAULT 18
	"aplication"   BLOB -- Se suele guardar el link de la aplicación el el servidor y poner solo la url en la base de datos
	"porcentajes"  REAL
	"numero_pì"    NUMERIC
	"DATE"         DATETIME
);
```

### Consultas / Querys Básicas
Funciones de una consulta
- Create -> Crear
- Read -> Leer
- Update -> Actualizar
- Delete -> Borrar

Aquí al tener default de 18, no es necesario poner la edad.
```mysql
INSERT INTO users ("name", "aplication", "porcentajes", "numero_pì") 
VALUES  ('Usuario1', 'Aplicacion1', 75.5, 3,1415);
		('Usuario2', 'Aplicacion2', 23.7, 3,1415);
		('Usuario3', 'Aplicacion3', 90.2, 3,1415);
```

Con esta consulta nos muestra todos los datos de la tabla **users**:
```mysql
SELECT * FROM users
```

Con esta consulta nos muestra las columnas **name** y **porcentajes** de la tabla **users**:
```mysql
SELECT name,porcentajes FROM users
```

## Segunda tabla
En este caso en la tabla **reg_med** (registro médico) la columna **id_paciente** es una Foreign KEY (es decir, consulta al id de la tabla **users**)
```mysql
CREATE TABLE users (
	"id"           INTEGER PRIMARY KEY AUTOINCREMENT
	"name"         TEXT
	"age"          INTEGER DEFAULT 18
);

CREATE TABLE reg_med (
	"id"           INTEGER PRIMARY KEY AUTOINCREMENT
	"horario"      TEXT
	"id_paciente"  INTEGER REFERENCES users("id")
);
```

#### Sintaxis
Se Puede hacer en un one liner:
```mysql
"id"           INTEGER PRIMARY KEY AUTOINCREMENT
"id_paciente"  INTEGER REFERENCES users("id")
```

Y se puede hacer en varias lineas
```mysql
"id" INTEGER,
"id_paciente"  INTEGER,

PRIMARY KEY ("id" AUTOINCREMENT),
FOREIGN KEY ("id_paciente") REFERENCES users("id")
```

## Diagrama ER de Northwind
Al final todas las tablas están interconectadas con **FOREIGNS KEYS**, para facilitar la búsqueda de información
![[./images/Pasted image 20230825121350.png]]

Una vez que ya nos podemos imaginar el código de este diagrama empezamos con las consultas.
Página para practicar online: https://sqliteonline.com
Página donde esta la DB de la imagen: https://en.wikiversity.org/wiki/Database_Examples/Northwind/SQLite
### Consultas / Querys Intermedias
#### AS
Nos sirve para meter un nombre que solo vemos nosotros en la petición para identificar más el valor de las tablas
```mysql
SELECT  FirstName AS nombre, LastName AS apellido FROM Employees
```

Otro ejemplo más práctico es el siguiente
```mysql
SELECT SUM(Price) / COUNT(Price) AS mediaPrecio FROM Products
```
Podría ser **COUNT(*)** pero contaría también las columnas de precio que tienen el valor **NULL** y no nos conviene eso.
PD: Más adelante podemos simplificar eso con AVG()

#### ORDER BY
Sirve para ordenar el output de las tablas

Prioridad con ASC:
- NULL
- Números (0-9)
- Carácteres especiales
- Letras

En este ejemplo nos sirve para que nos muestre de menor a mayor el **precio** de la tabla **Products**:
```mysql
SELECT * FROM Products ORDER BY price ASC -- De menor a mayor (Con integers)
SELECT * FROM Products ORDER BY price DESC -- De mayor a menor

SELECT * FROM Products ORDER BY UPPER(ProductNam)e ASC -- Orden alfabético (Con strings) estando todo en mayúsculas
SELECT * FROM Products ORDER BY LOWER(ProductName) DESC -- Orden alfabético a la inversa, estando todo en minusculas
```

En el caso que tengamos valores nulos que no queramos ver con **ASC** podemos añadir la siguiente query:
```mysql
SELECT * FROM Prodcuts ORDER BY ProductName ASC NULLS LAST -- Los seguimos viendo pero los mandamos al final
```
##### Jugando con duplicados
Quitar **duplicados** en un el output:
```mysql
SELECT DISTINCT ProductName FROM Products ORDER BY Products ASC 
```

Mostrar **duplicados**:
```mysql
INSERT INTO Products ("ProductName", "Unit", "Price") VALUES ("Chais", "213", "777");

SELECT productname, COUNT(*) AS cantidad
FROM Products
GROUP BY productname
HAVING COUNT(*) > 1;
```

Output:
![[./images/Pasted image 20230825130923.png]]

#### WHERE
Condicional donde la cumple (El va a filtar entonces no puede estar despues de un agrupamiento o justo al final de la query)
```mysql
SELECT * FROM Products WHERE productname='Chais'
```

Output: (El segundo lo añadimos nosotros para tener mayor libertad)
![[./images/Pasted image 20230825131527.png]]

Vamos a complicar un poco más la query:
```mysql
SELECT * FROM Products WHERE productname='Chais' ORDER BY price DESC
```

Output:
![[./images/Pasted image 20230825131804.png]]

Volviendo a lo fácil, también podemos usar** operaciones aritméticas**:
- Que nos muestre los productos que valgan menos de 40 euros de menor a mayor
```mysql
SELECT unit,price FROM Products WHERE price <= 40 ORDER BY price ASC
```

#### UPDATE
**Cambiar** los valores de las columnas/tablas
```mysql 
UPDATE Products SET price=420,unit=70 WHERE price=777
```

#### Operadores lógicos pt1
Listar solo los usuarios que estén entre 50 y 55 en el **CustomID**
```mysql
SELECT * FROM Customers WHERE CustomerID >= 50 AND CUSTOMER ID <= 55
```

Que te muestre 2 empleados con **x** o **y** **nombres**
```mysql
SELECT * FROM Employees WHERE FirstName = "Nancy" OR Firstname = "Anne"
```

Que **no** nos muestre los **precios** mayores de 50
```mysql
SELECT * FROM Products WHERE NOT price > 50 ORDER BY price DESC
```

![[./images/Pasted image 20230825133425.png]]

**PD: En vez de usar _NOT_ existe _AND !=_ que es exactamente lo mismo**

Filtrar por **NULL**
```mysql
SELECT * FROM Products WHERE ProductName IS NULL ORDER BY PorductName ASC
```

Filtrar por los que **no** son **NULL**
```mysql
SELECT * FROM Products WHERE ProductName IS NOT NULL ORDER BY PorductName ASC
```

#### LIMIT
Hacer querys y que solos nos muestre las **primeras x columnas**
```mysql
SELECT * FROM Products WHERE NOT price > 50 ORDER BY price DESC LIMIT 1
```

![[./images/Pasted image 20230825133638.png]]

Hacer que nos muestre las **últimas x columnas**
```mysql
SELECT * FROM Products WHERE NOT price > 50 ORDER BY price ASC LIMIT 1
```

![[./images/Pasted image 20230825134009.png]]

Que nos muestren **3 precios random** para elegir
```mysql 
SELECT * FROM Products WHERE NOT price > 50 ORDER BY RANDOM() LIMIT 3
```


### Consultas / Querys Medias 

#### BETWEEN
Recordaís en el apartado de **Condicionales** que filtraba entre 50 y 55, se puede hacer de otra manera:
```mysql
SELECT * FROM Products WHERE price BETWEEN 20 AND 40 AND CategoryID=6
```

Algo más practico:
```mysql
SELECT * FROM Employees WHERE BirthDate BETWEEN "1950-0-1" AND "1960-0-1"
```

Podemos hacer que no nos filtre el rango especificado:
```mysql
SELECT * FROM Products WHERE (NOT) price (NOT) BETWEEN 20 AND 40
-- Hay 2 porque se puede poner en una de las 2 opciones, esto con BETWEEN y con todo en sqlite
```

#### LIKE
Es exactamente como un **=** pero tiene una busqueda más especializada: [Apellido -> FULLER]
- **"%r"** -> Todos los apellidos que terminen por **r**
```mysql
SELECT * FROM Employees WHERE LastName LIKE "%r"
```

-  **"F_ler"** -> Todos los apellidos que empiecen por **F** tengan un **char desconocido** y continue con **ler**
```mysql
SELECT * FROM Employees WHERE FirstName LIKE "F_l_er"
```

#### Operadores lógicos pt2
En vez de usar muchos **OR** para filtrar por algo podmeos hacer una **lista** y filtrar con **IN**:
```mysql
SELECT * FROM Products WHERE SuppliersID IN (3,4,5,6)
```

**ESTA SERÍA LA FORMA QUE NO DEBEMOS USAR**
```mysql
SELECT * FROM Products WHERE SuppliersID=3 OR SuppliersID=4 OR SuppliersID=5  OR SuppliersID=6
```

Lo mismo pasa con **NOT IN** que sería lo mismo que **OR NOT**
```mysql
SELECT * FROM Products WHERE SuppliersID NOT IN (3,4,5,6)
```

#### Funciones de Agregación / Funciones internas
###### count()
Esta ya la hemos usado anteriormente, nos sirve para **contar** cuantas columnas hay en la fila x
```mysql
SELECT count(firstName) FROM Employees
```

###### SUM()
Te **suma** todos los **integers** que hay en la columna x
```mysql
SELECT SUM(Price) AS sumaTotal FROM Products
```

###### AVG()
Te muestra la **media**/**promedio** de la columna
```mysql
SELECT AVG(Price) AS sumaTotal FROM Products
```

###### ROUND()
**Trunquea** un número **con** x **decimales** que tu le digas

Sin decimales
```mysql
SELECT ROUND(AVG(Price)) AS sumaTotal FROM Products
```

Con 2 decimales
```mysql
SELECT ROUND(AVG(Price),2) AS sumaTotal FROM Products
```

###### MAX()
Te muestra el valor más alto de una fila
```mysql
SELECT MAX(Quantity) FROM OrderDetails
```

###### MIN()
Te muestra el valor más bajo de una file
```mysql
SELECT MIN(Quantity) FROM OrderDetails
```

###### CELI()
Redondea para arriba
###### FLOOR()
Redondea para abajo
#### GROUP BY y HAVING
##### GROUP BY
Básicamente te agrupa/**suma** todos los valores (int) de una columna
```mysql
SELECT SupplierID,ROUND(AVG(Price)) AS Promedio FROM products 
GROUP BY SupplierID
ORDER BY Promedio ASC
LIMIT 5
```

Output:
![[./images/Pasted image 20230825144346.png]]

##### HAVING
Sabiendo que el **WHERE** filtra registros el **HAVING** filtra por **grupos**, es decir, que se pone detras de **GROUP BY**
```mysql
SELECT SupplierID,ROUND(AVG(Price)) AS Promedio FROM products 
GROUP BY SupplierID
HAVING promedio = 5
```

Output: 
Si probamos esto mismo pero con **WHERE** data un **SINTAXIS ERROR**
![[./images/Pasted image 20230825145020.png]]

###### Ejemplo 1
Este **WHERE** esta filtrando por los registros del **SELECT** para quitar todos los valores que contengan **NULL**, así cuando usemos el **GROUP BY** para filtrar con **HAVING** los promedios que son iguales a 5, sin tomar en cuenta los valores de nombre NULL que tienen el **Price** en 0.

_Hint: Seguramente tengas que añadir tu en la tabla esos valores porque sino te saldra todo igual_
```mysql
SELECT SupplierID,ROUND(AVG(Price)) AS Promedio FROM products
WHERE ProductName IS NOT NULL
GROUP BY SupplierID
HAVING promedio = 5
```

###### Ejemplo 2
En este caso estamos sumando todas las ventas que tienen cada producto (que lo identificamos por la fila **ProductID**) y lo ordenamos por la por **productID** de menor a mayor, podríamos ordenarlos por el precio (**ORDER BY TOTAL**)

```mysql
SELECT ProductID, SUM(Quantity) AS Total FROM OrderDetails GROUP BY ProductID ORDER BY ProductID
```

### Consultas / Querys Hard
#### Subconsulta
Si o si tiene que ser un **select**, es una **mini-consulta** que esta **dentro** de la consulta **normal**. Para representar a la subconsulta usamos los **parentesis**, cabe recalcar (visto en el primer ejemplo) que podemos usar columnas de otras tablas mientas que las llamemos, es decir: 
**nombreTabla.nombreColumna**, esto también lo podemos usar en consultas normales, pero no tiene sentido porque la espeficiamos en el **FROM**

###### Ejemplo 1
El primer ejemplo nos sirve para sacar el **ID** y **Cantidad** de la tabla **OrderDetails** y el **nombre** del producto de la tabla **ProductName**
```mysql
SELECT 
	ProductID,
	Quantity,
	(SELECT ProductName FROM products WHERE OrderDetails.ProductID = ProductID) as Name
FROM OrderDetails
```

En el ejemplo anterior podemos poner un **AS** en el **FORM** para simplificar más el código:
```mysql
SELECT 
	ProductID,
	Quantity,
	(SELECT ProductName FROM products WHERE OD.ProductID = ProductID) as Name
FROM [OrderDetails] OD -- Para las tablas es lo mismo decir: [tablaName] newName que tablaName as newName
```

###### Ejemplo 2
En el segundo ejemplo será un poco más complicado ya que calcularemos todo el **dinero recaudado** por cada **productID**
```mysql
SELECT 
	ProductID, 
	(SELECT Price FROM Products WHERE ProductID = OD.ProductID) as Price,
	SUM(Quantity) as total_vendido,
	(SUM(Quantity) * (SELECT Price FROM Products WHERE ProductID = OD.ProductID)) as dinero_recaudado
FROM [OrderDetails] OD
GROUP BY ProductID
```

Pero aqui somos chisquillosos y no queremos decimales, entonces queremos **truncar** los decimales y ya que estamos lo **ordenamos** de menor a mayor (el dinero recaudado): 
```mysql
SELECT 
	ProductID, 
	ROUND((SELECT Price FROM Products WHERE ProductID = OD.ProductID)) as Price,
	SUM(Quantity) as total_vendido,
	ROUND((SUM(Quantity) * (SELECT Price FROM Products WHERE ProductID = OD.ProductID))) as dinero_recaudado
FROM [OrderDetails] OD
GROUP BY ProductID
ORDER BY dinero_recaudado DESC
```

También podemos usar **subconsultas** en operaciones aritmeticas, es decir, imaginate que no queremos ver el precio pero si queremos que nos muestre solo los productos que valgan **más de 50 euros**.
```mysql
SELECT
	ProductID,
	SUM(Quantity) as total_vendido,
	ROUND((SUM(Quantity) * (SELECT Price FROM Products WHERE ProductID = OD.ProductID))) as dinero_recaudado
FROM [OrderDetails] OD
WHERE ROUND((SELECT Price FROM Products WHERE ProductID = OD.ProductID)) > 50
GROUP BY ProductID
ORDER BY dinero_recaudado DESC
```

###### Ejemplo 3
Este no varia mucho del anterior pero para no saturarlo lo hacemos en un ejemplo nuevo.
Para este caso queremos solo que nos muestre el **total_vendido**.
```mysql
SELECT productid,total_vendido FROM (
	SELECT
		ProductID,
		SUM(Quantity) as total_vendido,
		ROUND((SUM(Quantity) * (SELECT Price FROM Products WHERE ProductID = OD.ProductID))) as dinero_recaudado
	FROM [OrderDetails] OD
	WHERE ROUND((SELECT Price FROM Products WHERE ProductID = OD.ProductID)) > 50
	GROUP BY ProductID
)
```

###### Ejemplo 4
Juntando 3 tablas para averiguar los empleados que venden más del promedio
```mysql
SELECT
	FirstName,
	LastName,
	(
		SELECT SUM(od.Quantity) 
      	FROM [orders] o, [OrderDetails] od
		WHERE o.EmployeeID = e.EmployeeID AND od.orderID = o.orderID
    ) as unidades_totales
FROM [EMployees] e

WHERE unidades_totales < (
  	SELECT AVG(unidades_totales)
	FROM (
     	SELECT (
          	SELECT SUM(od.Quantity)
			FROM [orders] o, [OrderDetails] od
			WHERE o.EmployeeID = e2.EmployeeID AND od.orderID = o.orderID) as unidades_totales
		FROM [Employees] e2
	GROUP BY e2.EmployeeID
    )
)
```

No te preocupes, esto es un código que no es eficiente ni legible, pero esta bien saberlo

#### JOINS

##### Cross Join
Ya ni se usa, pero esta bien saber por si en algún momento te lo preguntan, lo que hace es **multiplicar** el contenido de las tablas para así mostrarnos todas las combinaciones posibles de ambas.

![[./images/Pasted image 20230825210119.png]]
 
##### INNER JOIN
 Es cuando **mezclamos** las **columnas** de 2 tablas cuando se cumple la **condición** especificada:
![[./images/Pasted image 20230825210030.png]]
 
```mysql
SELECT FirstName,Reward,MONTH FROM Employees e
INNER JOIN Rewards r ON e.EmployeeId = r.EmployeeID
```

Lo que estamos haciendo es que en la tabla **Rewards** había algunos **EmployeeID** que coincidían con la tabla **Employees**
Output:

![[./images/Pasted image 20230825205345.png]]

##### LEFT JOIN
Nos mostrara la **primera** tabla (en este caso **Employees**) y si cumple la condición parte de la **segunda** tabla (en este caso **Rewards**)

![[./images/Pasted image 20230825210437.png]]

```mysql
SELECT FirstName,Reward,MONTH FROM Employees e
LEFT JOIN Rewards r ON e.EmployeeId = r.EmployeeID
```
Output:
Donde pone **NULL** es porque no coincide nada y no tienen valor en la tabla **Rewards**

![[./images/Pasted image 20230825210630.png]]

##### RIGHTJOIN
Exactamente igual que **LEFT JOIN** solo que ahora muestra la **segunda** tabla entera (en este caso **Reward**)

![[./images/Pasted image 20230825210902.png]]

En SQLITE no se puede aplicar el **RIGHT JOIN** perosi podemos cambiar el **nombre** de la tabla **a** **por** el **nombre** de la tabla **b** y viceversa.
En este ejemplo sería de la siguiente manera:
```mysql
SELECT FirstName,Reward,MONTH FROM Rewards r
LEFT JOIN Employees e ON e.EmployeeID = r.EmployeeID 
```

##### FULL JOIN
Como con el **RIGHT JOIN** no se puede aplicar este tipo de JOIN en SQLite pero si podemos simularlo con **CROSS JOIN**, recordando que este multiplica las 2 tablas para sacar todas las variantes, mientras que el **FULL JOIN** nos junta ambas tablas **sumando** los valores:

```mysql
SELECT FirstName,Reward,MONTH FROM Employees e
LEFT JOIN Rewards r ON e.EmployeeId = r.EmployeeID

UNION (ALL -- ALL -> Si a = 10 y b = 6 el union all sera 16)

SELECT FirstName,Reward,MONTH FROM Rewards r
LEFT JOIN Employees e ON e.EmployeeID = r.EmployeeID 
```
Ouput:

![[./images/Pasted image 20230825211752.png]]

#### UNION y UNION ALL

![[./images/Pasted image 20230825215449.png]]

#### Indices
PD: Se suelen poner los **INDICES** en los **ID** para facilitar las **FOREIGN KEY**
Los indices nos facilitan la tarea de llamar a valores de columnas especificas o modificar su configuración:
```mysql
CREATE INDEX name ON Employees (FirstName,LastName)
```

Haciendo esto haremos que tarde menos en hacer las consultas, sin embargo podemos saturar el programa con muchos index y hacer que vaya más lento.

###### Index unique
Con **UNIQUE INDEX** podemos hacer uno o varias columnas de valores que sean unicas, como la **primary key** pero sin el autoincremento.
```mysql
CREATE UNIQUE INDEX name ON Employees(FirtName,LastName) -- Haciendo que un empleado no pueda tener el mismo nombre que otro
CREATE INDEX UNIQUE email ON Employees (contantEmail) -- Haciendo que los correos electronicos sean unicos
```

#### Vistas
Es una macro variable para guardar tablas y poder usarlas siempre (se quedan guardando ocupando memoria)
```mysql
CREATE VIEW producto_simplificados AS

SELECT ProdutID,ProductName,Price FROM Products
WHERE ProductID > 20
ORDER BY ProductID DESC
```

Ahora solo con llamar a **producto_simplificados** nos mostrara la consulta que especificamos (la usamos como si fuera una tabla nueva)
```mysql
SELECT * FROM productos_simplificados
```

Para borrarla: (Usamos **IF EXISTS** porque verifica que exista y después la elimina)
```mysql
DROP VIEW IF EXISTS producto_simplificados
```
### Teoría
#### Cardinalidad
Especifica como es la**relación** que hay **entre** 2 **tablas**/entidades
###### 1:1
![[./images/Pasted image 20230826123534.png]]

###### 1:n / n:1
![[./images/Pasted image 20230826123620.png]]

###### n:m
![[./images/Pasted image 20230826123711.png]]

##### Ejemplo
![[./images/Pasted image 20230826124628.png]]

##### Notacion de Martin (Erwin Data Modeler)
1 : One | 0 : Zero | n : Many

![[./images/Pasted image 20230826125314.png]]

![[./images/Pasted image 20230826125726.png]]


#### Normalización
Por lo general en tu vida cotidiana solo tendras que llegar hasta la **tercera etapa de normalización**, ya que la **4 y 5** no sirve salvo que juguemos con muchisimos datos como google/facebook...

Hay **5NF**, es decir, 5 etapas de normalizacion:

##### 1NF
La primeras es tan fan fácil que no vamos a profundizar, lo que índica esta estapa es que cada **valor no** sea **compuesto**, pongo un ejemplo:
###### No cumple 1NF
- España, Madird -> Ubicacón

###### Cumple 1NF
- Espala -> Pais
- Madrid -> Ciudad

##### 2NF
En esta estapa nos preocupamos para que **todos** los valores de la tabla **dependan** del **ID principal**

###### No cumple 2NF
![[./images/Pasted image 20230826132119.png]]

###### Cumple 2NF
![[./images/Pasted image 20230826132306.png]]
![[./images/Pasted image 20230826132313.png]]

##### 3NF
**No** puede haber **dependencias transitivas**, es como **2NF** pero recursiva. Se suele usar con **KEY FOREIGN**.

###### No cumple 3NF
![[./images/Pasted image 20230826132453.png]]

###### Cumple 3NF
![[./images/Pasted image 20230826132508.png]]
![[./images/Pasted image 20230826132514.png]]

##### 4NF
Eliminar **dependencias múltiples** de valores, diviendo la base de datos para solucionarlo.

###### No cumple 4NF
![[./images/Pasted image 20230826132751.png]]

###### Cumple 4NF
![[./images/Pasted image 20230826132714.png]]
![[./images/Pasted image 20230826132741.png]]

##### 5NF
La Quinta Forma Normal (5NF) en bases de datos relacionales se centra en eliminar las dependencias de unión, que son situaciones en las que un atributo no clave depende de otras relaciones entre conjuntos de datos, en lugar de depender directamente de una clave primaria compuesta.

#### Bloqueos
1. Bloqueo compartido -> Cuando lo lees -> Tu puede escribir y el resto leer
2. Bloqueo reservado ->  Cuando escrbies -> El rresto pueden leer
3. Bloqueo exclisivo -> Cuando lo lees -> Y el resto no leen ni escriben

#### Transacciones
Si empezamos la petición con **BEGIN** y queremos hacer un **Ctrl+Z** podemos hacerlo con **ROLLBACK** y para finalizar el **BEGIN** usamos un **COMMIT**
```mysql
BEGIN;
UPDATE ...
-- Ahora podemos ver, y si vemos que no queremos aplicar cambios en una nueva peticion ponemos:
ROLLBACK

-- De lo contrario, queremos guardar los cambios usamos un:
COMMIT
```
