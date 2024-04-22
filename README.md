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

