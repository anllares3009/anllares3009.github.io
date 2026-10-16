# PRÁCTICAS ROBÓTICA MÓVIL <br> Illán García Olivares (i.garciaol.2024@alumnos.urjc.es) <br> <br>

## Práctica 1: BasicVacuumCleaner <br>
Programa una aspiradora de gama baja para que limpie una casa movíendose pseudoaleatoriamente por ella. Se programará un autómata con al menos 3 estados (avanzando, retrocediendo, girando). Se puede incluir un estado haciendo-espiral, que aumenta la eficiencia del barrido. Con él se logra una navegación pseudoaleatoria que estocásticamente cubre toda la casa, limpiándola. La aspiradora, al ser de gama baja no incorpora algoritmos robustos y precisos de autolocalización. La posición estimada por la odometría no se puede usar como posición verdadera, pues acumula ruido. Sí se puede utilizar la orientación para medir giros de modo aproximado. 

El autómata se implementará como un bucle infinito. No se admiten sleeps que rompan la reactividad del bucle principal (no uséis sleep!). <br>

### Solución Propuesta <br>
La aspiradora consta de 4 estados , siendo estos ESPIRAL, AVANZANDO, RETROCEDIENDO y GIRANDO. La aspiradora empezará en estado ESPIRAL y se mantendrá en este estado durante 14 segundos a menos que se encuentre un obstáculo, cuando salga del estado de ESPIRAL tras los 14 segundos, entrará en estado AVANZAR y se mantendrá en este estado durante un lapso de 5 a 12 segundos de forma aleatoria antes de volver al estado de ESPIRAL a menos que detecte un obstáculo antes. En caso de detección de obstáculo a menos de 0.3m el robot sale de su estado actual y entra en el estado RETROCEDIENDO durante 0.8 segundos después de los cuales realiza entra en estado GIRANDO y gira entre 40 y 120 grados de forma aleatoria para volver al estado AVANZANDO. Para la detección de obstáculos el robot accede a los datos del Laser en un cono de los 60º a los 121º en frente suya. <br> <br>

### Resultado <br>
Al principio los parámetros eran distintos a los especificados anteriormente pero tras unos intentos para reajustarlo decidí usar esos parámetros. <br> Aquí se puede ver un video y captura del funcionamiento: <br>
[VIDEO](RECURSOS/Practica_1/BasicVacuumSim.mp4) <br>
<img width="465" height="979" alt="Captura desde 2026-10-06 17-47-10" src="https://github.com/user-attachments/assets/5b771845-8616-4452-8242-a8289ce1d5bf" />

Un problema que me he encontrado es que al tener ese rango de detección con el laser y los 0.3 metros de margen a veces detecta como obstáculo paredes que no le obstruyen realmente el paso, pero si reducia el rango del laser había casos en los que se chocaba con un obstáculo y seguía intentando avanzar.<br> <br> <br>

## Práctica 2: Follow Line <br>
Programa un coche de Fórmula1, equipado con una cámara frontal, para que recorra un circuito de carreras que tiene una línea roja en el medio de la pista. Se programará uno o varios controladores PID para gobernar la velocidad lineal del coche (V) y la velocidad angular (W). El coche debe funcionar en varios circuitos, completarlos en el menor tiempo posible y sin alejarse de la línea roja. Cuantas menos oscilaciones tenga mejor, y conviene que sea robusto si en algún momento pierde la línea.

La aplicación robótica se implementará como un bucle infinito. Cada iteración incluirá las instrucciones para materializar el procesamiento de la imagen y para la toma de decisiones sobre los dos actuadores V y W. <br>

### Solución Propuesta <br>
Se obtienen imagenes de la camara del Formula en formato BGR y se transforman a HSV (ya que será más fácil identificar el color rojo de la línea independientemente de la saturación y otros factores), después creamos una máscara a partir de la imágen en la que solo se reconozca el color rojo para poder seguir la línea. A partir de esta máscara creo una franja recortada de esta ya que es innecesario que el coche vea toda la imagen ya que puede haber errores al identificar otras cosas rojas por ejemplo. Después se identifica el centro exacto de la linea (se puede ver un circulito que representa el centro de la linea creado con cv2.circle) y se implementa un controlador PD (no uso componente integral) para el movimiento del Formula y la corrección de errores en giros y velocidad.

### Resultado <br>
El rango de valores HSV esta dividido en 2 ya que el rojo se encuentra al principio y final del espectro, por lo que hay que tener dos rangos para reconocer los rojos. Para que la máscara acepte ambos rangos se usa cv2.bitwise_or. No he tenido problemas al implementar la franja ni la identificación del centro de la linea. Después para la implementación del controlador PD he tenido que ir probando valores diferentes para el componente proporcional y el derivativo con tal de conseguir que el coche pueda tener una velocidad lineal de 12m/s y angular de 4.5rad/s llegando a los valores de KP = 0.008 y KD = 0.09. El coche completa el primer circuito en unos 45 segundos sin muchas oscilaciones. Aqui se pueden ver una captura y un video del mismo a parte de videos de los demas circuitos y una prueba en caso de no ver la linea:
