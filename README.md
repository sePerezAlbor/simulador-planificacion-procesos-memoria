# Simuladores de Sistemas Operativos

Cinco herramientas para resolver y verificar los ejercicios de clase. Cada una es un solo archivo HTML: funcionan en el navegador, sin instalar nada y sin conexión.

### 👉 [Planificación de procesos](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/planificacion-procesos.html) &nbsp;·&nbsp; [Gestión de memoria](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/gestion-memoria.html) &nbsp;·&nbsp; [Combinado](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/procesos-memoria.html)
### 👉 [Memoria virtual](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/memoria-virtual.html) &nbsp;·&nbsp; [Planificación de disco](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/planificacion-disco.html)

También se pueden descargar ([`planificacion-procesos.html`](planificacion-procesos.html), [`gestion-memoria.html`](gestion-memoria.html), [`procesos-memoria.html`](procesos-memoria.html), [`memoria-virtual.html`](memoria-virtual.html), [`planificacion-disco.html`](planificacion-disco.html)) y abrir con doble clic.

Las dos comparten la misma lógica: una **cola de trabajo** que se llena a mano, la marca **OK** sobre cada fila ya atendida, la **espera** calculada como `tiempo actual − llegada`, un **recorrido paso a paso** por cada unidad de tiempo, y **modo claro y oscuro**.

---

## Planificación de procesos

Recibe una cola de trabajo y simula la planificación mostrando lo mismo que se llena a mano en la hoja: Cola de Trabajo, Cola de Listos, Cola de I/O y Cola de Terminados, los diagramas de **CPU** e **I/O** con una celda por unidad de tiempo, y el **TP** de cada proceso con el **TEP**.

En los diagramas, el rótulo `Px(z)` significa el proceso `Px` con `z` unidades pendientes al inicio de esa celda. `LIBRE` marca el recurso ocioso.

**Tipos de ejercicio:** solo CPU, y CPU – I/O – CPU con el número de ráfagas de E/S que se quiera. Las ráfagas alternan CPU e I/O empezando y terminando siempre en CPU, así que con *n* ráfagas de E/S hay *n+1* de CPU. Un único dispositivo de I/O.

| Algoritmo | Orden de desempate |
|---|---|
| FCFS | llegada → menor CPU → prioridad → primero en la cola |
| SJF no apropiativo | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad no apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Round Robin | llegada → menor CPU → prioridad → primero en la cola |
| SRTF (SJF apropiativo) | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Cola de I/O (siempre FCFS) | llegada → menor I/O → prioridad → primero en la cola |

Trae cargados los **10 ejercicios de práctica** del curso: se escogen en el desplegable junto a *Cargar ejemplo* y cada uno ajusta solo su quantum. El botón **Generar aleatorio** inventa uno nuevo del tipo que tengas puesto, con pocos procesos y ráfagas cortas para que quepa en una hoja.

En Round Robin el **quantum es obligatorio**, no trae valor por defecto. En cada selección se indica **cuál criterio desempató** y qué candidatos había. La casilla **Resumir** junta las filas repetidas del mismo proceso cuando corrió sin parar, sin alterar los totales.

### Cómo funciona por dentro

Sigue el pseudocódigo del curso, en este orden dentro de cada unidad de tiempo:

1. Pasan a Listos los procesos de la Cola de Trabajo con `llegada ≤ Tiempo`.
2. Si la CPU está libre y hay listos, se selecciona según el algoritmo y se calcula su espera.
3. Si la I/O está libre y hay cola de I/O, se selecciona por FCFS.
4. Se ejecuta un ciclo de CPU.
5. Se ejecuta un ciclo de I/O.
6. `Tiempo = Tiempo + 1`.
7. Si el proceso de la CPU terminó su ráfaga o agotó su quantum, sale: a la Cola de I/O si reclama E/S, a Terminados si acabó, o de vuelta a Listos si le queda CPU.
8. Si el proceso de la I/O terminó, vuelve a Listos.

En los apropiativos el proceso vuelve a la Cola de Listos como **fila nueva**, con llegada igual al tiempo actual y la CPU que le queda. En SRTF y prioridad apropiativa eso ocurre cada unidad de tiempo. Cuando en un mismo instante sale un proceso de la CPU y llega otro de la Cola de Trabajo, **entra primero el que salió de la CPU**.

---

## Gestión de memoria

Recibe una cola de trabajo y simula la ocupación de la RAM instante por instante. Cada columna del diagrama es una unidad de tiempo, y dentro van los bloques de memoria de arriba hacia abajo con el rótulo `Px(tamañoK) (tiempo que le queda)` o `LIBRE (tamaño)`.

**Modos:** particiones fijas, particiones variables, y particiones variables con compactación.

**Algoritmos de ajuste:** primer ajuste, mejor ajuste y peor ajuste, para escoger entre los huecos libres donde cabe el proceso. La página explica con un ejemplo dibujado en qué se diferencian.

El botón **Generar aleatorio** inventa un ejercicio del modo que tengas puesto. Descarta los que saldrían triviales: siempre hay al menos un proceso que debe esperar, y en modo compactación siempre se llega a compactar.

### Cómo funciona por dentro

En cada unidad de tiempo:

