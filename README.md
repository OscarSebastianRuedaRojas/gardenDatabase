# Consultas sobre una tabla

1. #### Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

```sql
SELECT of.id as id_oficina, c.nombre as ciudad_oficina
FROM oficina as of
INNER JOIN ciudad as c ON of.id_ciudad = c.id;

+------------+----------------+
| id_oficina | ciudad_oficina |
+------------+----------------+
|          1 | Barcelona      |
|          2 | París          |
|          3 | Múnich         |
+------------+----------------+
```

2. #### Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

```sql
SELECT of.nombre, c.nombre AS ciudad_oficina, tl_of.numero AS numero_oficina
FROM oficina as of
INNER JOIN ciudad as c ON of.id_ciudad = c.id
INNER JOIN region as r ON c.id_region = r.id
INNER JOIN pais as p ON r.id_pais = p.id
INNER JOIN telefono_oficina as tl_of ON tl_of.id_oficina = of.id
WHERE p.nombre = "España";

+-------------------+----------------+----------------+
| nombre            | ciudad_oficina | numero_oficina |
+-------------------+----------------+----------------+
| Oficina Barcelona | Barcelona      |          32767 |
+-------------------+----------------+----------------+
```

3. #### Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

```sql
SELECT nombre, apellido1, apellido2, email
FROM empleado
WHERE id_jefe = 7;
```

4. #### Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

```sql
SELECT p.nombre, em.nombre, em.apellido1, em.apellido2, em.email
FROM empleado as em
INNER JOIN puesto_empleado as p ON p.id = em.id_puesto_empleado
WHERE em.id_puesto_empleado = 1;

+---------+--------+-----------+-----------+---------------------------+
| nombre  | nombre | apellido1 | apellido2 | email                     |
+---------+--------+-----------+-----------+---------------------------+
| Gerente | Juan   | García    | Pérez     | juan.garcia@example.com   |
| Gerente | Pierre | Dubois    | NULL      | pierre.dubois@example.com |
| Gerente | Hans   | Müller    | NULL      | hans.muller@example.com   |
+---------+--------+-----------+-----------+---------------------------+
```

5. #### Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

```sql
SELECT em.nombre, em.apellido1, em.apellido2, p.nombre
FROM empleado as em
INNER JOIN puesto_empleado AS p ON p.id = em.id_puesto_empleado
WHERE em.id_puesto_empleado != 3;

+--------+-----------+-----------+---------+
| nombre | apellido1 | apellido2 | nombre  |
+--------+-----------+-----------+---------+
| Juan   | García    | Pérez     | Gerente |
| Pierre | Dubois    | NULL      | Gerente |
| Hans   | Müller    | NULL      | Gerente |
+--------+-----------+-----------+---------+
```

6. #### Devuelve un listado con el nombre de los todos los clientes españoles.

```sql
SELECT cl.nombre
FROM cliente as cl
INNER JOIN ciudad as c ON c.id = cl.id_ciudad
INNER JOIN region AS r ON r.id = c.id_region
INNER JOIN pais AS p ON p.id = r.id_pais
WHERE p.nombre = "España";

+------------+
| nombre     |
+------------+
| Juan Pérez |
+------------+
```

7. #### Devuelve un listado con los distintos estados por los que puede pasar un pedido.

```sql
SELECT id, nombre
FROM estado_pedido;

+----+------------+
| id | nombre     |
+----+------------+
|  1 | En proceso |
|  2 | Completado |
|  3 | Cancelado  |
+----+------------+
```

8. #### Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar
aquellos códigos de cliente que aparezcan repetidos.

```sql
SELECT DISTINCT fpc.id_cliente AS id_cliente
FROM pago_pedido AS pp
INNER JOIN forma_pago_cliente AS fpc ON pp.id_forma_pago_cliente = fpc.id
WHERE YEAR(pp.fecha_pago) = 2008;
```

9. #### Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a
tiempo.

