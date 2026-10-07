# Simulador de Planificación de Procesos

Página local para resolver y verificar ejercicios de **administración de procesos** de Sistemas Operativos.

Es un solo archivo HTML: se abre con doble clic, no necesita instalar nada ni conexión a internet.

👉 [`planificacion-procesos.html`](planificacion-procesos.html)

## Qué hace

Recibe una **Cola de Trabajo** y simula la planificación paso a paso, mostrando lo mismo que se llena a mano en la hoja:

- Cola de Trabajo, Cola de Listos, Cola de I/O y Cola de Terminados, con la marca **OK** en cada fila ya seleccionada.
- Diagrama de **CPU** y de **I/O**, una celda por unidad de tiempo, con el rótulo `Px(z)` donde `z` es lo que le queda al proceso al inicio de esa celda, y `LIBRE` cuando el recurso está ocioso.
- **TP** de cada proceso (suma de todas sus esperas) y **TEP** (promedio).

### Tipos de ejercicio

- **Solo CPU**
- **CPU – I/O – CPU** (tres ráfagas, un único dispositivo de I/O)

### Algoritmos

| Algoritmo | Orden de desempate |
|---|---|
| FCFS | llegada → menor CPU → prioridad → primero en la cola |
| SJF no apropiativo | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad no apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Round Robin | llegada → menor CPU → prioridad → primero en la cola |
| SRTF (SJF apropiativo) | menor CPU → llegada → prioridad → primero en la cola |
| Prioridad apropiativa | prioridad → menor CPU → llegada → primero en la cola |
| Cola de I/O (siempre FCFS) | llegada → menor I/O → prioridad → primero en la cola |

La **prioridad se interpreta con menor número = mayor prioridad**. El tiempo siempre empieza en 0.

## Cómo se usa

1. Elige el tipo de ejercicio y el algoritmo. En Round Robin el **quantum es obligatorio**, no trae valor por defecto.
2. Llena la Cola de Trabajo, o pulsa **Cargar ejemplo**.
3. Pulsa **Simular**. Quedas en el resultado final.
4. Con **◀ Anterior** / **Siguiente ▶** (o las flechas del teclado) recorres la simulación unidad por unidad.
5. En *Qué pasó en este paso* aparece la justificación de cada selección, con el criterio que desempató y los candidatos que había.
6. La casilla **Resumir** junta las filas repetidas del mismo proceso cuando corrió sin parar. No altera los totales.

> Al retroceder pasos, el TEP que se muestra es el acumulado hasta ese instante, no el final.

## Cómo funciona por dentro

El motor sigue el pseudocódigo del curso, en este orden dentro de cada unidad de tiempo:

1. Pasan a Listos los procesos de la Cola de Trabajo con `llegada ≤ Tiempo`.
2. Si la CPU está libre y hay listos, se selecciona según el algoritmo y se calcula su espera.
3. Si la I/O está libre y hay cola de I/O, se selecciona por FCFS.
4. Se ejecuta un ciclo de CPU.
5. Se ejecuta un ciclo de I/O.
6. `Tiempo = Tiempo + 1`.
7. Si el proceso de la CPU terminó su ráfaga o agotó su quantum, sale: a la Cola de I/O si reclama E/S, a Terminados si acabó, o de vuelta a Listos si le queda CPU.
8. Si el proceso de la I/O terminó, vuelve a Listos.

En los apropiativos el proceso vuelve a la Cola de Listos como **fila nueva**, con llegada igual al tiempo actual y la CPU que le queda. En SRTF y prioridad apropiativa eso ocurre cada unidad de tiempo. Cuando en un mismo instante sale un proceso de la CPU y llega otro de la Cola de Trabajo, **entra primero el que salió de la CPU**.

## Estado

Verificado contra los ejercicios resueltos en clase: reproduce las colas, los diagramas y el TEP celda por celda.

Pendiente: la parte de **gestión de memoria**.
