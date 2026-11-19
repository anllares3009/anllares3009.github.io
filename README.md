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
El rango de valores HSV esta dividido en 2 ya que el rojo se encuentra al principio y final del espectro, por lo que hay que tener dos rangos para reconocer los rojos. Para que la máscara acepte ambos rangos se usa cv2.bitwise_or. No he tenido problemas al implementar la franja ni la identificación del centro de la linea. Después para la implementación del controlador PD he tenido que ir probando valores diferentes para el componente proporcional y el derivativo con tal de conseguir que el coche pueda tener una velocidad lineal de 12m/s y angular de 4.5rad/s llegando a los valores de KP = 0.008 y KD = 0.09. El coche completa el primer circuito en unos 45 segundos sin muchas oscilaciones. Aqui se pueden ver una captura y un video del mismo a parte de videos de los demas circuitos y una prueba en caso de no ver la linea: <br>
[VIDEO_Circuito1](RECURSOS/Practica_2/Circuito1.mp4) <br>
[VIDEO_Circuito2](RECURSOS/Practica_2/Circuito2.mp4) <br>
[VIDEO_Circuito3](RECURSOS/Practica_2/Circuito3.mp4) <br>
[VIDEO_Circuito4](RECURSOS/Practica_2/Circuito4.mp4) <br>
[VIDEO_NoLine](RECURSOS/Practica_2/NoLine.mp4) <br>
<img width="1356" height="979" alt="Captura desde 2026-10-16 20-24-10" src="https://github.com/user-attachments/assets/47a2ea9d-5041-48f3-b287-cbbc79c871a1" /> <br>

## Práctica 3: ObstacleAvoidance <br>
Programa un coche de Fórmula1, equipado con un sensor laser y un sensor GPS, para que recorra un circuito de carreras con valla en los laterales y con otros coches como obstáculos a lo largo del recorrido. Se programará el algoritmo de navegación local VFF con fuerzas virtuales para gobernar la velocidad lineal del coche (V) y la velocidad angular (W) en cada iteración. El coche debe completar el circuito en el menor tiempo posible y siempre manteniendo la seguridad de que no choca con ningún obstáculo. El sistema de navegación global ha planificado una secuencia de subobjetivos de modo que si se van alcanzando consecutivamente garantiza que se completa la vuelta al circuito. Se considerará un subobjetivo alcanzado cuando la posición del coche está razonablemente de la posición del subobjetivo. En ese caso se pasará a navegar localmente hacia el siguiente subobjetivo.

La aplicación robótica se implementará como un bucle infinito. Cada iteración incluirá las instrucciones para materializar el procesamiento del sensor laser, las fuerzas repulsivas, la fuerza atractiva, su combinación y su traducción a decisiones sobre los dos actuadores V y W. <br>

### Solución Propuesta <br>
Se hace uso de los waypoints para generar un vector de fuerza atractiva y luego haciendo uso del sensor laser se sacan todos los obstaculos a menos de 10 metros y con ellos calculo un vector repulsivo por cada uno y los divido entre la distancia al cuadrado para que los vectores de repulsion sean mucho menores cuanto mas lejos esten y mas fuertes cuanto mas cerca esten de cada obstaculo en si, con esta medida reducimos las oscilaciones del vehículo. Después sumamos todos estos vectores multiplicados por una constante para ajustar la repulsion junto al atractivo para que nos quede un vector resultante que será la dirección a seguir por el vehículo. Después mediante los valores obtenidos calculamos la velocidad lineal y angular del vehículo.

### Resultado <br>
Al final he conseguido implementar todolo necesario para que el vehículo se mueva mediante un Campo de Fuerzas Virtuales, sinembargo, me ha costado mucho ajustar los valores de las distintas constantes para llegar a un resultado en el que el coche llegara a la meta sin chocarse con ningún otro coche y sin oscilar en un tiempo aproximado de 2 minutos. Cabe recalcar que en cuanto la conexión a internet no es óptima el simulador falla y hay algun choque, no he conseguido ajustar los valores para que el coche funcione en estas condiciones más limitadas. Aqui dejo un video del funcionamiento: <br>
[VIDEO_Avoidance](RECURSOS/Practica_3/Circuito_Avoidance.mp4) <br>

## Práctica 4: GlobalNavigation

Programa un coche autónomo, equipado con un sensor GPS y un mapa de la ciudad, para que ejerza de taxi autónomo siendo capaz de planificar su navegación desde el lugar en el que se encuentra hasta otro punto de la ciudad como destino, utilizando el algoritmo de Gradient Path Planning. Este algoritmo es un ejemplo de plan como recurso, proporcionando un campo escalar que el robot puede emplear en cualquier momento para pedir consejo de hacia dónde moverse para navegar hacia el destino. El campo será la suma de dos componentes, el subcampo de propagación a destino y el subcampo de penalización por cercanía a obstáculos. Además de la planificación utilizando el mapa la aplicación robótica deberá pilotar el coche siguiendo el gradiente de ese campo, gobernando reactivamente la velocidad lineal del coche (V) y la velocidad angular (W) en cada iteración. El pilotaje se implementará como un bucle reactivo. Idealmente, una vez alcanzado un destino remoto, el coche autónomo esperará un nuevo destino que marque el usuario humano picando en otro punto del mapa de la ciudad.

### Solución Propuesta

Se carga el mapa y se convierte en un mapa de obstáculos (celdas con valor menor que 50). Con cv2.distanceTransform se calcula la distancia de cada celda libre a la pared más cercana y se genera una penalización que crece cuanto más cerca está la celda de una pared (a menos de 6 celdas), de forma que la ruta tienda a ir por el centro de la carretera. Cuando se selecciona un destino nuevo se aplica el algoritmo Wave Front: una cola que parte de la celda del destino y asigna a cada celda libre vecina el coste acumulado, sumando 1 en movimientos ortogonales y 1.414 en diagonales, más la penalización de pared. La expansión se detiene al llegar a la celda del taxi. Después se obtiene la ruta bajando siempre hacia el vecino de menor coste desde la posición del taxi hasta el destino, y se dibuja con WebGUI.showPath. Para el movimiento se busca el punto de la ruta más cercano al taxi y se toma como objetivo local el punto 8 celdas por delante. Se calcula el error de ángulo hacia ese punto y se aplica un W proporcional (2.5 por el error) y una V de 8.5, que baja a 2.5 si el error supera 0.35 rad. Al estar a menos de 3 metros del destino el taxi se detiene.

### Resultado

El taxi llega al destino siguiendo la ruta calculada sin chocar con las paredes. Un problema que me encontré al principio es que WebGUI.worldToGrid devuelve las coordenadas como (columna, fila), al revés de como se indexa el mapa en numpy, por lo que hay que intercambiarlas, y si no la ruta atravesaba paredes. Otro problema fue que el camino más corto iba pegado a los bordes de la carretera y el taxi chocaba en las curvas, y lo solucioné con la penalización según la distancia a la pared. Aquí se puede ver un vídeo del funcionamiento: <br>
[VIDEO_GlobalNavigation](RECURSOS/Practica_4/global.mp4)<br>
