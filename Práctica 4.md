# Consultas SQL

1.  1.  **Listar el dni, nombre y teléfono de todos los dueños que NO son choferes**

        ```sql
        SELECT dni, nombre, telefono
        FROM dueño
        WHERE dni NOT IN (SELECT dni FROM chofer)
        ```

        2.  **Listar la patente y el id_chofer de todos los autos a cuyos choferes les caduca la licencia el 01/01/2024**

        ```sql
        SELECT a.patente, .id_chofer
        FROM auto a INNER JOIN chofer c ON a.id_chofer = c.id_chofer
        WHERE c.fecha_licencia_hasta = "01/01/2024"
        ```

2.  1. **Obtener el nombre de los estudiantes que ingresaron en 2019.**

       ```sql
       SELECT nombreCompleto
       FROM estudiante
       WHERE añoIngreso = 2019
       ```

    2. **Obtener el nombre de los estudiantes con nacionalidad “Argentina” que NO estén en la carrera con código “LI07”**

       ```sql
       SELECT e.nombre
       FROM estudiante
       WHERE nacionalidad = 'Argentina' AND codigoDeCarrera <> 'LI07'
       ```

Que es más estructurado en SQL.

# Preparación BD

No sabía cómo hacerlo desde la terminal asi que lo hice desde el workbench. Cree las bases de datos y después importe todo desde: Serser → Import Data.

# Ejercicios

1. me da paja
2. ```sql
   #base normalizada
   SELECT dniCliente, nombreApellidoCliente
   FROM reparacion.cliente
   order by dniCliente;

   #base sin normalizar
   SELECT dniCliente, nombreApellidoCliente
   FROM reparacion_dn.reparacion
   order by dniCliente;
   ```

   La consulta a la base sin normalizar arroja 162252 resultados, mientras que la normalizada 20000. Es claro que es menos performante la sin normalizar ya que incluso tardo casi 1 segundo en devolver la respuesta y muchos datos están repetidos.

3. ```sql
   #base normalizada
   SELECT distinct(c.dniCliente), c.nombreApellidoCliente
   FROM reparacion.cliente c
   WHERE NOT EXISTS ( SELECT *
   					FROM reparacion.reparacion r
   					WHERE r.dniCliente = c.dniCliente
             AND r.tarjetaReparacion = c.tarjetaSecundaria)

   #base sin normalizar
   SELECT r.dniCliente, r.nombreApellidoCliente
   FROM reparacion_dn.reparacion r
   WHERE NOT EXISTS (SELECT *
   		FROM reparacion_dn.reparacion r2
   		WHERE r2.dniCliente = r.dniCliente
   		AND r2.tarjetaReparacion = r.tarjetaSecundaria
   );
   ```

4. ```sql
   #base normalizada
   CREATE VIEW sucursalesPorCliente AS
   SELECT c.dniCliente, s.nombre
   FROM reparacion.cliente c
   INNER JOIN reparacion.sucursal s
   ON c.ciudadCliente = s.ciudadSucursal;

   #base sin normalizar
   CREATE VIEW sucursalesPorCliente AS
   SELECT r.dniCliente, r.nombre
   FROM reparacion_dn.reparacion r
   WHERE r.ciudadCliente = r.ciudadSucursal;
   ```

5. 1. Con not exists:

      ```sql
      #base normalizada
      SELECT c.dniCliente
      FROM reparacion.cliente c
      WHERE NOT EXISTS (
      	SELECT s.codSucursal
          FROM reparacion.sucursal s
          WHERE s.ciudadSucursal = c.ciudadCliente
          AND s.codSucursal NOT IN (
      		SELECT r.codSucursal
              FROM reparacion.reparacion r
              WHERE r.dniCliente = c.dniCliente
          )
      )
      LIMIT 100;
      ```

      Sin not exists:

      ```sql
      SELECT dniCliente FROM cliente c
      WHERE (
          SELECT COUNT(codSucursal) FROM sucursal s
          WHERE c.ciudadCliente = s.ciudadSucursal)
      = (
          SELECT COUNT(DISTINCT s.codSucursal) FROM reparacion r
          INNER JOIN sucursal s ON r.codSucursal = s.codSucursal
          WHERE c.dniCliente = r.dniCliente
          AND c.ciudadCliente = s.ciudadSucursal
      )
      ORDER BY dniCliente
      LIMIT 100;
      ```

   2. me da paja

6. Lo siguiente me tira error porque la PK no puede ser null, así que le agrego el not null.

   ```sql
   CREATE TABLE `reparacion`.`reparacionesporcliente` (
     `idRC` INT(11) NULL AUTO_INCREMENT,
     `dniCliente` INT(11) NULL,
     `cantidadReparaciones` INT(11) NULL,
     `fechaultimaactualizacion` DATETIME NULL,
     `usuario` CHAR(16) NULL,
     PRIMARY KEY (`idRC`));
   ```

   Bien:

   ```sql
   CREATE TABLE `reparacion`.`reparacionesporcliente` (
     `idRC` INT(11) not NULL AUTO_INCREMENT,
     `dniCliente` INT(11) NULL,
     `cantidadReparaciones` INT(11) NULL,
     `fechaultimaactualizacion` DATETIME NULL,
     `usuario` CHAR(16) NULL,
     PRIMARY KEY (`idRC`));
   ```