```sql
SELECT p.id AS codigo_pedido, fpc.id_cliente AS codigo_cliente, p.fecha_esperada, p.fecha_entrega
FROM pedido as p
INNER JOIN pago_pedido AS pp ON p.id = pp.id_pedido
INNER JOIN forma_pago_cliente AS fpc ON pp.id_forma_pago_cliente = fpc.id
WHERE p.fecha_esperada > p.fecha_entrega;
```

10. #### Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al
menos dos días antes de la fecha esperada.

```sql
SELECT p.id AS codigo_pedido, fpc.id_cliente AS codigo_cliente, p.fecha_esperada, p.fecha_entrega
FROM pedido as p
INNER JOIN pago_pedido as pp ON p.id = pp.id_pedido
INNER JOIN forma_pago_cliente as fpc ON pp.id_forma_pago_cliente = fpc.id
WHERE p.fecha_entrega < ADDDATE(p.fecha_esperada, -2);
```

11. #### Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

```sql
SELECT id
FROM pedido
WHERE id_estado = 3 AND YEAR(fecha_pedido) = 2009;
```

12. #### Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.

```sql
SELECT id
FROM pedido
WHERE MONTH(fecha_entrega) = 01;
```

13. #### Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.

```sql
SELECT pp.fecha_pago, pp.total
FROM pago_pedido as pp
INNER JOIN forma_pago_cliente as fpc ON pp.id_forma_pago_cliente = fpc.id
INNER JOIN forma_pago as fp ON fpc.id_forma_pago = fp.id
WHERE fp.nombre = "Paypal" AND YEAR(pp.fecha_pago) = 2008
ORDER BY pp.total DESC;
```

14. #### Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago
repetidas.

```sql
SELECT DISTINCT nombre
FROM forma_pago;

+------------------------+
| nombre                 |
+------------------------+
| Paypal                 |
| Tarjeta de Crédito     |
| Transferencia Bancaria |
+------------------------+
```

15. #### Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado
deberá estar ordenado por su precio de venta, mostrando en primer lugar
los de mayor precio.

```sql
SELECT p.nombre, gp.descripcion_texto, p.precio_venta
FROM producto as p
INNER JOIN gama_producto as gp ON p.id_gama_producto = gp.id
WHERE gp.descripcion_texto = 'Ornamentales' AND p.cantidad_stock >100
ORDER BY p.precio_venta DESC;
```

16. #### Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.

```sql
SELECT cl.id, cl.nombre
FROM cliente as cl
INNER JOIN ciudad as c ON cl.id_ciudad = c.id
WHERE c.nombre = 'Madrid' AND cl.id_empleado_rep_ventas in (11,30);
```

### Consultas multitabla (Composición interna)

1. #### Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado
FROM cliente as cl
INNER JOIN empleado as em ON cl.id_empleado_rep_ventas = em.id;

+----------------+-----------------+--------------------+--------------------+
| nombre_cliente | nombre_empleado | apellido1_empleado | apellido2_empleado |
+----------------+-----------------+--------------------+--------------------+
| Juan Pérez     | Juan            | García             | Pérez              |
| María García   | María           | López              | Martínez           |
| Pedro Martínez | Pierre          | Dubois             | NULL               |
+----------------+-----------------+--------------------+--------------------+
```

2. #### Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado
FROM cliente AS cl
INNER JOIN empleado as em ON cl.id_empleado_rep_ventas = em.id
INNER JOIN forma_pago_cliente As fpc On fpc.id_cliente = cl.id
INNER JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id;

+----------------+-----------------+--------------------+--------------------+
| nombre_cliente | nombre_empleado | apellido1_empleado | apellido2_empleado |
+----------------+-----------------+--------------------+--------------------+
| Juan Pérez     | Juan            | García             | Pérez              |
| María García   | María           | López              | Martínez           |
| Pedro Martínez | Pierre          | Dubois             | NULL               |
+----------------+-----------------+--------------------+--------------------+
```

