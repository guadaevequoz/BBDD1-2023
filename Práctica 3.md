# Práctica 3

- Selección σ
- Proyección π
- Producto Cartesiano X
- Producto Natural |X|
- Diferencia o Resta –
- Intersección ∩
- Unión ∪
- División %
- Renombre ρ

# Parte I

1.  1.  1. **A(a,b,c) U B(a,b,d)** → ❌ NO ES VÁLIDA, ya que no son de unión compatible. Debido a que no tienen los mismos atributos.
        2. **(A(a,b,c) |X| B(a,b) ) - C(a,b,c)** → ✅ ES VÁLIDA
        3. **(A(a,b,c) |X| B(a,d,e) ) ∩ D(a,b,c,d,e)**  → ✅ ES VÁLIDA
        4. **(A(a,b,c) X B (a,b,d) ) ∩ D(a,b,c,d)** → ❌ NO ES VÁLIDA, ya que el producto no elimina duplicados
    2.  Para la operación de resta es necesario que los esquemas involucrados sean compatibles, es decir, deben cumplir las siguientes condiciones:
        - Deben tener la misma cantidad de columnas
        - Las columnas deben ser del mismo dominio
2.  Es necesario que los atributos sean compatibles en las operaciones: resta y unión.
3.  La respuesta correcta es: `(#compra, cantidad)`. Ya que primero se toman solo los #producto de Producto y luego se hace la división con **COMPRA_PRODUCTO**, así que se listan todos los elementos de ese esquema sin el atributo **#producto.**
4.  1.  Esta consulta primero toma todos los vuelos realizados en el proximo año y los almacena en VUELOS_PROX_AÑO. Luego, se hace un natural obteniendo: todos los nuevos del próximo año, las reservas correspondientes y los pasajeros correspondientes. Así se obtienen todos los pasajeros junto con su vuelo y su asiento que tienen reservas para el próximo año, así que es correcto ✅

            2. La tabla VUELO no tiene el atributo ciudad_salida o ciudad_destino pero supongo que se refieren a aeropueto_salida y aeropueto_destino.

               - Se crea una tabla con todos los vuelos con salida de Buenos Aires y destino Cordoba
               - Se toman todas las reservas de agosto de 2023. Se hace un natural con la tabla creada antes. Se obtiene una tabla con todos los vuelos con origen Buenos Aires y destino Cordoba que se hicieron en Agosto de 2023.
               - Se quiere proyectar el atributo monto_total de la tabla generad en el punto anterior. Esto falla ya que no existe el atributo.

               No es correcto ❌


            3. - Se renombran los atributos de la tabla Pasajero con los atributos: `#p, nom, d, pun`
               - Se obtiene una tabla que mezcla los valores de la tabla Pasajero con la anterior
               - Se obtienen solo los pasajeros que tengan un puntaje alto?
               - Se obtienen todos los pasajeros que tengan puntaje bajo
               - Se obtienen todos los pasajeros que tengan puntajes bajos de nuevo

               Esto no es correcto ya que no se evalúa si tienen vuelos pendientes. ❌

            4. - Obtengo todos los NUMEROS de vuelos que salgan del aeropuerto “Ministro Pistarini”
               - Obtengo todos los NÚMEROS de pasajeros que hayan hecho una reserva
               - Obtengo nombre y dni de los pasajeros que hayan hecho una reserva de vuelos que salgan del aeropuerto “Ministro Pistarini”

               Cumple ✅

               Arreglo: no cumple pq relaciona reserva y quiere sacar el nmero de pasajero de ahí pero no esta en esa tabla xD



            5. La consulta en sí no está mal, sin embargo el id de pasajero no se encuentra en la tabla RESERVAS asi que hay una inconsistencia como el punto d.

# Parte II

5.  (es el 6 de la práctica)

    DUEÑO(id_dueño, nombre, teléfono, dirección, dni) CHOFER(id_chofer, nombre,
    teléfono, dirección, fecha_licencia_desde, fecha_licencia_hasta, dni)
    AUTO(patente, id_dueño, id_chofer, marca, modelo, año) VIAJE(patente,
    hora_desde, hora_hasta, origen, destino, tarifa, metraje)

    1.  dueño_no_chofer_dni ← `(Π (dni) DUEÑO) - (Π (dni) CHOFER)`

        dueño_no_chofer ← `Π (dni, nombre, teléfono) (dueño_no_chofer_dni |X| dueño)`

    2.  licencia_2024 ← `Π (id_chofer) (σ (fecha_licencia_hasta = 01/01/2024) CHOFER)`

        auto_chofer_2024 ← `Π (patente, id_chofer)(licencia_2024 |X| AUTO)`

6.  (7 de la práctica)

    ESTUDIANTE ( #legajo, nombreCompleto, nacionalidad, añoDeIngreso,
    códigoDeCarrera ) CARRERA ( códigoDeCarrera, nombre ) INSCRIPCIONAMATERIA (
    #legajo, códigoDeMateria ) MATERIA ( códigoDeMateria, nombre)

    1.  `Π (nombreCompleto) (σ (añoIngreso = 2019) ESTUDIANTE)`

    2.  estudiantes_argentinos ← `σ (nacionalidad = "Argentina") ESTUDIANTE`

        carrera_LI07 ← `σ (codigoDeCarrera = "LI07") ESTUDIANTE`

        estudiantes_argentinos_noLI07 ← `Π (nombreCompleto) (estudiantes_argentinos - carrera_LI07)`

    3.  `Π(#legajo)(INSCRIPCIONMATERIA % (Π (codigoDeMateria) MATERIA))`

7.  (8 de la práctica)

    LUGAR_TRABAJO (#empleado, #departamento) CURSO_EXIGIDO (#departamento,
    #curso) CURSO_REALIZADO (#empleado, #curso)

    1. `(CURSO_REALIZADO % (Π (#curso) CURSO_EXIGIDO))`
    2. `(CURSO_REALIZADO % (Π (#curso) CURSO_EXIGIDO % Π (#curso) LUGAR_TRABAJO))` //NO SÉ COMO HACERLO :// sé que esto esta mal entonces capaz debería hacer un producto antes no sé

8.  (9 de la práctica)

    FRECUENTA (bebedor, bar) SIRVE (bar, cerveza) GUSTA (bebedor, cerveza)

    1.  `Π (bar) (Π (cerveza) (σ (bebedor= "x") GUSTA |X| SIRVE)`

    2.  `Π (bebedor) (GUSTA |X| SIRVE |X| FRECUENTA)`
    3.  NO SÉ COMO HACERLO 😭😭😭
    4.  bares_gusta ← `Π (bebedor, bar) (GUSTA |X| SIRVE)`

        no_frecuenta ← `Π (bebedor) (bares_gusta - FRECUENTA)`

    5.  `GUSTA % (Π (cerveza) (σ (bar = "y") SIRVE))`
    6.  `GUSTA % (Π (cerveza) SIRVE)` //creo q ta mal :(
