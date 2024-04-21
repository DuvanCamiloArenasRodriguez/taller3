# TALLER 3 - BASES DE DATOS

**Estudiante:** Duván Camilo Arenas Rodríguez - J1

En este taller basado en las tablas iniciales sin normalizar, se normalizó hasta el 4° grado, teniendo como resultado final 21 tablas:

- cargo
- ciudad
- cliente 
- contacto
- detalle_pedido
- dimension
- direccion
- empleado
- estado
- gama_producto
- metodo_pago
- oficina 
- pago 
- pais
- pedido
- producto
- proveedor
- proveedor_producto
- region 
- telefono
- tipo_telefono

### Comando DDL y DML

Para este taller se implementó el siguiente comando DDL:

```mysql
CREATE TABLE gama_producto(
    id_gama INT AUTO_INCREMENT NOT NULL,
    nombre VARCHAR(50) NOT NULL,
    descripcion_texto TEXT NULL,
    descripcion_html TEXT NULL,
    imagen VARCHAR(256) NULL,
    CONSTRAINT PK_gama_producto PRIMARY KEY (id_gama)
);
```

Este mismo comando se implementó para las demás tablas de las que consta la base de datos.

En cuanto al comando DML, se implementó el siguiente comando:

```mysql
INSERT INTO producto (nombre, cantidad_stock, precio_venta, precio_proveedor, descripcion_producto, codigo_gama, codigo_dimension)
    VALUES ('Planta de Menta', 25, 4.49, 2.75, 'Planta de menta fresca para infusiones.', 1, 1);
```

Este mismo se replicó para el resto de información y las demás tablas.



Como resultado de la normalización de cliente, pedido, oficina y producto, como primera instancia se declaró una llave primaria, seguidamente se determinó si había tablas repetidas. En el segundo nivel de normalización se eliminaron datos redundantes y se crearon tablas independientes vinculados con registros de las tablas iniciales, tales como dirección, proveedor, ciudad, país, región, entre otras. En el tercer nivel, se eliminaron aquellos campos no dependientes de la clave, como el estado, para el cual se asignó una tabla independiente, al que para el cargo del empleado. Por último la cuarta forma de normalización se implementó al eliminar las relaciones "muchos a muchos" de tablas como producto-proveedor, entre otros.



### DER de la base de datos:

