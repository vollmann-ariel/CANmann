# CANmann — Visor de trazas CAN

Herramienta de escritorio/navegador para inspeccionar logs de una red CAN, al estilo de **CANalyzer**: decodifica cada mensaje usando archivos **DBC** y permite explorar la traza y graficar señales en el tiempo.

Es un único archivo HTML autocontenido (`can_viewer.html`) — sin instalación, sin build, sin dependencias externas. Se abre directo en el navegador y todo corre localmente: ningún archivo cargado (DBC, CSV, ASC) sale de tu máquina.

## Uso

1. Abrí `can_viewer.html` en Chrome/Edge (necesita `<input type="file">`, drag & drop, Canvas 2D).
2. **Archivos DBC**: cargá uno o más `.dbc`, uno por bus. Si el nombre incluye `CAN1`, `CAN2`, etc., el canal correspondiente del log se asigna automáticamente a ese DBC.
3. **Log CAN**: cargá un `.csv` (formato `Timestamp,CAN_ID,DLC,Data`, el que genera `CANmann.py`) o un `.asc` (Vector ASCII).
4. Elegí el DBC de cada canal en **Canal → DBC** si el mapeo automático no fue correcto.

## Pestaña Traza

- Tabla virtualizada (soporta cientos de miles de mensajes sin lag), con filtro por ID/nombre/canal y toggle "solo decodificados".
- Panel de señales decodificadas al seleccionar un mensaje: valor físico, crudo, unidad, etiquetas de tablas `VAL_`, multiplexado, y aviso cuando un mensaje J1939 multi-paquete (ej. DM1) excede los 8 bytes del frame capturado.
- Reproducción de la traza con control de velocidad.
- Lista de "Mensajes vistos" ordenada por frecuencia, con indicador verde/rojo de decodificado o no.
- "Más variaciones": ranking de señales por cantidad de cambios de valor reales (no solo por cuántas veces se transmitió el mensaje).

## Pestaña Gráficas

- Elegí cualquier señal reconocida por el DBC (desde la lista de mensajes o el buscador) y se agrega como un carril apilado, con eje Y propio.
- Línea escalonada (una señal CAN mantiene su valor hasta el próximo mensaje).
- Cursor sincronizado entre todos los carriles y el eje de tiempo, con panel de lectura que muestra valor, descripción del DBC y timestamp real del último mensaje de cada señal.
- Zoom con rueda del mouse y pan horizontal arrastrando; arrastre vertical para desplazarse entre carriles que no entran en pantalla.
- Vista alternativa como tabla para cada señal.

## Formato de archivos soportados

| Archivo | Formato |
|---|---|
| DBC | Windows-1252, sintaxis estándar (`BO_`, `SG_`, `VAL_`, `CM_`, multiplexado simple) |
| Log | `.csv` (`Timestamp,CAN_ID,DLC,Data`) o `.asc` (Vector ASCII, multi-canal) |

La decodificación de bits (Intel/Motorola, con signo, multiplexado, truncamiento de mensajes multi-paquete) fue validada contra [`cantools`](https://github.com/cantools/cantools) antes de la primera versión.

## Limitaciones conocidas

- No lee archivos `.blf` binarios directamente — Vector BLF usa contenedores comprimidos en un formato propietario. Convertí a `.csv` o `.asc` primero (por ejemplo con `python-can`).
- Pensado para Chrome/Edge de escritorio; no optimizado para mobile.
