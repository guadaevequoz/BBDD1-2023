# Práctica 2

# Parte I

1. La frase verdadera es “el esquema tiene más de una clave candidata” ya que “idMapa” y “idSitioWeb” pueden ser consideradas claves candidatas ya que son identificadores únicos en sus tablas.
2. ~~Las CC correctas con `CC(a,c)` ya que contienen todos los atributos determinados por las DF df1 y df2.~~ ❌ Las CC correctas son `CC(a,f)` ya que no existe ninguna DF que determine f entonces tiene que ser parte de la CC.
3. La opción correcta es la b), ya que ALUMNO tiene dos claves candidatas: DNI y nroLegajo pero sólo tendrá una clave primaria.
4. Las CC serían: `#aplicacion, #categoria, #etiqueta y #desarrollador`.

   Analizando las DFs proporcionadas:

   1. `#aplicacion,#actualizacion -> nombre_aplicacion,descripcion`: No es mínima, está mal.
   2. `#aplicacion,#actualizacion -> descripcion_cambios`: Tiene sentido porque una actualización tiene cambios y cada aplicación define los suyos.
   3. `nombre_desarrollador -> #desarrollador`: No tomaría esta como válida a menos que se garantice que el nombre y apellido del desarrollador son únicos, pero como no puedo garantizarlo no lo tomo como válido.
   4. `#desarrollador -> nombre_apellido_desarrollador`: Es válida.
   5. `#aplicación -> #categoria`: Es válida ya que la categoría nunca cambiar y es única por aplicación.

   Agregaría alguna DFs de etiquetas.

5. Todas las DM son válidas ya que se encuentra en BCNF.

# Parte II