![](https://github.com/DuvanCamiloArenasRodriguez/taller3/blob/main/images/BaseDatosTaller3.png?raw=true)



## Consultas:

### Consultas sobre una tabla

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

   ```mysql
   SELECT o.id_oficina, c.nombre
   FROM oficina AS o
   INNER JOIN direccion as dir ON dir.id_direccion = o.codigo_direccion_o
   INNER JOIN ciudad as c ON c.id_ciudad = dir.codigo_ciudad_d;
   +------------+-------------+
   | id_oficina | nombre      |
   +------------+-------------+
   |          1 | Madrid      |
   |          2 | Fuenlabrada |
   |          3 | Barcelona   |
   |          4 | Madrid      |
   +------------+-------------+
   ```

   

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

   ```mysql
   SELECT c.nombre, t.numero
   FROM telefono as t
   INNER JOIN oficina as o ON o.id_oficina = t.codigo_oficina_te
   INNER JOIN direccion as d ON o.codigo_direccion_o = d.id_direccion
   INNER JOIN ciudad as c ON c.id_ciudad = d.codigo_ciudad_d
   INNER JOIN region as r ON r.id_region = c.codigo_region
   INNER JOIN pais as p ON p.id_pais = r.codigo_pais
   WHERE p.id_pais = 2;
   +-------------+------------+
   | nombre      | numero     |
   +-------------+------------+
   | Madrid      | 3123456789 |
   | Madrid      | 5712345678 |
   | Fuenlabrada | 4123456789 |
   +-------------+------------+
   ```

   

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

   ```mysql
   SELECT nombre_empleado, apellido1, apellido2, email
   FROM empleado
   WHERE id_jefe = 7;
   +-----------------+-----------------+-----------+------------------------+
   | nombre_empleado | apellido1       | apellido2 | email                  |
   +-----------------+-----------------+-----------+------------------------+
   | Sofía           | Pérez           | NULL      | sofia.romero@ejemplo.com   |
   | Diego           | Ramírez			 | NULL      | diego.martinez@ejemplo.com |
   | Andrea          | García          | NULL      | andrea.cala@ejemplo.com     |
   +-----------------+-----------------+-----------+------------------------+
   ```

   

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

   ```mysql
   SELECT c.nombre_cargo, e.nombre_empleado, e.apellido1, e.apellido2, e.email
   FROM empleado as e, cargo as c
   WHERE id_jefe = 6 AND c.id_cargo = e.codigo_cargo;
   +------------------+-----------------+-----------+-----------+---------------------+
   | nombre_cargo     | nombre_empleado | apellido1 | apellido2 | email               |
   +------------------+-----------------+-----------+-----------+---------------------+
   | Director Oficina | Laura           | González  | NULL      | lauraG@ejemplo.com |
   +------------------+-----------------+-----------+-----------+---------------------+
   ```

   

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

   ```mysql
   SELECT e.nombre_empleado, e.apellido1, e.apellido2
   FROM empleado as e, cargo as c
   WHERE e.codigo_cargo != 5 AND e.codigo_cargo = id_cargo;
   +-----------------+-----------+-----------+
   | nombre_empleado | apellido1 | apellido2 |
   +-----------------+-----------+-----------+
   | María           | García    | López     |
   | Edwind          | Hernandez | NULL      |
   | Juan            | Pérez     | Gómez     |
   | Laura           | González  | NULL      |
   +-----------------+-----------+-----------+
   ```

   

6. Devuelve un listado con el nombre de los todos los clientes españoles.

   ```mysql
   SELECT cl.nombre_cliente
   FROM cliente as cl
   INNER JOIN ciudad as c ON cl.codigo_ciudad_c = c.id_ciudad
   INNER JOIN region as r ON r.id_region = c.codigo_region
   INNER JOIN pais as p ON p.id_pais = r.codigo_pais
   WHERE p.id_pais = 2;
   +-------------------+
   | nombre_cliente    |
   +-------------------+
   | Roberto Martínez  |
   | Alejandra López   |
   | Fernando Sánchez  |
   | Isabel Gutiérrez  |
   | Gabriel Rodríguez |
   | Verónica Pérez    |
   | Marina Fernández  |
   | Andrés Ramírez    |
   +-------------------+
   ```

   

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

   ```mysql
   SELECT es.nombre_estado
   FROM estado as es;
   +---------------+
   | nombre_estado |
   +---------------+
   | Entregado     |
   | Rechazado     |
   | Pendiente     |
   +---------------+
   ```

   

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

   - Utilizando la función YEAR de MySQL.

   - Utilizando la función DATE_FORMAT de MySQL.

   - Sin utilizar ninguna de las funciones anteriores.

     ```mysql
     SELECT cl.id_cliente 
     FROM cliente as cl, pago as p
     WHERE YEAR(p.fecha_pago) = 2008 AND cl.id_cliente = p.codigo_cliente_pa;
     +------------+
     | id_cliente |
     +------------+
     |          1 |
     |          2 |
     +------------+
     SELECT cl.id_cliente 
     FROM cliente as cl, pago as p
     WHERE DATE_FORMAT(p.fecha_pago, '%Y') = 2008 AND cl.id_cliente = p.codigo_cliente_pa;
     +------------+
     | id_cliente |
     +------------+
     |          1 |
     |          2 |
     +------------+
     ```

     

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

   ```mysql
   SELECT ped.id_pedido, cl.id_cliente, ped.fecha_esperado, ped.fecha_entrega
   FROM cliente as cl, pedido as ped
   WHERE cl.id_cliente = ped.codigo_client_pedido AND ped.fecha_entrega <= ped.fecha_esperado;
   +-----------+------------+----------------+---------------+
   | id_pedido | id_cliente | fecha_esperado | fecha_entrega |
   +-----------+------------+----------------+---------------+
   |         2 |          1 | 2024-04-06     | 2024-04-06    |
   |         3 |          1 | 2024-04-07     | 2024-04-07    |
   |         5 |          3 | 2024-04-09     | 2024-04-09    |
   |         7 |          3 | 2024-04-11     | 2024-04-11    |
   |        10 |          2 | 2024-04-14     | 2024-04-12    |
   +-----------+------------+----------------+---------------+
   ```

   

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

    - Utilizando la función ADDDATE de MySQL.

    - Utilizando la función DATEDIFF de MySQL.

    - ¿Sería posible resolver esta consulta utilizando el operador de suma + o resta -?

      ```mysql
      SELECT ped.id_pedido, cl.id_cliente, ped.fecha_esperado, ped.fecha_entrega
      FROM pedido as ped, cliente as cl 
      WHERE cl.id_cliente = ped.codigo_client_pedido AND ped.fecha_entrega < ADDDATE(ped.fecha_esperado, INTERVAL 2 DAY);
      +-----------+------------+----------------+---------------+
      | id_pedido | id_cliente | fecha_esperado | fecha_entrega |
      +-----------+------------+----------------+---------------+
      |        10 |          2 | 2024-04-14     | 2024-04-12    |
      +-----------+------------+----------------+---------------+
      SELECT ped.id_pedido, cl.id_cliente, ped.fecha_esperado, ped.fecha_entrega
      FROM pedido as ped, cliente as cl
      WHERE cl.id_cliente = ped.codigo_client_pedido AND DATEDIFF(ped.fecha_esperado, ped.fecha_entrega) > 1;
      
      ```

      

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

    ```mysql
    SELECT id_pedido, fecha_esperado, fecha_entrega
    FROM pedido
    WHERE YEAR(fecha_pedido) = '2009' AND codigo_estado_pedido = 2;
    
    Este espacio es vacío porque no hubo pedidos rechazados en el 2009
    ```

    

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.

    ```mysql
    SELECT id_pedido, fecha_esperado, fecha_entrega
    FROM pedido
    WHERE MONTH(fecha_entrega) = 01;
    
    Este espacio es vacío porque no hubo pedidos entregados en el mes de enero
    ```

    

13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.

    ```mysql
    SELECT id_pago, fecha_pago, total, codigo_metodo_pago, codigo_cliente_pa
    FROM pago 
    WHERE YEAR(fecha_pago) = 2008 AND codigo_metodo_pago = 1;
    +---------+------------+-------+--------------------+-------------------+
    | id_pago | fecha_pago | total | codigo_metodo_pago | codigo_cliente_pa |
    +---------+------------+-------+--------------------+-------------------+
    |       6 | 2008-02-28 |   140 |                  1 |                 1 |
    |       7 | 2008-11-02 |   180 |                  1 |                 2 |
    +---------+------------+-------+--------------------+-------------------+
    ```

    

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.

    ```mysql
    SELECT id_metodo_pago, nombre_metodo
    FROM metodo_pago;
    +----------------+---------------+
    | id_metodo_pago | nombre_metodo |
    +----------------+---------------+
    |              1 | PayPal        |
    |              2 | Transferencia |
    |              3 | Cheque        |
    +----------------+---------------+
    ```

    

15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

    ```mysql
    SELECT pr.nombre, pr.cantidad_stock
    FROM producto as pr, gama_producto as gama
    WHERE pr.cantidad_stock > 100 AND gama.nombre = 'Ornamentales' AND gama.id_gama = pr.codigo_gama
    ORDER BY pr.precio_venta DESC;
    +--------------+----------------+
    | nombre       | cantidad_stock |
    +--------------+----------------+
    | Lirio Blanco |             125 |
    +--------------+----------------+
    ```

    

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.

    ```mysql
    SELECT cl.id_cliente, cl.nombre_cliente, cl.limite_credito, cl.codigo_ciudad_c
    FROM cliente as cl
    INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas
    WHERE cl.codigo_ciudad_c = 1 AND  cl.codigo_empleado_rep_ventas IN(11, 30);
    +------------+------------------+----------------+-----------------+
    | id_cliente | nombre_cliente   | limite_credito | codigo_ciudad_c |
    +------------+------------------+----------------+-----------------+
    |          1 | Roberto Martínez |           5000 |               1 |
    +------------+------------------+----------------+-----------------+
    ```

    

### Consultas multitabla (Composición interna)

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

   ```mysql
   SELECT cl.nombre_cliente, emp.nombre_empleado, emp.apellido1, emp.apellido2
   FROM cliente as cl
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas;
   +-------------------+-----------------+-----------+-----------+
   | nombre_cliente    | nombre_empleado | apellido1 | apellido2 |
   +-------------------+-----------------+-----------+-----------+
   | Roberto Martínez  | Pedro           | Romero    | Sánchez   |
   | Alejandra López   | Ana             | Martínez  | Fernández |
   | Fernando Sánchez  | Ana             | Martínez  | Fernández |
   | Isabel Gutiérrez  | Andrea          | García    | NULL      |
   | Gabriel Rodríguez | Sofía           | Pérez     | NULL      |
   | Verónica Pérez    | Sofía           | Pérez     | NULL      |
   +-------------------+-----------------+-----------+-----------+
   ```

   

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el  nombre de sus representantes de ventas.

   ```mysql
   SELECT DISTINCT cl.nombre_cliente, emp.nombre_empleado
   FROM cliente as cl
   INNER JOIN pago as p ON cl.id_cliente = p.codigo_cliente_pa
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas;
   +------------------+-----------------+
   | nombre_cliente   | nombre_empleado |
   +------------------+-----------------+
   | Roberto Martínez | Pedro           |
   | Alejandra López  | Ana             |
   | Fernando Sánchez | Ana             |
   +------------------+-----------------+
   ```

   

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con  el nombre de sus representantes de ventas.

   ```mysql
   SELECT DISTINCT cl.nombre_cliente, emp.nombre_empleado
   FROM cliente as cl
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas
   WHERE cl.id_cliente NOT IN(
       SELECT codigo_cliente_pa
       FROM pago
   );
   +-------------------+-----------------+
   | nombre_cliente    | nombre_empleado |
   +-------------------+-----------------+
   | Isabel Gutiérrez  | Andrea          |
   | Gabriel Rodríguez | Sofía           |
   | Verónica Pérez    | Sofía           |
   +-------------------+-----------------+
   ```

   

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el  representante.

   ```mysql
   SELECT DISTINCT cl.nombre_cliente, emp.nombre_empleado, city.nombre as 'Ciudad'
   FROM cliente as cl
   INNER JOIN pago as p ON cl.id_cliente = p.codigo_cliente_pa
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas
   INNER JOIN oficina as ofi ON emp.codigo_oficina = ofi.id_oficina
   INNER JOIN direccion as d ON ofi.codigo_direccion_o = d.id_direccion
   INNER JOIN ciudad as city ON d.codigo_ciudad_d = city.id_ciudad;
   +------------------+-----------------+--------+
   | nombre_cliente   | nombre_empleado | Ciudad |
   +------------------+-----------------+--------+
   | Roberto Martínez | Pedro           | Madrid |
   | Alejandra López  | Ana             | Madrid |
   | Fernando Sánchez | Ana             | Madrid |
   +------------------+-----------------+--------+
   ```

   

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre  de sus representantes junto con la ciudad de la oficina a la que pertenece el  representante.

   ```mysql
   SELECT DISTINCT cl.nombre_cliente, emp.nombre_empleado, city.nombre as 'Ciudad'
   FROM cliente as cl
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas
   INNER JOIN oficina as ofi ON emp.codigo_oficina = ofi.id_oficina
   INNER JOIN direccion as d ON ofi.codigo_direccion_o = d.id_direccion
   INNER JOIN ciudad as city ON d.codigo_ciudad_d = city.id_ciudad
   WHERE cl.id_cliente NOT IN(
       SELECT codigo_cliente_pa
       FROM pago
   );
   +-------------------+-----------------+-------------+
   | nombre_cliente    | nombre_empleado | Ciudad      |
   +-------------------+-----------------+-------------+
   | Gabriel Rodríguez | Sofía           | Fuenlabrada |
   | Verónica Pérez    | Sofía           | Fuenlabrada |
   | Isabel Gutiérrez  | Andrea          | Fuenlabrada |
   +-------------------+-----------------+-------------+
   ```

   

6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.

   ```mysql
   SELECT DISTINCT d.linea_direccion1, d.linea_direccion2
   FROM cliente as cl
   INNER JOIN empleado as emp ON emp.id_empleado = cl.codigo_empleado_rep_ventas
   INNER JOIN oficina as ofi ON ofi.id_oficina = emp.codigo_oficina
   INNER JOIN direccion as d ON ofi.codigo_direccion_o = d.id_direccion
   WHERE cl.codigo_ciudad_c = 2;
   +-----------------------------+------------------+
   | linea_direccion1            | linea_direccion2 |
   +-----------------------------+------------------+
   | Avenida San Carlos, 456 | Piso 1, Puerta 5 |
   +-----------------------------+------------------+
   ```

   

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto  con la ciudad de la oficina a la que pertenece el representante.

   ```mysql
   SELECT c.nombre_cliente AS Nombre_Cliente, CONCAT(e.nombre_empleado, ' ', e.apellido1) AS Representante, ciu.nombre AS Ciudad_Oficina_Representante
   FROM cliente AS c
   INNER JOIN empleado AS e ON c.codigo_empleado_rep_ventas = e.id_empleado
   INNER JOIN oficina AS o ON e.codigo_oficina = o.id_oficina
   INNER JOIN direccion AS dir ON o.codigo_direccion_o = dir.id_direccion
   INNER JOIN ciudad AS ciu ON dir.codigo_ciudad_d = ciu.id_ciudad;
   +-------------------+---------------+------------------------------+
   | Nombre_Cliente    | Representante | Ciudad_Oficina_Representante |
   +-------------------+---------------+------------------------------+
   | Roberto Martínez  | Pedro Romero  | Madrid                       |
   | Alejandra López   | Ana Martínez  | Madrid                       |
   | Fernando Sánchez  | Ana Martínez  | Madrid                       |
   | Gabriel Rodríguez | Sofía Pérez   | Fuenlabrada                  |
   | Verónica Pérez    | Sofía Pérez   | Fuenlabrada                  |
   | Isabel Gutiérrez  | Andrea García | Fuenlabrada                  |
   +-------------------+---------------+------------------------------+
   ```

   

8. Devuelve un listado con el nombre de los empleados junto con el nombre  de sus jefes.

   ```mysql
   SELECT e.nombre_empleado AS 'Nombre Empleado', CONCAT(j.nombre_empleado, ' ', j.apellido1) AS 'Nombre Jefe'
   FROM empleado AS e
   LEFT JOIN empleado AS j ON e.id_jefe = j.id_empleado;
   +-----------------+------------------+
   | Nombre Empleado | Nombre Jefe      |
   +-----------------+------------------+
   | María           | NULL             |
   | Juan            | María García     |
   | Pedro           | Juan Pérez       |
   | Ana             | Juan Pérez       |
   | José            | Juan Pérez       |
   | Edwind          | NULL             |
   | Laura           | Edwind Hernandez |
   | Sofía           | Laura  González  |
   | Diego           | Laura  González  |
   | Andrea          | Laura  González  |
   +-----------------+------------------+
   ```

   

9. Devuelve un listado que muestre el nombre de cada empleados, el nombre  de su jefe y el nombre del jefe de sus jefe.

   ```mysql
   SELECT emp1.nombre_empleado AS 'Nombre Empleado', CONCAT(emp2.nombre_empleado, ' ', emp2.apellido1) AS 'Nombre Jefe', CONCAT(emp3.nombre_empleado, ' ', emp3.apellido1) AS 'Jefe De Jefe'
   FROM empleado AS e1
   LEFT JOIN empleado AS emp2 ON emp1.id_jefe = emp2.id_empleado
   LEFT JOIN empleado AS emp3 ON emp2.id_jefe = emp3.id_empleado;
   +-----------------+------------------+---------------------+
   | Nombre_Empleado | Nombre_Jefe      | Nombre_Jefe_De_Jefe |
   +-----------------+------------------+---------------------+
   | María           | NULL             | NULL                |
   | Juan            | María García     | NULL                |
   | Pedro           | Juan Pérez       | María García        |
   | Ana             | Juan Pérez       | María García        |
   | José            | Juan Pérez       | María García        |
   | Edwind          | NULL             | NULL                |
   | Laura           | Edwind Hernandez | NULL                |
   | Sofía           | Laura  González  | Edwind Hernandez    |
   | Diego           | Laura  González  | Edwind Hernandez    |
   | Andrea          | Laura  González  | Edwind Hernandez    |
   +-----------------+------------------+---------------------+
   ```

   

10. Devuelve el nombre de los clientes a los que no se les ha entregado a  tiempo un pedido.

    ```mysql
    SELECT DISTINCT cl.nombre_cliente AS Nombre_Cliente
    FROM cliente AS cl
    INNER JOIN pedido AS p ON cl.id_cliente = p.codigo_client_pedido
    WHERE
        p.fecha_entrega > p.fecha_esperado;
    +------------------+
    | Nombre_Cliente   |
    +------------------+
    | Roberto Martínez |
    | Fernando Sánchez |
    | Alejandra López  |
    +------------------+
    ```

    

11. Devuelve un listado de las diferentes gamas de producto que ha comprado  cada cliente.

    ```mysql
    SELECT DISTINC cL.nombre_cliente AS Nombre_Cliente, gp.nombre AS Gama_Producto
    FROM cliente AS cL
    INNER JOIN pedido AS p ON cl.id_cliente = p.codigo_client_pedido
    INNER JOIN detalle_pedido AS dp ON p.id_pedido = dp.id_pedido_producto
    INNER JOIN producto AS pr ON dp.id_producto_pedido = pr.id_producto
    INNER JOIN gama_producto AS gp ON pr.codigo_gama = gp.id_gama
    ORDER BY Nombre_Cliente, Gama_Producto;
    +------------------+---------------+
    | Nombre_Cliente   | Gama_Producto |
    +------------------+---------------+
    | Alejandra López  | Frutales      |
    | Alejandra López  | Herbaceas     |
    | Alejandra López  | Ornamentales  |
    | Fernando Sánchez | Aromáticas    |
    | Fernando Sánchez | Frutales      |
    | Fernando Sánchez | Herbaceas     |
    | Fernando Sánchez | Herramientas  |
    | Roberto Martínez | Frutales      |
    | Roberto Martínez | Herbaceas     |
    | Roberto Martínez | Herramientas  |
    | Roberto Martínez | Ornamentales  |
    +------------------+---------------+
    ```

    

### Consultas multitabla (Composición externa)

1. Devuelve un listado que muestre solamente los clientes que no han  realizado ningún pago.

   ```mysql
   SELECT cl.nombre_cliente AS 'Nombre Cliente'
   FROM cliente AS cl
   LEFT JOIN pago AS p ON cl.id_cliente = p.codigo_cliente_pa
   WHERE p.id_pago IS NULL;
   +-------------------+
   | Nombre Cliente    |
   +-------------------+
   | Isabel Gutiérrez  |
   | Gabriel Rodríguez |
   | Verónica Pérez    |
   | Marina Fernández  |
   | Andrés Ramírez    |
   +-------------------+
   ```

   

2. Devuelve un listado que muestre solamente los clientes que no han  realizado ningún pedido.

   ```mysql
   SELECT cl.nombre_cliente AS Nombre_Cliente
   FROM cliente cl
   LEFT JOIN pedido p ON cl.id_cliente = p.codigo_client_pedido
   WHERE p.id_pedido IS NULL;
   +-------------------+
   | Nombre_Cliente    |
   +-------------------+
   | Isabel Gutiérrez  |
   | Gabriel Rodríguez |
   | Verónica Pérez    |
   | Marina Fernández  |
   | Andrés Ramírez    |
   +-------------------+
   ```

   

3. Devuelve un listado que muestre los clientes que no han realizado ningún  pago y los que no han realizado ningún pedido. 

   ```mysql
   SELECT cl.nombre_cliente AS Nombre_Cliente, 'Sin Pagos' AS Estado
   FROM cliente cl
   LEFT JOIN pago pa ON cl.id_cliente = pa.codigo_cliente_pa
   WHERE pa.id_pago IS NULL
   UNION
   SELECT c2.nombre_cliente AS Nombre_Cliente, 'Sin Pedidos' AS Estado
   FROM cliente c2
   LEFT JOIN pedido pe ON c2.id_cliente = pe.codigo_client_pedido
   WHERE pe.id_pedido IS NULL;
   +-------------------+-------------+
   | Nombre_Cliente    | Estado      |
   +-------------------+-------------+
   | Isabel Gutiérrez  | Sin Pagos   |
   | Gabriel Rodríguez | Sin Pagos   |
   | Verónica Pérez    | Sin Pagos   |
   | Marina Fernández  | Sin Pagos   |
   | Andrés Ramírez    | Sin Pagos   |
   | Isabel Gutiérrez  | Sin Pedidos |
   | Gabriel Rodríguez | Sin Pedidos |
   | Verónica Pérez    | Sin Pedidos |
   | Marina Fernández  | Sin Pedidos |
   | Andrés Ramírez    | Sin Pedidos |
   +-------------------+-------------+
   ```

   

4. Devuelve un listado que muestre solamente los empleados que no tienen  una oficina asociada.

   ```mysql
   SELECT emp.nombre_empleado AS 'Nombre Empleado'
   FROM empleado emp
   WHERE emp.codigo_oficina IS NULL;
   +-----------------+
   | 'Nombre Empleado' |
   +-----------------+
   | María           |
   | Edwind          |
   +-----------------+
   ```

   

5. Devuelve un listado que muestre solamente los empleados que no tienen un  cliente asociado. 

   ```mysql
   SELECT emp.nombre_empleado AS Nombre_Empleado
   FROM empleado AS emp
   WHERE emp.id_empleado NOT IN (
       SELECT codigo_empleado_rep_ventas
       FROM cliente WHERE codigo_empleado_rep_ventas IS NOT NULL
   );
   +-----------------+
   | Nombre_Empleado |
   +-----------------+
   | María           |
   | Juan            |
   | José            |
   | Edwind          |
   | Laura           |
   | Diego           |
   +-----------------+
   ```

   

6. Devuelve un listado que muestre solamente los empleados que no tienen un  cliente asociado junto con los datos de la oficina donde trabajan.

   ```mysql
   SELECT emp.nombre_empleado AS 'Nombre Empleado', ofi.id_oficina AS 'ID Oficina', ofi.codigo_direccion_o AS 'Codigo Direccion Oficina'
   FROM empleado AS emp
   LEFT JOIN oficina AS ofi ON emp.codigo_oficina = ofi.id_oficina
   WHERE emp.id_empleado NOT IN (SELECT codigo_empleado_rep_ventas FROM cliente WHERE codigo_empleado_rep_ventas IS NOT NULL);
   +-----------------+------------+--------------------------+
   | Nombre Empleado | ID Oficina | Codigo Direccion Oficina |
   +-----------------+------------+--------------------------+
   | María           |       NULL |                     NULL |
   | Juan            |          1 |                        1 |
   | José            |          1 |                        1 |
   | Edwind          |       NULL |                     NULL |
   | Laura           |          2 |                        2 |
   | Diego           |          2 |                        2 |
   +-----------------+------------+--------------------------+
   ```

   

7. Devuelve un listado que muestre los empleados que no tienen una oficina  asociada y los que no tienen un cliente asociado. 

   ```mysql
   SELECT e.nombre_empleado AS 'Nombre Empleado', 'Sin Oficina' AS Estado, NULL AS 'ID Oficina', NULL AS 'Codigo Direccion Oficina'
   FROM empleado AS e
   WHERE e.codigo_oficina IS NULL
   UNION
   SELECT e.nombre_empleado AS 'Nombre Empleado', 'Con Oficina' AS Estado, o.id_oficina AS 'ID Oficina', o.codigo_direccion_o AS 'Codigo Direccion Oficina'
   FROM empleado e
   LEFT JOIN oficina o ON e.codigo_oficina = o.id_oficina
   WHERE e.id_empleado NOT IN (SELECT codigo_empleado_rep_ventas FROM cliente WHERE codigo_empleado_rep_ventas IS NOT NULL);
   +-----------------+-------------+------------+--------------------------+
   | Nombre_Empleado | Estado      | ID_Oficina | Codigo_Direccion_Oficina |
   +-----------------+-------------+------------+--------------------------+
   | María           | Sin Oficina |       NULL |                     NULL |
   | Edwind          | Sin Oficina |       NULL |                     NULL |
   | María           | Con Oficina |       NULL |                     NULL |
   | Juan            | Con Oficina |          1 |                        1 |
   | José            | Con Oficina |          1 |                        1 |
   | Edwind          | Con Oficina |       NULL |                     NULL |
   | Laura           | Con Oficina |          2 |                        2 |
   | Diego           | Con Oficina |          2 |                        2 |
   +-----------------+-------------+------------+--------------------------+
   ```

   

8. Devuelve un listado de los productos que nunca han aparecido en un  pedido.

   ```mysql
   SELECT pr.id_producto, pr.nombre AS 'Nombre Producto'
   FROM producto AS pr
   LEFT JOIN detalle_pedido dp ON p.id_producto = dp.id_producto_pedido
   WHERE dp.id_producto_pedido IS NULL;
   +-------------+-----------------+
   | id_producto | Nombre_Producto |
   +-------------+-----------------+
   |          14 | Fresas Frescas  |
   |          15 | Rosa Roja       |
   +-------------+-----------------+
   ```

   

9. Devuelve un listado de los productos que nunca han aparecido en un  pedido. El resultado debe mostrar el nombre, la descripción y la imagen del  producto.

   ```mysql
   SELECT pr.nombre, pr.descripcion_producto, gp.imagen
   FROM producto pr
   LEFT JOIN gama_producto gp ON pr.codigo_gama = gp.id_gama
   LEFT JOIN detalle_pedido dp ON pr.id_producto = dp.id_producto_pedido
   WHERE dp.id_producto_pedido IS NULL;
   +----------------+----------------------------------+--------+
   | nombre         | descripcion_producto             | imagen |
   +----------------+----------------------------------+--------+
   | Fresas Frescas | Fresas maduras y jugosas.        | NULL   |
   | Rosa Roja      | Rosa roja de floración temprana. | NULL   |
   +----------------+----------------------------------+--------+
   ```

   

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que  hayan sido los representantes de ventas de algún cliente que haya realizado  la compra de algún producto de la gama Frutales.

    ```mysql
    
    ```

    

11. Devuelve un listado con los clientes que han realizado algún pedido pero no  han realizado ningún pago.

    ```mysql
    
    ```

    

12. Devuelve un listado con los datos de los empleados que no tienen clientes  asociados y el nombre de su jefe asociado.

    ```mysql
    SELECT emp.id_empleado, emp.nombre_empleado, emp.apellido1, emp.apellido2, j.nombre_empleado AS 'Nombre Jefe'
    FROM empleado AS emp
    LEFT JOIN empleado AS je ON e.id_jefe = j.id_empleado
    LEFT JOIN cliente AS cl ON e.id_empleado = c.codigo_empleado_rep_ventas
    WHERE cl.id_cliente IS NULL;
    +-------------+-----------------+-----------------+-----------+-------------+
    | id_empleado | nombre_empleado | apellido1       | apellido2 | nombre_jefe |
    +-------------+-----------------+-----------------+-----------+-------------+
    |           1 | María           | García          | López     | NULL        |
    |           2 | Juan            | Pérez           | Gómez     | María       |
    |           5 | José            | Cala            | Marin     | Juan        |
    |           6 | Edwind          | Hernandez       | NULL      | NULL        |
    |           7 | Laura           | González        | NULL      | Edwind      |
    |           9 | Diego           | MarRamíreztínez | NULL      | Laura       |
    +-------------+-----------------+-----------------+-----------+-------------+
    ```

    

### Consultas resumen

1. ¿Cuántos empleados hay en la compañía?

   ```mysql
   SELECT COUNT(nombre_empleado) AS Empleados
   FROM empleado;
   +-----------------+
   | Empleados |
   +-----------------+
   |              10 |
   +-----------------+
   ```

   

2. ¿Cuántos clientes tiene cada país? 

   ```mysql
   SELECT p.nombre AS 'País', COUNT(c.id_cliente) AS 'Total Clientes'
   FROM pais p
   LEFT JOIN region r ON p.id_pais = r.codigo_pais
   LEFT JOIN ciudad ci ON r.id_region = ci.codigo_region
   LEFT JOIN cliente c ON ci.id_ciudad = c.codigo_ciudad_c
   GROUP BY p.nombre;
   +----------------+----------------+
   | pais           | Total Clientes |
   +----------------+----------------+
   | USA            |              0 |
   | Spain          |              8 |
   | France         |              0 |
   | Australia      |              0 |
   | United Kingdom |              0 |
   +----------------+----------------+
   ```

   

3. ¿Cuál fue el pago medio en 2009?

   ```mysql
   SELECT AVG(total) AS 'Promedio de pagos 2009'
   FROM pago
   WHERE YEAR(fecha_pago) = 2009;
   +------------------------+
   | Promedio de pagos 2009 |
   +------------------------+
   |                    300 |
   +------------------------+
   ```

   

4.  ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma  descendente por el número de pedidos.

   ```mysql
   SELECT e.nombre_estado, COUNT(p.id_pedido) AS cantidad_pedidos
   FROM pedido p
   INNER JOIN estado e ON p.codigo_estado_pedido = e.id_estado
   GROUP BY e.nombre_estado
   ORDER BY cantidad_pedidos DESC;
   +---------------+------------------+
   | nombre_estado | cantidad_pedidos |
   +---------------+------------------+
   | Entregado     |                5 |
   | Rechazado     |                3 |
   | Pendiente     |                2 |
   +---------------+------------------+
   ```

   

5.  Calcula el precio de venta del producto más caro y más barato en una  misma consulta.

   ```mysql
   SELECT MAX(precio_venta) AS 'Precio Máximo', MIN(precio_venta) AS 'Precio Mínimo'
   FROM producto;
   +---------------+---------------+
   | Precio Máximo | Precio Mínimo |
   +---------------+---------------+
   |        199.99 |          0.99 |
   +---------------+---------------+
   ```

   

6. Calcula el número de clientes que tiene la empresa.

   ```mysql
   SELECT COUNT(nombre_cliente) AS numero_clientes
   FROM cliente;
   +-----------------+
   | numero_clientes |
   +-----------------+
   |               8 |
   +-----------------+
   ```

   

7. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

   ```mysql
   SELECT COUNT(nombre_cliente) AS 'Clientes Madrid'
   FROM cliente c
   JOIN ciudad ci ON c.codigo_ciudad_c = ci.id_ciudad
   WHERE ci.nombre = 'Madrid';
   +-----------------+
   | Clientes Madrid |
   +-----------------+
   |               3 |
   +-----------------+
   ```

   

8.  ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan  por M?

   ```mysql
   SELECT ci.nombre AS ciudad, COUNT(ci.nombre) AS total_clientes
   FROM cliente c
   JOIN ciudad ci ON c.codigo_ciudad_c = ci.id_ciudad
   WHERE ci.nombre LIKE 'M%'
   GROUP BY ci.nombre;
   +--------+----------------+
   | ciudad | total_clientes |
   +--------+----------------+
   | Madrid |              3 |
   +--------+----------------+
   ```

    

9. Devuelve el nombre de los representantes de ventas y el número de clientes  al que atiende cada uno. 

   ```mysql
   SELECT e.nombre_empleado AS representante_ventas, COUNT(cl.id_cliente) AS numero_clientes
   FROM empleado AS e
   LEFT JOIN cliente cl ON e.id_empleado = c.codigo_empleado_rep_ventas
   WHERE e.codigo_cargo = 5
   GROUP BY e.nombre_empleado;
   +----------------------+-----------------+
   | representante_ventas | numero_clientes |
   +----------------------+-----------------+
   | Pedro                |               1 |
   | Ana                  |               2 |
   | José                 |               0 |
   | Sofía                |               2 |
   | Diego                |               0 |
   | Andrea               |               1 |
   +----------------------+-----------------+
   ```

   

10. Calcula el número de clientes que no tiene asignado representante de  ventas.

    ```mysql
    SELECT COUNT(nombre_cliente) AS clientes
    FROM cliente
    WHERE codigo_empleado_rep_ventas IS NULL;
    +----------------------------+
    | clientes                   |
    +----------------------------+
    |                          2 |
    +----------------------------+
    ```

    

11. Calcula la fecha del primer y último pago realizado por cada uno de los  clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente. 

    ```mysql
    SELECT cl.nombre_cliente, MIN(pa.fecha_pago) AS 'Primera fecha de pago', MAX(pa.fecha_pago) AS 'Última fecha de pago'
    FROM cliente cl
    LEFT JOIN pago pa ON c.id_cliente = pa.codigo_cliente_pa
    GROUP BY cl.nombre_cliente;
    +-------------------+--------------------+-------------------+
    | nombre_cliente    | Primera fecha de pago | Última fecha de pago |
    +-------------------+--------------------+-------------------+
    | Roberto Martínez  | 2008-02-28         | 2024-04-04        |
    | Alejandra López   | 2008-11-02         | 2024-04-02        |
    | Fernando Sánchez  | 2009-06-21         | 2024-04-05        |
    | Isabel Gutiérrez  | NULL               | NULL              |
    | Gabriel Rodríguez | NULL               | NULL              |
    | Verónica Pérez    | NULL               | NULL              |
    | Marina Fernández  | NULL               | NULL              |
    | Andrés Ramírez    | NULL               | NULL              |
    +-------------------+--------------------+-------------------+
    ```

    

12. Calcula el número de productos diferentes que hay en cada uno de los  pedidos. 

    ```mysql
    SELECT id_pedido_producto, COUNT(DISTINCT id_producto_pedido) AS 'Número de productos'
    FROM detalle_pedido
    GROUP BY id_pedido_producto;
    +--------------------+---------------------+
    | id_pedido_producto | Número de productos |
    +--------------------+---------------------+
    |                  1 |                   4 |
    |                  2 |                   4 |
    |                  3 |                   3 |
    |                  4 |                   3 |
    |                  5 |                   3 |
    |                  6 |                   3 |
    |                  7 |                   2 |
    |                  8 |                   3 |
    |                  9 |                   1 |
    |                 10 |                   2 |
    +--------------------+---------------------+
    ```

    

13. Calcula la suma de la cantidad total de todos los productos que aparecen en  cada uno de los pedidos. 

    ```mysql
    SELECT ped.id_pedido, SUM(dp.cantidad) AS cantidad_total_productos
    FROM pedido ped
    INNER JOIN detalle_pedido dp ON ped.id_pedido = dp.id_pedido_producto
    GROUP BY ped.id_pedido;
    +-----------+--------------------------+
    | id_pedido | cantidad_total_productos |
    +-----------+--------------------------+
    |         1 |                       13 |
    |         2 |                       11 |
    |         3 |                        6 |
    |         4 |                        3 |
    |         5 |                        3 |
    |         6 |                        4 |
    |         7 |                        8 |
    |         8 |                        5 |
    |         9 |                        2 |
    |        10 |                        6 |
    +-----------+--------------------------+
    ```

    

14. Devuelve un listado de los 20 productos más vendidos y el número total de  unidades que se han vendido de cada uno. El listado deberá estar ordenado  por el número total de unidades vendidas.

    ```mysql
    SELECT p.nombre AS nombre_producto, SUM(dp.cantidad) AS total_unidades_vendidas
    FROM detalle_pedido AS dp
    JOIN producto AS p ON dp.id_producto_pedido = p.id_producto
    GROUP BY p.nombre
    ORDER BY total_unidades_vendidas DESC
    LIMIT 20;
    +-----------------------------------+-------------------------+
    | nombre_producto                   | total_unidades_vendidas |
    +-----------------------------------+-------------------------+
    | Uvas Thompson                     |                      15 |
    | Plátanos Canarios                 |                       9 |
    | Lirio Blanco                      |                       5 |
    | Planta de Lavanda                 |                       4 |
    | Hierba de Limón                   |                       4 |
    | Manzanas Gala                     |                       3 |
    | Begonia Escarlata                 |                       3 |
    | Planta de Menta                   |                       2 |
    | Cortadora de Césped               |                       2 |
    | Set de Riego Automático           |                       2 |
    | Vela de Lavanda                   |                       2 |
    | Aceite Esencial de Eucalipto      |                       2 |
    | Naranjas Valencia                 |                       2 |
    | Girasol Gigante                   |                       2 |
    | Orquídea Phalaenopsis             |                       2 |
    | Set de Herramientas de Jardinería |                       1 |
    | Incienso de Sándalo               |                       1 |
    +-----------------------------------+-------------------------+
    ```

    

15. La facturación que ha tenido la empresa en toda la historia, indicando la  base imponible, el IVA y el total facturado. La base imponible se calcula  sumando el coste del producto por el número de unidades vendidas de la  tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la  suma de los dos campos anteriores.

    ```mysql
    SELECT SUM(base_imponible) AS total_base_imponible, SUM(iva) AS 'IVA Total', SUM(total_facturado) AS 'Total'
    FROM (
        SELECT SUM(dp.cantidad * p.precio_venta) AS base_imponible, SUM(dp.cantidad * p.precio_venta) * 0.21 AS iva, SUM(dp.cantidad * p.precio_venta) * 1.21 AS total_facturado
        FROM detalle_pedido dp
        JOIN producto p ON dp.id_producto_pedido = p.id_producto
        GROUP BY dp.id_pedido_producto
    ) AS facturacion;
    +----------------------+--------------------+----------+
    | total_base_imponible | IVA Total          | Total    |
    +----------------------+--------------------+----------+
    |               600.39 | 126.08190000000002 | 726.4719 |
    +----------------------+--------------------+----------+
    ```

    

16. La misma información que en la pregunta anterior, pero agrupada por  código de producto.

    ```mysql
    SELECT dp.id_producto_pedido AS 'id_producto', ROUND(SUM(dp.cantidad * p.precio_venta), 2) AS base_imponible, ROUND(SUM(dp.cantidad * p.precio_venta) * 0.21, 2) AS iva, ROUND(SUM(dp.cantidad * p.precio_venta) * 1.21, 2) AS 'Total'
    FROM detalle_pedido as dp
    JOIN producto as p ON dp.id_producto_pedido = p.id_producto
    GROUP BY dp.id_producto_pedido;
    +-------------+----------------+------+--------+
    | id_producto | base_imponible | iva  | Total  |
    +-------------+----------------+------+--------+
    |           1 |          23.96 | 5.03 |  28.99 |
    |           2 |           8.98 | 1.89 |  10.87 |
    |           3 |          15.96 | 3.35 |  19.31 |
    |           4 |          39.99 |  8.4 |  48.39 |
    |           5 |         299.98 |   63 | 362.98 |
    |           6 |          59.98 | 12.6 |  72.58 |
    |           7 |           2.99 | 0.63 |   3.62 |
    |           8 |           5.98 | 1.26 |   7.24 |
    |           9 |          19.98 |  4.2 |  24.18 |
    |          10 |           2.97 | 0.62 |   3.59 |
    |          11 |           2.98 | 0.63 |   3.61 |
    |          12 |           8.01 | 1.68 |   9.69 |
    |          13 |          44.85 | 9.42 |  54.27 |
    |          16 |          22.45 | 4.71 |  27.16 |
    |          17 |          11.37 | 2.39 |  13.76 |
    |          18 |          11.98 | 2.52 |   14.5 |
    |          19 |          17.98 | 3.78 |  21.76 |
    +-------------+----------------+------+--------+
    ```

    

17. La misma información que en la pregunta anterior, pero agrupada por  código de producto filtrada por los códigos que empiecen por OR. 

    ```mysql
    SELECT 
        dp.id_producto_pedido AS codigo_producto,
        ROUND(SUM(dp.cantidad * p.precio_venta), 2) AS base_imponible,
        ROUND(SUM(dp.cantidad * p.precio_venta) * 0.21, 2) AS iva,
        ROUND(SUM(dp.cantidad * p.precio_venta) * 1.21, 2) AS total_facturado
    FROM detalle_pedido dp
    JOIN producto p ON dp.id_producto_pedido = p.id_producto
    WHERE p.id_producto LIKE 'OR%'
    GROUP BY dp.id_producto_pedido;
    
    En este caso la respuesta es vacía, ya que los productos tienen un ID autoincremental y carecen de 'OR' (no se especificó dicho requisito al inicio del taller)
    ```

    

18. Lista las ventas totales de los productos que hayan facturado más de 3000  euros. Se mostrará el nombre, unidades vendidas, total facturado y total  facturado con impuestos (21% IVA). 

    ```mysql
    SELECT p.nombre AS nombre_producto, SUM(dp.cantidad) AS unidades_vendidas, ROUND(SUM(dp.cantidad * p.precio_venta), 2) AS total_facturado_sin_iva, ROUND(SUM(dp.cantidad * p.precio_venta) * 1.21, 2) total_facturado_con_iva
    FROM detalle_pedido dp
    JOIN producto p ON dp.id_producto_pedido = p.id_producto
    GROUP BY p.nombre
    HAVING total_facturado_con_iva > 3000;
    
    En este caso el resultado es vacío porque la cantidad de precios no supera el monto de 3000
    ```

    

19. Muestre la suma total de todos los pagos que se realizaron para cada uno  de los años que aparecen en la tabla pagos

    ```mysql
    SELECT YEAR(fecha_pago) AS 'Año', SUM(total) AS 'Total'
    FROM pago
    GROUP BY YEAR(fecha_pago);
    +------+-------+
    | Año  | Total |
    +------+-------+
    | 2024 |  1080 |
    | 2008 |   350 |
    | 2009 |   300 |
    +------+-------+
    ```

    

### Consultas variadas

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos  pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no  han realizado ningún pedido.

   ```mysql
   SELECT c.nombre_cliente AS 'Nombre cliente', COUNT(p.id_pedido) AS 'Número de pedidos'
   FROM cliente AS cl
   LEFT JOIN pedido AS p ON cl.id_cliente = p.codigo_client_pedido
   GROUP BY cl.nombre_cliente;
   +-------------------+------------------+
   | Nombre Cliente    |Número de pedidos |
   +-------------------+------------------+
   | Roberto Martínez  |                4 |
   | Alejandra López   |                2 |
   | Fernando Sánchez  |                4 |
   | Isabel Gutiérrez  |                0 |
   | Gabriel Rodríguez |                0 |
   | Verónica Pérez    |                0 |
   | Marina Fernández  |                0 |
   | Andrés Ramírez    |                0 |
   +-------------------+------------------+
   ```

   

2. Devuelve un listado con los nombres de los clientes y el total pagado por  cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han  realizado ningún pago.

   ```mysql
   SELECT c.nombre_cliente AS 'Nombre Cliente', COALESCE(SUM(pa.total), 0) AS 'Total'
   FROM cliente AS c
   LEFT JOIN pago AS pa ON c.id_cliente = pa.codigo_cliente_pa
   GROUP BY c.nombre_cliente;
   +-------------------+-------+
   | Nombre Cliente    | Total |
   +-------------------+-------+
   | Roberto Martínez  |   550 |
   | Alejandra López   |   400 |
   | Fernando Sánchez  |   780 |
   | Isabel Gutiérrez  |     0 |
   | Gabriel Rodríguez |     0 |
   | Verónica Pérez    |     0 |
   | Marina Fernández  |     0 |
   | Andrés Ramírez    |     0 |
   +-------------------+-------+
   ```

   

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008  ordenados alfabéticamente de menor a mayor.

   ```mysql
   SELECT c.nombre_cliente
   FROM cliente c
   JOIN pedido p ON c.id_cliente = p.codigo_client_pedido
   WHERE YEAR(p.fecha_pedido) = 2008
   ORDER BY c.nombre_cliente ASC;
   
   ```

   

4. Devuelve el nombre del cliente, el nombre y primer apellido de su  representante de ventas y el número de teléfono de la oficina del  representante de ventas, de aquellos clientes que no hayan realizado ningún  pago.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado, ' ', e.apellido1) AS nombre_representante, t.numero AS telefono_oficina_representante
   FROM cliente c
   JOIN empleado e ON c.codigo_empleado_rep_ventas = e.id_empleado
   JOIN telefono t ON e.id_empleado = t.codigo_oficina_te
   LEFT JOIN pago p ON c.id_cliente = p.codigo_cliente_pa
   WHERE p.id_pago IS NULL;
   
   El resultado es vacío porque los clientes vinculados a representates de ventas realizaron pedidos y pagaron
   ```

   

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el  nombre y primer apellido de su representante de ventas y la ciudad donde  está su oficina.

   ```mysql
   SELECT c.nombre_cliente, CONCAT(e.nombre_empleado, ' ', e.apellido1) AS Representante, ci.nombre AS ciudad
   FROM cliente AS c
   JOIN empleado AS e ON c.codigo_empleado_rep_ventas = e.id_empleado
   JOIN oficina AS o ON e.codigo_oficina = o.id_oficina
   JOIN direccion AS d ON o.codigo_direccion_o = d.id_direccion
   JOIN ciudad AS ci ON d.codigo_ciudad_d = ci.id_ciudad;
   +-------------------+---------------+-------------+
   | nombre_cliente    | Representante | ciudad      |
   +-------------------+---------------+-------------+
   | Roberto Martínez  | Pedro Romero  | Madrid      |
   | Alejandra López   | Ana Martínez  | Madrid      |
   | Fernando Sánchez  | Ana Martínez  | Madrid      |
   | Gabriel Rodríguez | Sofía Pérez   | Fuenlabrada |
   | Verónica Pérez    | Sofía Pérez   | Fuenlabrada |
   | Isabel Gutiérrez  | Andrea García | Fuenlabrada |
   +-------------------+---------------+-------------+
   ```

   

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos  empleados que no sean representante de ventas de ningún cliente.

   ```mysql
   SELECT e.nombre_empleado, e.apellido1, e.apellido2, c.nombre_cargo AS Puesto, t.numero AS 'Teléfono'
   FROM empleado e
   JOIN cargo c ON e.codigo_cargo = c.id_cargo
   JOIN telefono t ON e.id_empleado = t.codigo_oficina_te
   LEFT JOIN cliente cl ON e.id_empleado = cl.codigo_empleado_rep_ventas
   WHERE cl.codigo_empleado_rep_ventas IS NULL;
   +-----------------+-----------+-----------+------------------+------------+
   | nombre_empleado | apellido1 | apellido2 | Puesto           | Teléfono   |
   +-----------------+-----------+-----------+------------------+------------+
   | María           | García    | López     | Director General | 3123456789 |
   | María           | García    | López     | Director General | 5712345678 |
   | Juan            | Pérez     | Gómez     | Director Oficina | 4123456789 |
   +-----------------+-----------+-----------+------------------+------------+
   ```

   

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el  número de empleados que tiene

   ```mysql
   SELECT ci.nombre AS Ciudad, COUNT(e.id_empleado) AS 'Empleados'
   FROM empleado AS e
   JOIN oficina AS o ON e.codigo_oficina = o.id_oficina
   JOIN direccion AS d ON o.codigo_direccion_o = d.id_direccion
   JOIN ciudad AS ci ON d.codigo_ciudad_d = ci.id_ciudad
   GROUP BY ci.nombre;
   +-------------+-----------+
   | Ciudad      | Empleados |
   +-------------+-----------+
   | Madrid      |         4 |
   | Fuenlabrada |         4 |
   +-------------+-----------+
   ```

   





