3. #### Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre AS nombre_empleado, em.apellido1 AS apellido1_empleado, em.apellido2 AS apellido2_empleado
FROM cliente AS cl
INNER JOIN empleado AS em ON cl.id_empleado_rep_ventas = em.id
INNER JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
LEFT JOIN pago_pedido AS pp ON fpc.id_forma_pago = pp.id_forma_pago_cliente
WHERE pp.id_forma_pago_cliente IS NULL;
```

4. #### Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el
representante.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado, c.nombre as ciudad_oficina_representante
FROM cliente AS cl
INNER JOIN empleado as em ON cl.id_empleado_rep_ventas = em.id
INNER JOIN forma_pago_cliente As fpc On fpc.id_cliente = cl.id
INNER JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
INNER JOIN oficina AS of ON em.id_oficina = of.id
INNER JOIN ciudad AS c ON of.id_ciudad = c.id;

+----------------+-----------------+--------------------+--------------------+------------------------------+
| nombre_cliente | nombre_empleado | apellido1_empleado | apellido2_empleado | ciudad_oficina_representante |
+----------------+-----------------+--------------------+--------------------+------------------------------+
| Juan Pérez     | Juan            | García             | Pérez              | Barcelona                    |
| María García   | María           | López              | Martínez           | Barcelona                    |
| Pedro Martínez | Pierre          | Dubois             | NULL               | París                        |
+----------------+-----------------+--------------------+--------------------+------------------------------+
```

5. #### Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el
representante.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre AS nombre_empleado, em.apellido1 AS apellido1_empleado, em.apellido2 AS apellido2_empleado
FROM cliente AS cl
INNER JOIN empleado AS em ON cl.id_empleado_rep_ventas = em.id
INNER JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
INNER JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
INNER JOIN oficina AS of ON em.id_oficina = of.id
INNER JOIN ciudad AS c ON of.id_ciudad = c.id;
WHERE pp.id_forma_pago_cliente IS NULL;

```

6. #### Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.

```sql
SELECT df.direccion AS direccion_oficina
FROM oficina AS of
INNER JOIN empleado as em ON em.id_oficina = of.id
INNER JOIN cliente as cl ON cl.id_empleado_rep_ventas = em.id
INNER JOIN ciudad as c ON cl.id_ciudad = c.id
INNER JOIn direccion_oficina as df ON df.id_oficina = of.id
WHERE c.nombre = 'Fuenlabrada';
```

7. #### Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado, c.nombre as ciudad_oficina_representante
FROM cliente AS cl
INNER JOIN empleado as em ON cl.id_empleado_rep_ventas = em.id
INNER JOIN oficina AS of ON em.id_oficina = of.id
INNER JOIN ciudad AS c ON of.id_ciudad = c.id;

+----------------+-----------------+--------------------+--------------------+------------------------------+
| nombre_cliente | nombre_empleado | apellido1_empleado | apellido2_empleado | ciudad_oficina_representante |
+----------------+-----------------+--------------------+--------------------+------------------------------+
| Juan Pérez     | Juan            | García             | Pérez              | Barcelona                    |
| María García   | María           | López              | Martínez           | Barcelona                    |
| Pedro Martínez | Pierre          | Dubois             | NULL               | París                        |
+----------------+-----------------+--------------------+--------------------+------------------------------+
```

8. #### Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

```sql
SELECT em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado, j.nombre as nombre_jefe, j.apellido1 as apellido1_jefe, j.apellido2 as apellido2_jefe
FROM empleado as em
INNER JOIN empleado as j ON em.id_jefe = j.id;

+-----------------+--------------------+--------------------+-------------+----------------+----------------+
| nombre_empleado | apellido1_empleado | apellido2_empleado | nombre_jefe | apellido1_jefe | apellido2_jefe |
+-----------------+--------------------+--------------------+-------------+----------------+----------------+
| Juan            | García             | Pérez              | Juan        | García         | Pérez          |
| María           | López              | Martínez           | Juan        | García         | Pérez          |
| Pierre          | Dubois             | NULL               | Juan        | García         | Pérez          |
| Sophie          | Leclerc            | NULL               | Pierre      | Dubois         | NULL           |
| Hans            | Müller             | NULL               | Juan        | García         | Pérez          |
| Anna            | Schmidt            | NULL               | Hans        | Müller         | NULL           |
+-----------------+--------------------+--------------------+-------------+----------------+----------------+
```

