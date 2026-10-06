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

Un problema que me he encontrado es que al tener ese rango de detección con el laser y los 0.3 metros de margen a veces detecta como obstáculo paredes que no le obstruyen realmente el paso, pero si reducia el rango del laser había casos en los que se chocaba con un obstáculo y seguía intentando avanzar.<br>
