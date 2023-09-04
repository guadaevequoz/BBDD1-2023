# Práctica 1

1.  1. La indica la agregación de Cuadro y Museo que se conectan mediante la relación `expuesto`con la entidad Período y tienen como cardinalidad (1,N). Para que cada período sea exclusivo de cada cuadro de un museo entonces cambiaría la cardinalidad a (1,1).

       ![Untitled](img/tp1_1_a.png)

    2. Cambiaría ahora la cardinalidad que está sobre Periodo a (1,1). **_//SE PODRÍA SACAR LA AGREGACIÓN Y HACER LA RELACION EXPUESTO DIRECTO CON MUSEO?_**
    3. Hago una generalización de Museo y como no pueden haber atributos compuestos haría una entidad nueva llamada Datos Históricos.

       ![Untitled](img/tp1_1_c.png)

2.  1. Verdadero.
    2. Falso.
    3. Falso. Debe tenerlo.
    4. Falso. Si es necesario sí, aporta información extra.
3.  1. Falso. Sólo interesa saber si las personas Físicas tienen monotributo.
    2. Verdadero.
    3. Verdadero. Pero repite datos.
    4. Falso. Si pueden haber.
    5. No entendí
    6. Falso. Se puede saber el monto acumulado, no así el que queda ya que no es un dato.
    7. Falso. Tiene redundancia en Persona.
4.  1. Cuando quiero saber en qué momento se realizó la “relación” entre el vendedor y el local.
    2. Cuando se quiere ver al vendedor y al local como una sola entidad y que puede repetirse, sacandole individualidad. Además, puede tener varias fechas de ingreso.
    3. Se esta marcando el horario en el que el vendedor trabaja en ese local. La cardinalidad sería: (1,1) ya que al tener la agregación se considera como una entidad al vendedor-local y (1,N) porque puede trabajar en varios lugares con distintos horarios.
5.  1. Verdadero.
    2. Verdadero.
    3. Falso. La clave debería ser `idic`.
    4. Verdadero. Es una opción.
    5. Falso.
6.