9. #### Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.

```sql
SELECT em.nombre as nombre_empleado, em.apellido1 as apellido1_empleado, em.apellido2 as apellido2_empleado, j.nombre as nombre_jefe, j2.nombre as jefe_del_jefe
FROM empleado as em
INNER JOIN empleado as j ON em.id_jefe = j.id
INNER JOIN empleado as j2 ON j.id_jefe = j2.id;

+-----------------+--------------------+--------------------+-------------+---------------+
| nombre_empleado | apellido1_empleado | apellido2_empleado | nombre_jefe | jefe_del_jefe |
+-----------------+--------------------+--------------------+-------------+---------------+
| Juan            | García             | Pérez              | Juan        | Juan          |
| María           | López              | Martínez           | Juan        | Juan          |
| Pierre          | Dubois             | NULL               | Juan        | Juan          |
| Sophie          | Leclerc            | NULL               | Pierre      | Juan          |
| Hans            | Müller             | NULL               | Juan        | Juan          |
| Anna            | Schmidt            | NULL               | Hans        | Juan          |
+-----------------+--------------------+--------------------+-------------+---------------+
```

10. #### Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

```sql
SELECT cl.nombre, p.fecha_esperada, p.fecha_entrega
FROM cliente AS cl
INNER JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
INNER JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
INNER JOIN pedido AS p ON pp.id_pedido = p.id
WHERE p.fecha_esperada-p.fecha_entrega;

+----------------+----------------+---------------+
| nombre         | fecha_esperada | fecha_entrega |
+----------------+----------------+---------------+
| Pedro Martínez | 2024-04-17     | 2024-04-18    |
+----------------+----------------+---------------+
```

11. #### Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

```sql
SELECT DISTINCT cl.nombre AS nombre_cliente, gp.descripcion_texto AS gama_producto
FROM cliente cl
INNER JOIN forma_pago_cliente fpc ON fpc.id_cliente = cl.id
INNER JOIN pago_pedido pp ON pp.id_forma_pago_cliente = fpc.id
INNER JOIN pedido pd ON pp.id_pedido = pd.id
INNER JOIN detalle_pedido dp ON pd.id = dp.id_pedido
INNER JOIN producto pr ON dp.id_producto = pr.id
INNER JOIN gama_producto gp ON pr.id_gama_producto = gp.id;

+----------------+------------------------+
| nombre_cliente | gama_producto          |
+----------------+------------------------+
| Juan Pérez     | Electrónica de Consumo |
| María García   | Informática            |
| Pedro Martínez | Electrodomésticos      |
+----------------+------------------------+
```

### Consultas multitabla (Composición externa)

1. #### Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre AS nombre_empleado, em.apellido1 AS apellido1_empleado, em.apellido2 AS apellido2_empleado
FROM cliente AS cl
LEFT JOIN empleado AS em ON cl.id_empleado_rep_ventas = em.id
LEFT JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
LEFT JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
WHERE pp.id_forma_pago_cliente IS NULL;
```

2. #### Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre AS nombre_empleado, em.apellido1 AS apellido1_empleado, em.apellido2 AS apellido2_empleado
FROM cliente AS cl
LEFT JOIN empleado AS em ON cl.id_empleado_rep_ventas = em.id
LEFT JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
LEFT JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
WHERE pp.id_pedido IS NULL;
```

3. #### Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

```sql
SELECT cl.nombre AS nombre_cliente, em.nombre AS nombre_empleado, em.apellido1 AS apellido1_empleado, em.apellido2 AS apellido2_empleado
FROM cliente AS cl
LEFT JOIN empleado AS em ON cl.id_empleado_rep_ventas = em.id
LEFT JOIN forma_pago_cliente AS fpc ON fpc.id_cliente = cl.id
LEFT JOIN pago_pedido AS pp ON pp.id_forma_pago_cliente = fpc.id
WHERE pp.id_pedido IS NULL AND pp.id_forma_pago_cliente IS NULL;
```