7. ```sql
   USE reparacion

   DELIMITER //
   CREATE PROCEDURE punto7()
   BEGIN
   	DECLARE dni INT;
       DECLARE cantidadReparaciones INT;
       DECLARE done INT DEFAULT 0;

   	DECLARE cursor_clientes CURSOR FOR
       SELECT dniCliente, COUNT(*) AS cantReparaciones
       FROM reparacion
       GROUP BY dniCliente;

       DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

       OPEN cursor_clientes;

       loop_cursor: LOOP
   		FETCH NEXT FROM cursor_clientes INTO dni, cantidadReparaciones;

           IF done = 1 THEN
   			LEAVE loop_cursor;
   		END IF;

           INSERT INTO reparacionesporcliente (dniCliente, cantidadReparaciones, fechaultimaactualizacion, usuario)
           VALUES (dni, cantidadReparaciones, NOW(), CURRENT_USER());
       END LOOP;

       CLOSE cursor_clientes;
   END;

   //
   DELIMITER ;
   ```

8. ```sql
   use reparacion

   DELIMITER //

   CREATE TRIGGER punto8
   AFTER INSERT
   ON reparacion FOR EACH ROWpunto8
   BEGIN
   	UPDATE reparacionesporcliente
       SET cantidadReparaciones = (cantidadReparaciones + 1),
   		fechaultimaactualizacion = NOW(),
           usuario = CURRENT_USER()
       WHERE dniCliente = NEW.dniCliente;
   END //
   DELIMITER ;
   ```

9. ```sql
   USE reparacion

   DELIMITER //
   CREATE PROCEDURE punto9 (
   	IN dniCliente INT,
       IN codSucursal INT,
       IN fechaReparacion DATE,
       IN cantDiasReparacion INT,
       IN telefonoReparacion VARCHAR(20),
       IN pagoConTarjetaPrimaria BOOLEAN,
       IN empleadoReparacion VARCHAR(50),
       IN repuestoReparacion VARCHAR(50))
   BEGIN
   	DECLARE clienteDomicilio VARCHAR(100);
       DECLARE clienteCiudad VARCHAR(50);
       DECLARE clienteTarjetaPrimaria VARCHAR(16);
       DECLARE clienteTarjetaSecundaria VARCHAR(16);

       SELECT domicilioCliente, ciudadCliente, tarjetaPrimaria, tarjetaSecundaria
       INTO clienteDomicilio, clienteCiudad, clienteTarjetaPrimaria, clienteTarjetaSecundaria
       FROM CLIENTE
       WHERE dniCliente = dniCliente;

       START TRANSACTION;

       INSERT INTO reparacion (dniCliente, fechaInicioReparacion, codSucursal, cantDiasReparacion, telefonoReparacionCliente, direccionReparacionCliente, ciudadReparacionCliente, tarjetaReparacion)
   	VALUES (dniCliente, fechaReparacion, codSucursal, cantDiasReparacion, telefonoReparacion, clienteDomicilio, clienteCiudad,
   		CASE WHEN pagoConTarjetaPrimaria THEN clienteTarjetaPrimaria ELSE clienteTarjetaSecundaria END);

       INSERT INTO revisionreparacion (dniCliente, fechaInicioReparacion, empleadoReparacion)
   	VALUES (dniCliente, fechaReparacion, empleadoReparacion);

       INSERT INTO repuestoreparacion (dniCliente, fechaInicioReparacion, repuestoReparacion)
       VALUES (dniCliente, fechaReparacion, respuestoReparacion);

       COMMIT;
   END;

   //
   DELIMITER ;
   ```

10. ```sql
    CALL punto9_1(
        1009443,
        100,
        '2013-12-14 12:20:31',
        4,
        '4243-4255',
        TRUE,
        'Maidana',
        'bomba de combustible'
    );
    ```

    El resultado fue: `Error Code: 1172. Result consisted of more than one row`.

11. ```sql
    EXPLAIN select count(r.dniCliente) from reparacion r, cliente c, sucursal s, revisionreparacion rv where
    r.dnicliente=c.dnicliente and r.codsucursal=s.codsucursal and r.dnicliente=rv.dnicliente and
    r.fechainicioreparacion=rv.fechainicioreparacion and empleadoreparacion = 'Maidana' and
    s.m2<200 and s.ciudadsucursal='La Plata';
    ```

    ![Untitled](/img/tp4_11.png)

    1. Los atributos relevantes son: type (puede ser relevante para determinar si se usa el join eficiente), key (puede indicar si se usan los índices adecuados), rows (a menos cantidad de filas, mejor rendimiento) y extra.
    2. Las tablas ‘s’ y ‘r’ se están escaneando completamente (ALL). La tabla 'c' y la tabla 'rv' se unen a través de claves primarias y se utilizan índices (eq_ref), es decir que no se escanean completamente por lo tanto es más eficiente.
    3. Se podría agregar índices para las tablas ‘s’ y ‘r’, por ejemplo para el atributo `ciudadsucursal` o `m2`.
    4. Podemos ver que disminuyó la cantidad de rows pero no las de la tabla r, porque no hicimos index para esa tabla!!

       ```sql
       CREATE INDEX idx_m2 ON sucursal (m2);
       CREATE INDEX idx_ciudadSucursal ON sucursal (ciudadSucursal);
       ```

       ![Untitled](/img/tp4_11_4.png)

