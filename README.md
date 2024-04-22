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