4. #### Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

```sql
SELECT em.nombre, em.apellido1, em.apellido2
FROM empleado as em
LEFT JOIN oficina as f ON em.id_oficina = f.id
WHERE f.id IS NULL;
```

5. #### Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

```sql
SELECT em.id, em.nombre, em.apellido1, em.apellido2
FROM empleado AS em
LEFT JOIN cliente AS cl ON em.id = cl.id_empleado_rep_ventas
WHERE cl.id_empleado_rep_ventas IS NULL;

+----+--------+-----------+-----------+
| id | nombre | apellido1 | apellido2 |
+----+--------+-----------+-----------+
|  4 | Sophie | Leclerc   | NULL      |
|  5 | Hans   | Müller    | NULL      |
|  6 | Anna   | Schmidt   | NULL      |
+----+--------+-----------+-----------+
```

6. #### Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

```sql
SELECT em.id, em.nombre, em.apellido1, em.apellido2, of.nombre
FROM empleado AS em
LEFT JOIN cliente AS cl ON em.id = cl.id_empleado_rep_ventas
LEFT JOIN oficina AS of ON of.id = em.id_oficina
WHERE cl.id_empleado_rep_ventas IS NULL;

+----+--------+-----------+-----------+----------------+
| id | nombre | apellido1 | apellido2 | nombre         |
+----+--------+-----------+-----------+----------------+
|  4 | Sophie | Leclerc   | NULL      | Oficina París  |
|  5 | Hans   | Müller    | NULL      | Oficina Múnich |
|  6 | Anna   | Schmidt   | NULL      | Oficina Múnich |
+----+--------+-----------+-----------+----------------+
```

7. #### Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.

```sql
SELECT em.id, em.nombre, em.apellido1, em.apellido2, of.nombre
FROM empleado AS em
LEFT JOIN cliente AS cl ON em.id = cl.id_empleado_rep_ventas
LEFT JOIN oficina AS of ON of.id = em.id_oficina
WHERE cl.id_empleado_rep_ventas IS NULL AND em.id_oficina IS NULL;
```

8. #### Devuelve un listado de los productos que nunca han aparecido en un pedido.

```sql
SELECT p.nombre
FROM producto AS p
LEFT JOIN detalle_pedido as dp ON p.id = dp.id_producto
WHERE dp.id_producto IS NULL;
```

9. #### Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del
producto.

```sql
SELECT p.nombre, gp.descripcion_texto, gp.imagen
FROM producto AS p
LEFT JOIN detalle_pedido AS dp ON p.id = dp.id_producto
LEFT JOIN gama_producto AS gp ON gp.id = p.id_gama_producto
WHERE dp.id_producto IS NULL;

```

10. #### Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado
la compra de algún producto de la gama Frutales.

```sql
SELECT DISTINCT ofi.id, ofi.nombre
FROM oficina AS ofi
LEFT JOIN empleado AS em ON ofi.id = em.id_oficina
WHERE em.id IN (
    SELECT DISTINCT cl.id_empleado_rep_ventas
    FROM cliente AS cl
    INNER JOIN forma_pago_cliente AS fpc ON cl.id = fpc.id_cliente
    INNER JOIN pago_pedido AS pp ON fpc.id = pp.id_forma_pago_cliente
    INNER JOIN pedido AS p ON pp.id_pedido = p.id
    INNER JOIN detalle_pedido AS dp ON p.id = dp.id_pedido
    INNER JOIN producto AS pro ON dp.id_producto = pro.id
    INNER JOIN gama_producto AS gp ON pro.id_gama_producto = gp.id
    WHERE gp.descripcion_texto = 'Frutales'
);

```