1. Se recorre la cola de trabajo en orden. Los que ya llegaron y no están en memoria se intentan ubicar con el algoritmo de ajuste. **El que no quepa se salta** y lo intenta el siguiente.
2. Se dibuja la columna de ese instante.
3. Baja en 1 el tiempo restante de cada proceso en memoria.
4. `Tiempo = Tiempo + 1`.
5. Salen los procesos que llegaron a 0 y pasan a Terminados, **en el orden en que estaban en memoria**, de arriba hacia abajo.
6. Los huecos libres que quedan pegados **se fusionan en uno solo**.
7. En modo compactación, los procesos que quedan **se corren hacia arriba**, dejando un único hueco al final. Esos instantes se marcan en el diagrama.

En **particiones fijas** la memoria se divide en particiones iguales y la división debe ser exacta. Un proceso más grande que una partición ocupa **varias seguidas**: con particiones de 200K, un proceso de 210K queda como `P2(200K)` + `P2(10K)`. Lo que sobra dentro de una partición no lo puede usar nadie más.

En **particiones variables** la memoria es un solo bloque que se va partiendo según lo que pida cada proceso.

---

---

## Combinado: procesos y memoria

Une las dos anteriores. Aquí la memoria es el **filtro de entrada**: un proceso llega a la Cola de Trabajo, tiene que conseguir espacio en RAM, y **solo cuando logra entrar a memoria** pasa a la Cola de Listos y empieza a competir por la CPU. Mientras no haya espacio se queda afuera esperando.

La Cola de Trabajo gana una columna de **Tamaño (K)**, y desaparece la de *tiempo en el sistema*: lo que el proceso dura en memoria ya no es un dato, sale de sus ráfagas.

Funciona con los dos tipos de ejercicio (solo CPU y CPU – I/O – CPU, con el número de ráfagas de E/S que se quiera) y con los seis algoritmos de planificación.

### Reglas propias

1. Se recorre la Cola de Trabajo en orden. El que quepa entra a memoria y pasa **en el mismo instante** a la Cola de Listos; **el que no quepa se salta** y lo intenta el siguiente.
2. El proceso **conserva su memoria** mientras esté listo, en CPU o bloqueado en E/S.
3. La memoria se libera **solo al terminar**, y ahí mismo se compacta.
4. La espera de un proceso suma **las tres**: la de la Cola de Trabajo esperando memoria, las de la Cola de Listos esperando CPU y las de la Cola de I/O.

Como se compacta apenas alguien termina, **siempre queda un único hueco libre**. Por eso aquí el algoritmo de ajuste no aplica: nunca hay dónde escoger.

---

## Memoria virtual: reemplazo de páginas

Recibe una cadena de referencias y un número de marcos, y arma la tabla paso a paso: una columna por referencia, con el contenido de cada marco, el puntero, la víctima y el resultado.

**Algoritmos:** FIFO, Óptimo, LRU y Clock.

Convenciones del curso, que no son las de los libros:

- Un **fallo** es solo cuando hay que **sacar** una página. Si entra a un marco vacío es una **carga** y no cuenta.
- **Tasa de fallos** = fallos / número de referencias.
- En **Óptimo**, cada página lleva `página(D)`: en cuántos pasos vuelve a aparecer. El contador baja 1 por paso y **no se detiene en 0**: una página que ya no se usa queda en 0 y sigue a −1, −2… Salen primero las que están en **0 o en negativo**; entre varias de esas, la de mayor número.
- En **LRU**, cada página lleva `página(C)`: al referenciarse recibe un valor igual al número de marcos y baja 1 por paso. Sale la de menor número.
- En **Clock**, el bit de uso se activa **solo cuando la página vuelve a aparecer**; una página nueva entra con el bit en 0.

Como las cargas no cuentan, estos números son **los del libro menos el número de marcos**. Ojo con eso: la anomalía de Belady queda invisible (FIFO da 6 con 3 marcos y 6 con 4, cuando en el libro sería 9 y 10).

---

## Planificación de disco

Recibe la posición inicial del cabezal y las solicitudes en orden de llegada. Muestra la tabla de recorrido (paso, cilindro, distancia) con el total, y la **gráfica del movimiento del brazo**: los cilindros en el eje horizontal con la escala completa, los pasos bajando por el eje vertical, y los puntos unidos en orden.

**Algoritmos:** FCFS, SSTF, SCAN ascendente, SCAN descendente y SCAN-C.

El brazo **se devuelve en la última solicitud**, no en el extremo físico del disco. Esto es lo que los libros llaman LOOK y C-LOOK, pero en el curso se llaman SCAN y SCAN-C.

Dos reglas que el profesor no ha confirmado quedaron como opción:

- **Empate en SSTF**, cuando dos solicitudes están a la misma distancia.
- **El salto de regreso de SCAN-C**, si se suma o no al recorrido total. Se muestran los dos valores.

---

## Estado

Las cinco están verificadas: las dos primeras contra los ejercicios resueltos en clase, celda por celda. La combinada se comprobó con 480 simulaciones sobre los seis algoritmos y los dos tipos de ejercicio, verificando que la RAM siempre sume el total y que nunca aparezca más de un hueco libre. Las de memoria virtual y disco reproducen los 13 casos de prueba, incluido el ejercicio de clase y la cadena de Silberschatz del libro.