# índices

1.  ```sql
    SELECT dniCliente, nombreApellidoCliente
    FROM cliente
    WHERE nombreApellidoCliente LIKE 'D%';
    ```

    ```sql
    EXPLAIN SELECT dniCliente, nombreApellidoCliente
    FROM cliente
    WHERE nombreApellidoCliente LIKE 'D%';
    ```

    El resultado del explain indica:

    ![Untitled](/img/tp4_indices_1.png)

    Se realiza el escaneo completo en la tabla cliente, es decir, se examinan todas las filas de la tabla. Además, se utiliza la cláusula WHERE. El número estimado de filas a analizar es de 20,059, lo que sugiere que la consulta está escaneando todas las filas de la tabla.

    En este caso, la consulta no está aprovechando un índice para la condición de filtro, lo que podría ralentizar la consulta, especialmente si la tabla tiene un gran número de filas. Para optimizar la consulta, se podría considerar la creación de un índice en la columna "nombreApellidoCliente" para acelerar la búsqueda de clientes cuyos nombres comienzan con "D". Después de crear el índice, el plan de ejecución podría mejorar, y el número de filas a analizar podría reducirse significativamente.

2.  ```sql
    CREATE INDEX idx_nombreApellidoCliente ON CLIENTE (nombreApellidoCliente);
    ```

    ```sql
    EXPLAIN SELECT dniCliente, nombreApellidoCliente
    FROM cliente
    WHERE nombreApellidoCliente LIKE 'D%';
    ```

    ![Untitled](/img/tp4_indices_2.png)

    El ALL cambio a range, lo que indica que se está utilizando un rango de búsqueda en el índice. Esto es más eficiente que un escaneo completo. Key muestra el nombre del índice utilizado, que es "idx_nombreApellidoCliente". El número estimado de filas a analizar es 1,787, lo cual es mucho menor que en el escaneo completo. Extra muestra "Using where" y "Using index", lo que indica que se están utilizando tanto la condición WHERE como el índice.

    La creación del índice en la columna "nombreApellidoCliente" ha permitido que la consulta utilice un rango en el índice, lo que mejora el rendimiento al reducir drásticamente el número de filas a examinar. Esto es especialmente beneficioso cuando la tabla tiene un gran número de filas. En este caso, la consulta es mucho más eficiente gracias al uso del índice.

3.  En las bases de datos, existen varios tipos de índices, y la elección del tipo de índice adecuado depende de la naturaleza de los datos y las operaciones que se realizarán en la tabla. A continuación, se describen algunos de los tipos de índices más comunes y cuándo se utilizan:

    - **Índice de Búsqueda (B-Tree)**:
      - Tipo de índice más común.
      - Se utiliza para mejorar el rendimiento de búsquedas exactas y de rangos.
      - Adecuado para columnas con valores discretos como ID, nombres, fechas, etc.
    - **Índice de Texto Completo (Full-Text)**:
      - Se utiliza para búsquedas de texto completo en columnas de texto.
      - Útil para la búsqueda de palabras clave en documentos o texto desestructurado.
    - **Índice de Clúster (Clustered)**:
      - Determina el orden físico de las filas en la tabla.
      - Se utiliza para la clave primaria de la tabla.
      - Solo puede haber un índice de clúster por tabla.
    - **Índice No Agrupado (Non-Clustered)**:
      - Se utiliza para mejorar el rendimiento de búsquedas y consultas que no se basan en el orden físico de los datos.
      - Puede haber múltiples índices no agrupados en una tabla.
    - **Índice Espacial (Spatial)**:
      - Se utiliza en bases de datos geoespaciales para acelerar consultas espaciales, como la búsqueda de puntos en un mapa.
    - **Índice de Bitmap**:
      - Se utiliza en columnas con valores discretos o categóricos.
      - Útil para columnas con un número limitado de valores distintos.
    - **Índice de Hash**:
      - Se utiliza para buscar valores de manera eficiente en columnas con valores discretos.
      - Adecuado para consultas de igualdad.

    Los índices que se crean por defecto en las tablas pueden variar según el sistema de gestión de bases de datos (SGBD) que se utilice. Sin embargo, en la mayoría de los SGBD, los índices por defecto suelen incluir:

    - Índice primario: Se crea automáticamente para la clave primaria de la tabla para acelerar la búsqueda de registros por la clave primaria.
    - Índices únicos: Se crean automáticamente para las restricciones de unicidad en las columnas para garantizar que no haya duplicados.

    La creación de otros tipos de índices (como índices no agrupados) generalmente debe ser especificada por el usuario según las necesidades de consulta específicas. Cada SGBD puede tener sus propias reglas y comportamientos predeterminados en cuanto a la creación de índices.