1. Busco dependencias funcionales:

   1. #plan → nombre_plan, texto_condiciones, precio

   1. email → nombre_usuario
   1. #contenido → titulo, sinopsis, duración
   1. #suscripcion → email, #plan
   1. #suscripcion, email_adicional → fecha_adicional
   1. email_adicional → nombre_adicional

   Busco claves candidatas:

   CC1: {#contenido, #suscripcion}

   Normalizo:

   Primera iteración:

   1. email_adicional no es superclave en el esquema suscripción, no cumple BCNF
   2. Separo para eliminar anomalias:
      - R1(X,Y) = S1(email_adicional, nombre_adicional)
      - R2(R-Y) = S2(SUSCRIPCION - nombre_adicional) =
        S2(#suscripcion, email, nombre_usuario, #plan, nombre_plan, texto_condiciones, precio, email_adicional, ~~nombre_adicional~~, #contenido,
        titulo, sinopsis, duracion, fecha_adicional)
   3. No perdí dependencias funcionales porque las dfs siguen valiendo en las particiones

   Segunda iteración:

   1. S1 cumple con BCNF porque email_adicional es superclave en S1.

      S2 no cumple con BCNF porque la DF2: email → nombre_usuario, email no es superclave en S2 asi que particiono S2

   2. Separo para eliminar anomalias:
      - **S3(email, nombre_usuario)**
      - S4(#suscripcion, email, #plan, nombre_plan, texto_condiciones, precio, email_adicional, #contenido, titulo, sinopsis, duracion, fecha_adicional)
   3. No perdí dependencias funcionales porque las dfs siguen valiendo para las particiones

   Tercer iteración:

   **S1(email_adicional, nombre_adicional) //cumple BCNF**
   ~~S2(#suscripcion, email, nombre_usuario, #plan, nombre_plan,
   texto_condiciones, precio, email_adicional, #contenido, titulo, sinopsis, duracion, fecha_adicional)~~ //no cumple BCNF
   S3(email, nombre_usuario)
   S4(#suscripcion, email, #plan, nombre_plan, texto_condiciones, precio, email_adicional, #contenido, titulo, sinopsis, duracion, fecha_adicional)

   1. S3 cumple con BCNF porque email es superclave en S3-

      S4 no cumple con BCNF porque en la DF4 #suscripcion no es superclave de S4, particiono

   2. Particiono:
      - S5(#contenido, titulo, sinopsis, duración)
      - S6(#suscripcion, email, #plan, nombre_plan, texto_condiciones, precio, email_adicional, #contenido, fecha_adicional)
   3. No se perdieron dependencias funcionales

   Cuarta iteración:

   **S1(email_adicional, nombre_adicional)**

   **S3(email, nombre_usuario)**

   S5(#contenido, titulo, sinopsis, duración)

   S6(#suscripcion, email, #plan, nombre_plan, texto_condiciones, precio, email_adicional, #contenido, fecha_adicional)

   1. S5 cumple, S6 no
   2. Particiono:
      - S7(#plan, nombre_plan, texto_condiciones, precio)
      - S8(#suscripcion, email, #plan, email_adicional, #contenido, fecha_adicional)
   3. No se pierde info ni dependencias funcionales

   Quinta iteración:

   **S1(email_adicional, nombre_adicional)**

   **S3(email, nombre_usuario)**

   S5(#contenido, titulo, sinopsis, duración)

   S7(#plan, nombre_plan, texto_condiciones, precio)

   S8(#suscripcion, email, #plan, email_adicional, #contenido, fecha_adicional)

   1. S7 cumple, S8 no cumple
   2. Particiono:
      - S9(#suscripcion, email, #plan)
      - S10(#suscripcion email_adicional, #contenido, fecha_adicional)
   3. No se pierde nada

   Sexta iteración:

   **S1(email_adicional, nombre_adicional)**

   **S3(email, nombre_usuario)**

   S5(#contenido, titulo, sinopsis, duración)

   S7(#plan, nombre_plan, texto_condiciones, precio)

   S9(#suscripcion, email, #plan)

   S10(#suscripcion email_adicional, #contenido, fecha_adicional)

   1. S9 cumple con BCNF, S10 no cumple
   2. Particiono:
      - S11(#suscripcion email_adicional, #contenido, fecha_adicional)
      - S12(#suscripcion email_adicional, #contenido)

   Todos los esquemas quedaron en BCNF y en la última particion quedó la clave candidata asi que no se sigue particionando.

   El resultado es:

   S1(email_adicional, nombre_adicional) //cumple BCNF
   S3(email, nombre_usuario) //cumple BCNF
   S5(#contenido, titulo, sinopsis, duración) //cumple BCNF
   S7(#plan, nombre_plan, texto_condiciones, precio) //cumple BCNF
   S9(#suscripcion, email, #plan) //cumple BCNF
   S11(#suscripcion, email_adicional, #contenido, fecha_adicional) //cumple BCNF
   S12(#suscripcion, email_adicional, #contenido) //cumple BCNF

   Podría seguir hasta hacer una particion con contenido?

2. ```
   MEDICION_AMBIENTAL(#medicion, #pozo, valor_medicion, #parametro, fecha_medicion,
   cuil_operario, #instrumento, nombre_parametro, valor_ref, descripcion_pozo,
   fecha_perforacion, nombre_parametro, apellido_operario, nombre_operario, fecha_nacimiento,
   marca_instrumento, modelo_instrumento, dominio_vehiculo, fecha_adquisicion
   ```

   Donde:

   ● Cada `medición` es realizada por `un operario` en `un pozo`, en una `fecha determinada`. En ella se miden `varios parámetros`, y para cada `uno se obtiene un valor`. Notar que _un mismo parámetro (#parametro) puede ser medido en diferentes mediciones_.
   Independientemente de las mediciones, todo `parámetro` tiene un `nombre` y valor de `referencia`, y el `#parametro` es único en el sistema.
   ● En cada medición se utilizan varios `instrumentos`, independientemente de los parámetros medidos. De cada instrumento se conoce la `marca` y `modelo`.
   ● De cada `operario` se conoce su cuit, nombre, apellido y fecha de nacimiento.

   ● La empresa cuenta con `vehículos`, y de cada uno se conoce la `fecha en la que fue adquirido.` El `dominio` (patente) de cada vehículo es único en el sistema.
   ● Un `pozo` tiene una `descripción` y una `fecha` de perforación. El identificador #pozo es único en el sistema.

   **Clave candidata:**

   CC1: `(#medicion, #parametro, cuil_operario, #instrumento, dominio_vehiculo, #pozo)`

   **Dependencias funcionales:**

   - df1: #medicion → cuil_operario, #pozo, fecha_medicion
   - df2: #parametro → nombre_parametro, valor_ref
   - df3: #instrumento → marca_instrumento, modelo_instrumento
   - df4: cuil_operario → apellido_operario, nombre_operario, fecha_nacimiento
   - df5: dominio_vehiculo → fecha_adquisicion
   - df6: #pozo → descripcion_pozo, fecha_perforacion
   - df7: #medicion, #parametro → valor_medicion

   **¿MEDICION_AMBIENTAL cumple con BCNF?**

   No, ya que hay una dependencia funcional `df1` donde `#medicion` no es superclave en MEDICION_AMBIENTAL, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df1`.

   - L1(**#medicion**, #pozo, fecha_medicion, cuil_operario)
   - L2(**#medicion, #pozo**, valor_medicion, **#parametro, cuil_operario, #instrumento**, nombre_parametro, valor_ref, descripcion_pozo, fecha_perforacion, nombre_parametro, apellido_operario, nombre_operario, fecha_nacimiento, marca_instrumento, modelo_instrumento, **dominio_vehiculo**, fecha_adquisicion)

   **¿Se perdió información?**

   No, ya que `L1 ∩ L2 = #medicion` es clave en L1.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df1 vale en L1
   - el resto valen en L2

   **¿L1 cumple con BCNF?**

   Sí, ya que vale df1 y `#medicion` es superclave del esquema L1.

   **¿L2 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df2` donde `#parametro` no es superclave en L2, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df2`.

   - L3(**#parametro**, nombre_parametro, valor_ref)
   - L4(**#medicion, #pozo**, valor_medicion, **#parametro, cuil_operario, #instrumento**, descripcion_pozo, fecha_perforacion, nombre_parametro, apellido_operario, nombre_operario, fecha_nacimiento, marca_instrumento, modelo_instrumento, **dominio_vehiculo**, fecha_adquisicion)

   **¿Se perdió información?**

   No, ya que `L3 ∩ L4 = #parametro` es clave en L.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df2 vale en L3
   - el resto valen en L4

   **¿L3 cumple con BCNF?**

   Sí, ya que vale df2 y `#parametro` es superclave del esquema L3.

   **¿L4 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df3` donde `#instrumento`no es superclave en L4, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df3`.

   - L5(**#instrumento**, marca_instrumento, modelo_instrumento)
   - L6(**#medicion, #pozo**, valor_medicion, **#parametro, cuil_operario, #instrumento**, descripcion_pozo, fecha_perforacion, nombre_parametro, apellido_operario, nombre_operario, fecha_nacimiento, **dominio_vehiculo**, fecha_adquisicion)

   **¿Se perdió información?**

   No, ya que `L5 ∩ L6 = #instrumento` es clave en L.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df3 vale en L5
   - el resto valen en L6

   **¿L5 cumple con BCNF?**

   Sí, ya que vale df3 y `#instrumento` es superclave del esquema L5.

   **¿L6 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df4` donde `cuil_operario` no es superclave en L6, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df4`.

   - L7(**cuil_operario,** apellido_operario, nombre_operario, fecha_nacimiento)
   - L8(**#medicion, #pozo**, valor_medicion, **#parametro, #instrumento**, descripcion_pozo, fecha_perforacion, nombre_parametro, **dominio_vehiculo**, fecha_adquisicion)

   **¿Se perdió información?**

   No, ya que `L7 ∩ L8 = cuil_operario` es clave en L.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df4 vale en L7
   - el resto valen en L8

   **¿L7 cumple con BCNF?**

   Sí, ya que vale df4 y `cuil_operario` es superclave del esquema L7.

   **¿L8 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df5` donde `dominio_vehiculo` no es superclave en L8, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df5`.

   - L9(**dominio_vehiculo**, fecha_adquisicion)
   - L10(**#medicion, #pozo**, valor_medicion, **#parametro, #instrumento**, descripcion_pozo, fecha_perforacion, nombre_parametro, **dominio_vehiculo**)

   **¿Se perdió información?**

   No, ya que `L9 ∩ L10 = dominio_vehiculo` es clave en L9.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df5 vale en L9
   - el resto valen en L10

   **¿L9 cumple con BCNF?**

   Sí, ya que vale df5 y `dominio_vehiculo` es superclave del esquema L9.

   **¿L10 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df7` donde `#medicion, #parametro` no es superclave en L10, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df7`.

   - L11(**#medicion, #parametro**, valor_medicion)
   - L12(**#medicion, #pozo**, **#parametro, #instrumento**, descripcion_pozo, fecha_perforacion, **dominio_vehiculo**)

   **¿Se perdió información?**

   No, ya que `L11 ∩ L12 = **#medicion, #parametro**` es clave en L10.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df7 vale en L11
   - el resto valen en L12

   **¿L11 cumple con BCNF?**

   Sí, ya que vale df7 y **`#medicion, #parametro`** es superclave del esquema L11

   **// CONSULTAR: #pozo deberia ser superclave también?**

   **Particiones finales:**

   - L1(**#medicion**, #pozo, fecha_medicion, cuil_operario)
   - L3(**#parametro**, nombre_parametro, valor_ref)
   - L5(**#instrumento**, marca_instrumento, modelo_instrumento)
   - L7(**cuil_operario,** apellido_operario, nombre_operario, fecha_nacimiento)
   - L9(**dominio_vehiculo**, fecha_adquisicion)
   - L11(**#medicion, #parametro**, valor_medicion)
   - L12(**#medicion, #pozo**, **#parametro, #instrumento**, descripcion_pozo, fecha_perforacion, **dominio_vehiculo**)

3. ```
   FESTIVALES (#festival, denominacion_festival, localidad, cuil_musico, nombre_musico, fecha_nacimiento, #banda, nombre_banda,
   estilo_musical, #tema, nombre_tema, duracion, instrumento, cuil_auspiciante, url_plataforma_entradas, #locacion)
   ```

   Donde:

   ● Para cada `festival` se conoce su `denominación` y la `localidad` en la que se realiza. Más de un festival podría tener la misma denominación.
   ● De cada `banda` se conoce su `nombre` y `estilo` musical.
   ● De cada `músico` se conoce su `nombre` y su `fecha` de nacimiento. Tenga en cuenta que varios músicos podrían tener el mismo nombre.
   ● Para cada `tema` interpretado por una `banda` en un festival se conoce su `nombre` y `duración`. Además, de cada músico que participó en el tema se sabe con qué instrumento lo hizo.
   ● Los `#tema` pueden repetirse para las distintas bandas.
   ● Un festival puede tener varios `auspiciantes`, y se vendieron entradas al mismo a través de `varias plataformas`.
   ● Se tiene además un registro de todas las posibles `locaciones` donde se pueden realizar los festivales.

   **Clave candidata:**

   CC1:(#festival, #banda, cuil_musico, #tema, #locacion, url_plataforma_entradas, cuil_auspiciante)

   **Dependencias funcionales:**

   - df1: #festival → denominacion_festival, localidad
   - df2: #banda → nombre_banda, estilo_musical
   - df3: cuil_musico → nombre_musico, fecha_nacimiento
   - df4: #tema → nombre_tema, duracion
   - df5: #tema, cuil_musico → instrumento
   - df6: #banda —>> #tema **//CONSULTAR**
   - df7: #festival, url_plataforma_entradas → url_plataforma_entradas
   - df8: #festival, cuil_auspiciante → cuil_auspiciante
   - df9: #festival, #locacion → #locacion

   **¿FESTIVALES cumple con BCNF?**

   No, ya que hay una dependencia funcional `df1` donde `#festival` no es superclave en FESTIVALES, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df1`.

   - L1(#festival, denominacion_festival, localidad)
   - L2(#festival, cuil_musico, nombre_musico, fecha_nacimiento, #banda, nombre_banda,
     estilo_musical, #tema, nombre_tema, duracion, instrumento, cuil_auspiciante, url_plataforma_entradas, #locacion)

   **¿Se perdió información?**

   No, ya que `L1 ∩ L2 = #festival`es clave en L1.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df1 vale en L1
   - el resto valen en L2

   **¿L1 cumple con BCNF?**

   Sí, ya que vale df1 y `#festival` es superclave del esquema L1.

   **¿L2 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df2` donde `#banda` no es superclave en L2, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df2`.

   - L3(#banda, nombre_banda, estilo_musical)
   - L4(#festival, #banda, cuil_musico, nombre_musico, fecha_nacimiento #tema, nombre_tema, duracion, instrumento, cuil_auspiciante, url_plataforma_entradas, #locacion)

   **¿Se perdió información?**

   No, ya que `L3 ∩ L4 = #banda`es clave en L3.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df2 vale en L3
   - el resto valen en L4

   **¿L3 cumple con BCNF?**

   Sí, ya que vale df2 y `#banda` es superclave del esquema L3.

   **¿L4 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df3` donde `cuil_musico` no es superclave en L4, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df3`.

   - L5(cuil_musico, nombre_musico, fecha_nacimiento )
   - L6(#festival, #tema, #banda, nombre_tema, duracion, instrumento, cuil_auspiciante, url_plataforma_entradas, #locacion)

   **¿Se perdió información?**

   No, ya que `L5 ∩ L6 = cuil_musico` es clave en L5.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df3 vale en L5
   - el resto valen en L6

   **¿L5 cumple con BCNF?**

   Sí, ya que vale df3 y `cuil_musico` es superclave del esquema L5.

   **¿L6 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df4` donde `#tema` no es superclave en L6, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df4`.

   - L7( #tema, nombre_tema, duracion)
   - L8(#festival, #tema, cuil_musico, #banda, instrumento, cuil_auspiciante, url_plataforma_entradas, #locacion)

   **¿Se perdió información?**

   No, ya que `L7 ∩ L8 = #tema`es clave en L7.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df4 vale en L7
   - el resto valen en L8

   **¿L7 cumple con BCNF?**

   Sí, ya que vale df4 y `#tema` es superclave del esquema L7.

   **¿L8 cumple con BCNF?**

   No, ya que hay una dependencia funcional `df5` donde `#tema, cuil_musico`no es superclave en L8, ni una dependencia funcional trivial. Por lo tanto particiono el esquema contemplando `df5`.

   - L9( #tema, cuil_musico, instrumento)
   - L10(#festival, #tema, cuil_musico, #banda, cuil_auspiciante, url_plataforma_entradas, #locacion)

   **¿Se perdió información?**

   No, ya que `L9 ∩ L10 = #tema, cuil_musico`es clave en L9.

   **¿Se perdieron dependencias funcionales?**

   No, ya que:

   - df5 vale en L9
   - el resto valen en L10

   **¿L9 cumple con BCNF?**

   Sí, ya que vale df5 y `#tema, cuil_musico`es superclave del esquema L9.

   **¿L10 cumple con BCNF?**

   Sí, ya que las dependencias funcionales restantes son triviales.

   **Particiones finales:**

   - L1(#festival, denominacion_festival, localidad)
   - L3(#banda, nombre_banda, estilo_musical)
   - L5(cuil_musico, nombre_musico, fecha_nacimiento)
   - L7( #tema, nombre_tema, duracion)
   - L9( #tema, cuil_musico, instrumento)
   - L10(#festival, #tema, cuil_musico, #banda, cuil_auspiciante, url_plataforma_entradas, #locacion)