11. #### Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```sql
SELECT DISTINCT cl.id, cl.nombre
FROM cliente AS cl
INNER JOIN pedido AS p ON cl.id = p.id_cliente
LEFT JOIN pago_pedido AS pp ON p.id = pp.id_pedido
WHERE pp.id_pedido IS NULL;
```

12. #### Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

```sql
SELECT em.id, em.nombre, em.apellido1, em.apellido2, em.id_jefe, j.nombre AS nombre_jefe, j.apellido1 AS apellido1_jefe, j.apellido2 AS apellido2_jefe
FROM empleado AS em
LEFT JOIN cliente AS c ON em.id = c.id_empleado_rep_ventas
INNER JOIN empleado AS j ON em.id_jefe = j.id
WHERE c.id_empleado_rep_ventas IS NULL;

+----+--------+-----------+-----------+---------+-------------+----------------+----------------+
| id | nombre | apellido1 | apellido2 | id_jefe | nombre_jefe | apellido1_jefe | apellido2_jefe |
+----+--------+-----------+-----------+---------+-------------+----------------+----------------+
|  4 | Sophie | Leclerc   | NULL      |       3 | Pierre      | Dubois         | NULL           |
|  5 | Hans   | Müller    | NULL      |       1 | Juan        | García         | Pérez          |
|  6 | Anna   | Schmidt   | NULL      |       5 | Hans        | Müller         | NULL           |
+----+--------+-----------+-----------+---------+-------------+----------------+----------------+
```

### Consultas resumen

1. #### ¿Cuántos empleados hay en la compañía?

```sql
SELECT COUNT(nombre) as empleados_compañia
FROM empleado;

+--------------------+
| empleados_compañia |
+--------------------+
|                  6 |
+--------------------+
```

2. #### ¿Cuántos clientes tiene cada país?

```sql
SELECT p.nombre AS pais, COUNT(cl.id) AS total_clientes
FROM cliente AS cl
INNER JOIN ciudad AS c ON cl.id_ciudad = c.id
INNER JOIN region AS r ON c.id_region = r.id
INNER JOIN pais AS p ON r.id_pais = p.id
GROUP BY p.nombre;

+----------+----------------+
| pais     | total_clientes |
+----------+----------------+
| Alemania |              1 |
| España   |              1 |
| Francia  |              1 |
+----------+----------------+

```

3. #### ¿Cuál fue el pago medio en 2009?

```sql
SELECT AVG(total) AS pago_medio_2009
FROM pago_pedido
WHERE YEAR(fecha_pago) = 2009;

+-----------------+
| pago_medio_2009 |
+-----------------+
|            NULL |
+-----------------+
```

4. #### ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma descendente por el número de pedidos.

```sql
SELECT ep.nombre AS estado, COUNT(p.id) AS total_pedidos
FROM pedido as p
INNER JOIN estado_pedido AS ep ON ep.id = p.id_estado
GROUP BY ep.nombre;

+------------+---------------+
| estado     | total_pedidos |
+------------+---------------+
| Cancelado  |             1 |
| Completado |             1 |
| En proceso |             1 |
+------------+---------------+
```

5. #### Calcula el precio de venta del producto más caro y más barato en una misma consulta.

```sql
SELECT (SELECT MAX(precio_venta) FROM producto) AS precio_mas_caro, (SELECT MIN(precio_venta) FROM producto) AS precio_mas_barato;
    
+-----------------+-------------------+
| precio_mas_caro | precio_mas_barato |
+-----------------+-------------------+
|         1999.99 |           1099.99 |
+-----------------+-------------------+

```

6. #### Calcula el número de clientes que tiene la empresa.

```sql
SELECT COUNT(nombre) AS numero_clientes
FROM cliente;

+-----------------+
| numero_clientes |
+-----------------+
|               3 |
+-----------------+
```

7. #### ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?

```sql
SELECT c.nombre AS ciudad, COUNT(cl.id) AS numero_clientes
FROM cliente AS cl
INNER JOIN ciudad AS c On cl.id_ciudad = c.id
GROUP BY c.nombre;

+-----------+-----------------+
| ciudad    | numero_clientes |
+-----------+-----------------+
| Barcelona |               1 |
| Múnich    |               1 |
| París     |               1 |
+-----------+-----------------+
```

8. #### ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan por M?

```sql
SELECT c.nombre AS ciudad, COUNT(cl.id) AS numero_clientes
FROM cliente AS cl
INNER JOIN ciudad AS c ON cl.id_ciudad = c.id
WHERE c.nombre LIKE 'M%'
GROUP BY c.nombre;

