# Simuladores de Sistemas Operativos

Dos herramientas para resolver y verificar los ejercicios de clase. Cada una es un solo archivo HTML: funcionan en el navegador, sin instalar nada y sin conexión.

### 👉 [Planificación de procesos](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/planificacion-procesos.html) &nbsp;·&nbsp; [Gestión de memoria](https://seperezalbor.github.io/simulador-planificacion-procesos-memoria/gestion-memoria.html)

También se pueden descargar ([`planificacion-procesos.html`](planificacion-procesos.html), [`gestion-memoria.html`](gestion-memoria.html)) y abrir con doble clic.

Las dos comparten la misma lógica: una **cola de trabajo** que se llena a mano, la marca **OK** sobre cada fila ya atendida, la **espera** calculada como `tiempo actual − llegada`, un **recorrido paso a paso** por cada unidad de tiempo, y **modo claro y oscuro**.

---

## Planificación de procesos

Recibe una cola de trabajo y simula la planificación mostrando lo mismo que se llena a mano en la hoja: Cola de Trabajo, Cola de Listos, Cola de I/O y Cola de Terminados, los diagramas de **CPU** e **I/O** con una celda por unidad de tiempo, y el **TP** de cada proceso con el **TEP**.

En los diagramas, el rótulo `Px(z)` significa el proceso `Px` con `z` unidades pendientes al inicio de esa celda. `LIBRE` marca el recurso ocioso.

**Tipos de ejercicio:** solo CPU, y CPU – I/O – CPU (tres ráfagas, un único dispositivo de I/O).

| Algoritmo | Orden de desempate |
|---|---|
| FCFS | llegada → menor CPU → prioridad → primero en la cola |
| SJF no apropiativo | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad no apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Round Robin | llegada → menor CPU → prioridad → primero en la cola |
| SRTF (SJF apropiativo) | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Cola de I/O (siempre FCFS) | llegada → menor I/O → prioridad → primero en la cola |

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

**Algoritmos de ajuste:** primer ajuste, mejor ajuste y peor ajuste, para escoger entre los huecos libres donde cabe el proceso.

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

## Estado

Las dos están verificadas contra los ejercicios resueltos en clase: reproducen las colas, los diagramas y los tiempos celda por celda.
