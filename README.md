# **Taller de Funciones Definidas por el Usuario (UDFs) en MySQL** 🚀

### TABLES & INSERTS:

```SQL
CREATE TABLE empleados (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    salario DECIMAL(10,2)
);

INSERT INTO empleados (nombre, salario) VALUES
('Juan Pérez', 1500.00),
('Ana Gómez', 3000.00),
('Carlos Ruiz', 6000.00);

CREATE TABLE clientes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    fecha_nacimiento DATE
);

INSERT INTO clientes (nombre, fecha_nacimiento) VALUES
('Luis Martínez', '1990-06-15'),
('María López', '1985-09-20'),
('Pedro Gómez', '2000-03-10');


CREATE TABLE contactos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    telefono VARCHAR(10)
);

INSERT INTO contactos (nombre, telefono) VALUES
('Luis Martínez', '3052020202'),
('María López', '3024646464'),
('Pedro Gómez', '3014248478');


CREATE TABLE productos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nombre VARCHAR(50),
    precio DECIMAL(10,2)
);

INSERT INTO productos (nombre, precio) VALUES
('audifonos', 30),
('computador', 500),
('celular', 150);
```



## **Caso 1: Cálculo de Bonificaciones de Empleados**

### **Escenario**:

La empresa **TechCorp** otorga bonificaciones a sus empleados según su salario base. La bonificación se calcula así:

- Si el salario es menor a 2,000 USD → Bonificación del **10%**.
- Si el salario está entre 2,000 y 5,000 USD → Bonificación del **7%**.
- Si el salario es mayor a 5,000 USD → Bonificación del **5%**.

**Tarea**:

1. Crea una función llamada `calcular_bonificacion` que reciba un `DECIMAL(10,2)` con el salario y devuelva la bonificación correspondiente.
2. Usa la función en un `SELECT` sobre la tabla `empleados`.

```SQL
DELIMITER //
CREATE FUNCTION calcular_bonificacion(salario DECIMAL(10,2)) RETURNS DECIMAL (10,2)
DETERMINISTIC
READS SQL DATA
BEGIN
	DECLARE bonificacion DECIMAL (10,2);
	IF salario < 2000 THEN
		SET bonificacion = salario * 0.10;
	ELSEIF salario >= 2000 AND salario <= 5000 THEN
		SET bonificacion = salario * 0.07;
	ELSE
		SET bonificacion = salario * 0.05;
	END IF;
	RETURN bonificacion;
END //

DELIMITER ;

SELECT nombre, calcular_bonificacion(salario) AS bonificacion
FROM empleados;

```

![](https://media.discordapp.net/attachments/1337463162940817490/1391826418207297556/image.png?ex=686d4edc&is=686bfd5c&hm=def46a4b7303f91323414912b857b29c68a48da156101407aa7d5f28591ff6ba&=&format=webp&quality=lossless)

## **Caso 2: Cálculo de Edad de Clientes**

### **Escenario**:

En la empresa **MarketShop**, se necesita calcular la edad de los clientes a partir de su fecha de nacimiento para determinar estrategias de marketing.

**Tarea**:

1. Crea una función llamada `calcular_edad` que reciba un `DATE` y devuelva la edad del cliente.
2. Usa la función en un `SELECT` sobre la tabla `clientes`.

```sql
DELIMITER //
CREATE FUNCTION calcular_edad(fecha_nacimiento DATE) RETURNS INT
DETERMINISTIC
BEGIN
	RETURN TIMESTAMPDIFF(YEAR, fecha_nacimiento, CURDATE());
END //

DELIMITER ;

SELECT nombre, calcular_edad(fecha_nacimiento) AS edad
FROM clientes;
```

![](https://media.discordapp.net/attachments/1337463162940817490/1391830067289657507/image.png?ex=686d5242&is=686c00c2&hm=7032f8e0ffdc45ddbac8de8787762fe716457a5b3878be8b511c9f22c20ca413&=&format=webp&quality=lossless)

## **Caso 3: Formatear Números de Teléfono**

### **Escenario**:

En **CallCenter Solutions**, los números de teléfono están almacenados sin formato y se necesita presentarlos en el formato `(XXX) XXX-XXXX`.

**Tarea**:

1. Crea una función llamada `formatear_telefono` que reciba un número de teléfono en formato `XXXXXXXXXX` y lo devuelva en formato `(XXX) XXX-XXXX`.
2. Usa la función en un `SELECT` sobre la tabla `contactos`.

```sql
DELIMITER //
CREATE FUNCTION formatear_telefono(telefono VARCHAR(10)) RETURNS VARCHAR(14)
DETERMINISTIC
BEGIN
	DECLARE primero VARCHAR(3);
	DECLARE segundo VARCHAR(3);
	DECLARE tercero VARCHAR(4);
	SET primero = SUBSTRING(telefono, 1, 3);
	SET segundo = SUBSTRING(telefono, 4, 3);
	SET tercero = SUBSTRING(telefono, 7, 4);
	RETURN CONCAT('(', primero, ')', ' ', segundo, '-', tercero);
END //

DELIMITER ;

SELECT nombre, formatear_telefono(telefono) AS telefono FROM contactos;
```

![](https://media.discordapp.net/attachments/1337463162940817490/1391836173458210867/image.png?ex=686d57f2&is=686c0672&hm=4472b66d0c28953331fc7f5621d324c4bf3ecb26c3559ec151a9c5908d94eb8d&=&format=webp&quality=lossless)

## **Caso 4: Clasificación de Productos por Precio**

### **Escenario**:

En la tienda **E-Shop**, los productos se categorizan en tres niveles según su precio:

- **Bajo**: Menos de 50 USD.
- **Medio**: Entre 50 y 200 USD.
- **Alto**: Más de 200 USD.

**Tarea**:

1. Crea una función llamada `clasificar_precio` que reciba un `DECIMAL(10,2)` y devuelva un `VARCHAR(10)` con la clasificación del producto (`Bajo`, `Medio`, `Alto`).
2. Usa la función en un `SELECT` sobre la tabla `productos`.

```sql
DELIMITER //
CREATE FUNCTION clasificar_precio(precio DECIMAL(10,2)) RETURNS VARCHAR(10)
DETERMINISTIC
READS SQL DATA
BEGIN
	DECLARE clasificacion VARCHAR(10);
	IF precio  < 50 THEN
		SET clasificacion = 'Bajo';
	ELSEIF precio >= 50 AND precio <= 200 THEN
		SET clasificacion = 'Medio';
	ELSE
		SET clasificacion = 'Alto';
	END IF;
	RETURN clasificacion;
END //

DELIMITER ;

SELECT nombre, clasificar_precio(precio) AS clasificacion
FROM productos;
```

![](https://media.discordapp.net/attachments/1337463162940817490/1391840694686978118/image.png?ex=686d5c28&is=686c0aa8&hm=39d6241104a97d21202292fd77dd3191f28dd50734cd9190d7b19383147aee6f&=&format=webp&quality=lossless)