+--------+-----------------+
| ciudad | numero_clientes |
+--------+-----------------+
| Múnich |               1 |
+--------+-----------------+
```

9. #### Devuelve el nombre de los representantes de ventas y el número de clientes al que atiende cada uno.

```sql
SELECT em.nombre as representante, COUNT(cl.id) as numero_clientes
FROM empleado AS em
INNER JOIN cliente as cl ON cl.id_empleado_rep_ventas = em.id
GROUP BY em.nombre;

+---------------+-----------------+
| representante | numero_clientes |
+---------------+-----------------+
| Juan          |               1 |
| María         |               1 |
| Pierre        |               1 |
+---------------+-----------------+
```

10. #### Calcula el número de clientes que no tiene asignado representante de ventas.

```sql
SELECT COUNT(cl.id) AS numero_clientes_sin_representante
FROM cliente AS cl
INNER JOIN empleado AS em ON em.id = cl.id_empleado_rep_ventas
WHERE em.id IS NULL;

+-----------------------------------+
| numero_clientes_sin_representante |
+-----------------------------------+
|                                 0 |
+-----------------------------------+
```

11. #### Calcula la fecha del primer y último pago realizado por cada uno de los clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.

```sql
SELECT cl.nombre AS nombre_cliente, MIN(pp.fecha_pago) AS primer_pago, MAX(pp.fecha_pago) AS ultimo_pago
FROM cliente AS cl
INNER JOIN forma_pago_cliente AS fpc ON cl.id = fpc.id_cliente
INNER JOIN pago_pedido AS pp ON fpc.id = pp.id_forma_pago_cliente
GROUP BY cl.id;

+----------------+-------------+-------------+
| nombre_cliente | primer_pago | ultimo_pago |
+----------------+-------------+-------------+
| Juan Pérez     | 2024-04-10  | 2024-04-10  |
| María García   | 2024-04-11  | 2024-04-11  |
| Pedro Martínez | 2024-04-12  | 2024-04-12  |
+----------------+-------------+-------------+
```

12. #### Calcula el número de productos diferentes que hay en cada uno de los pedidos.

```sql
SELECT id_pedido,  COUNT(DISTINCT id_producto) AS num_productos_diferentes
FROM detalle_pedido
GROUP BY id_pedido;

+-----------+--------------------------+
| id_pedido | num_productos_diferentes |
+-----------+--------------------------+
|         4 |                        1 |
|         5 |                        1 |
|         6 |                        1 |
+-----------+--------------------------+
```

13. #### Calcula la suma de la cantidad total de todos los productos que aparecen en cada uno de los pedidos.

```sql
SELECT id_pedido, SUM(cantidad) AS cantidad_total
FROM detalle_pedido
GROUP BY id_pedido;

+-----------+----------------+
| id_pedido | cantidad_total |
+-----------+----------------+
|         4 |              2 |
|         5 |              1 |
|         6 |              3 |
+-----------+----------------+
```

14. #### Devuelve un listado de los 20 productos más vendidos y el número total de unidades que se han vendido de cada uno. El listado deberá estar ordenado
por el número total de unidades vendidas.

```sql
SELECT p.nombre AS nombre_producto, SUM(dp.cantidad) AS total_unidades_vendidas
FROM detalle_pedido AS dp
INNER JOIN producto AS p ON dp.id_producto = p.id
GROUP BY p.nombre
ORDER BY total_unidades_vendidas DESC
LIMIT 20;

+-------------------+-------------------------+
| nombre_producto   | total_unidades_vendidas |
+-------------------+-------------------------+
| Samsung QLED Q90R |                       3 |
| iPhone 13 Pro     |                       2 |
| MacBook Pro 2021  |                       1 |
+-------------------+-------------------------+
```

15. #### La facturación que ha tenido la empresa en toda la historia, indicando la base imponible, el IVA y el total facturado. La base imponible se calcula
sumando el coste del producto por el número de unidades vendidas de la
tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la
suma de los dos campos anteriores.

```sql
SELECT SUM(dp.cantidad * p.precio_venta) AS base_imponible, SUM(dp.cantidad * p.precio_venta) * 0.21 AS iva, SUM(dp.cantidad * p.precio_venta) + (SUM(dp.cantidad * p.precio_venta) * 0.21) AS total_facturado
FROM detalle_pedido AS dp
INNER JOIN producto AS p ON dp.id_producto = p.id;

+----------------+-----------+-----------------+
| base_imponible | iva       | total_facturado |
+----------------+-----------+-----------------+
|        9999.94 | 2099.9874 |      12099.9274 |
+----------------+-----------+-----------------+
```

16. #### La misma información que en la pregunta anterior, pero agrupada por código de producto.

```sql
SELECT dp.id_producto, SUM(dp.cantidad * p.precio_venta) AS base_imponible, SUM(dp.cantidad * p.precio_venta) * 0.21 AS iva, SUM(dp.cantidad * p.precio_venta) + (SUM(dp.cantidad * p.precio_venta) * 0.21) AS total_facturado
FROM detalle_pedido AS dp
INNER JOIN producto AS p ON dp.id_producto = p.id
GROUP BY dp.id_producto;

+-------------+----------------+-----------+-----------------+
| id_producto | base_imponible | iva       | total_facturado |
+-------------+----------------+-----------+-----------------+
|           4 |        2199.98 |  461.9958 |       2661.9758 |
|           5 |        1799.99 |  377.9979 |       2177.9879 |
|           6 |        5999.97 | 1259.9937 |       7259.9637 |
+-------------+----------------+-----------+-----------------+
```

17. #### La misma información que en la pregunta anterior, pero agrupada por código de producto filtrada por los códigos que empiecen por OR.

```sql
SELECT dp.id_producto, SUM(dp.cantidad * p.precio_venta) AS base_imponible, SUM(dp.cantidad * p.precio_venta) * 0.21 AS iva, SUM(dp.cantidad * p.precio_venta) + (SUM(dp.cantidad * p.precio_venta) * 0.21) AS total_facturado
FROM detalle_pedido AS dp
INNER JOIN producto AS p ON dp.id_producto = p.id
WHERE p.id LIKE 'OR%'
GROUP BY dp.id_producto;
```

18. #### Lista las ventas totales de los productos que hayan facturado más de 3000 euros. Se mostrará el nombre, unidades vendidas, total facturado y total
facturado con impuestos (21% IVA).

```sql
SELECT p.nombre AS nombre_producto, SUM(dp.cantidad) AS unidades_vendidas, SUM(dp.cantidad * p.precio_venta) AS total_facturado_sin_iva, SUM(dp.cantidad * p.precio_venta) * 0.21 AS iva, SUM(dp.cantidad * p.precio_venta) + (SUM(dp.cantidad * p.precio_venta) * 0.21) AS total_facturado_con_iva
FROM detalle_pedido AS dp
INNER JOIN producto AS p ON dp.id_producto = p.id
GROUP BY dp.id_producto
HAVING total_facturado_sin_iva > 3000;

+-------------------+-------------------+-------------------------+-----------+-------------------------+
| nombre_producto   | unidades_vendidas | total_facturado_sin_iva | iva       | total_facturado_con_iva |
+-------------------+-------------------+-------------------------+-----------+-------------------------+
| Samsung QLED Q90R |                 3 |                 5999.97 | 1259.9937 |               7259.9637 |
+-------------------+-------------------+-------------------------+-----------+-------------------------+
```

19. #### Muestre la suma total de todos los pagos que se realizaron para cada uno de los años que aparecen en la tabla pagos.

```sql
SELECT YEAR(fecha_pago) AS año, SUM(total) AS total_pagos
FROM pago_pedido
GROUP BY YEAR(fecha_pago);

+------+-------------+
| año  | total_pagos |
+------+-------------+
| 2024 |       10000 |
+------+-------------+
```

