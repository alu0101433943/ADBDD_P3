# Modelo Entidad-Relación (Tajinaste S.A.) 

---

## Entidades

### VIVERO  
**Descripción:** punto de venta/almacén físico.  
**PK:** `id_vivero`  
**Atributos:**  
- `id_vivero` — ENTERO — Ej.: `1`.  
- `nombre` — CADENA — Ej.: `"Vivero Norte"`.  
- `direccion` — CADENA — Ej.: `"C/ Flores 10, Santa Cruz"`.  
- `latitud` — DECIMAL ([-90,90]) — Ej.: `28.4636`.  
- `longitud` — DECIMAL ([-180,180]) — Ej.: `-16.2518`.

---

### ZONA (entidad débil de VIVERO)  
**Descripción:** área dentro del vivero (p. ej. zona exterior, almacén). Depende de VIVERO.  
**Clave parcial / PK completa:** `codigo_zona` ; PK completa = (`id_vivero`, `codigo_zona`).  
**Atributos:**  
- `id_vivero` — ENTERO (FK→VIVERO) — Ej.: `1`.  
- `codigo_zona` — CADENA corta — Ej.: `"ZEX"`.  
- `nombre_zona` — CADENA — Ej.: `"Zona Exterior"`.  
- `descripcion` — CADENA — Ej.: `"Exposición de arbustos"`.  
- `latitud` — DECIMA — Ej.: `28.4637`.  
- `longitud` — DECIMAL — Ej.: `-16.2519`.

---

### PRODUCTO  
**Descripción:** artículo vendible.  
**PK:** `id_producto`  
**Atributos:**  
- `id_producto` — ENTERO — Ej.: `1001`.  
- `nombre` — CADENA — Ej.: `"Rosal 'Red Eden'"`.  
- `descripcion` — CADENA — Ej.: `"Rosal trepador, 40cm"`.  
- `tipo` — CADENA — Ej.: `"Planta"`.  
- `precio_unitario` — DECIMAL ≥ 0 — Ej.: `12.50`.

---

### EMPLEADO  
**Descripción:** trabajador que puede ser destinado a zonas y gestionar pedidos.  
**PK:** `id_empleado`  
**Atributos:**  
- `id_empleado` — ENTERO — Ej.: `501`.  
- `nombre` — CADENA — Ej.: `"María"`.  
- `apellidos` — CADENA — Ej.: `"González Pérez"`.  
- `dni` — CADENA — Ej.: `"12345678A"`.  
- `fecha_contratacion` — FECHA `YYYY-MM-DD` — Ej.: `2020-06-01`.

---

### CLIENTE  
**Descripción:** comprador; puede pertenecer a Tajinaste Plus.  
**PK:** `id_cliente`  
**Atributos:**  
- `id_cliente` — ENTERO — Ej.: `200`.  
- `nombre`, `apellidos` — CADENA — Ej.: `"Luis Martínez"`.  
- `email` — CADENA — Ej.: `"luis@example.com"`.  
- `telefono` — CADENA — Ej.: `"+34 600 123 456"`.  
- `es_tajinaste_plus` — BOOLEANO — Ej.: `true`.  
- `fecha_ingreso_plus` — FECHA (nullable) — Ej.: `2023-02-15` o `NULL`.  
- `bono_mensual` — DECIMAL ≥ 0 (calculable) — Ej.: `5.00`.

---

### PEDIDO  
**Descripción:** orden de compra.  
**PK:** `id_pedido`  
**Atributos:**  
- `id_pedido` — ENTERO — Ej.: `9001`.  
- `id_cliente` — ENTERO (FK→CLIENTE) — Ej.: `200`.  
- `id_empleado_responsable` — ENTERO (FK→EMPLEADO, NOT NULL) — Ej.: `501`.  
- `fecha` — FECHA `YYYY-MM-DD` — Ej.: `2024-05-12`.  
- `total` — DECIMAL ≥ 0 — Ej.: `78.40`.  

---

### PRODUCTIVIDAD_ZONA (entidad débil de ZONA)  
**Descripción:** registro histórico mensual de una zona.  
**PK completa:** (`id_vivero`, `codigo_zona`, `periodo`) donde `periodo` es PK parcial.  
**Atributos:**  
- `periodo` — CADENA `YYYY-MM` — Ej.: `"2024-05"`.  
- `ventas_monetarias` — DECIMAL ≥ 0 — Ej.: `1540.75`.  
- `unidades_vendidas` — ENTERO ≥ 0 — Ej.: `320`.  

---

### PRODUCTIVIDAD_EMPLEADO (entidad débil de EMPLEADO)  
**Descripción:** registro histórico mensual de un empleado.  
**PK completa:** (`id_empleado`, `periodo`) donde `periodo` es PK parcial.  
**Atributos:**  
- `periodo` — CADENA `YYYY-MM` — Ej.: `"2024-05"`.  
- `ventas_monetarias` — DECIMAL ≥ 0 — Ej.: `1200.00`.  
- `pedidos_gestionados` — ENTERO ≥ 0 — Ej.: `18`. 

---

## Relaciones

1. **VIVERO (1,1) — LOCALIZA — ZONA (0,N)**  
   - Justificación: un vivero puede no tener zonas al crearse (0) o muchas (N); cada zona pertenece a un único vivero.  
   - Atributos de relación: ninguno.

2. **ZONA (0,N) — STOCK — PRODUCTO (0,N)**
  - Atributos de la relación STOCK:  
     - `cantidad_disponible` — ENTERO ≥ 0 — Ej.: `25`.  
     - `fecha_ultima_actualizacion` — FECHA `YYYY-MM-DD` — Ej.: `2024-05-10`.  
   - Justificación: una zona o un producto pueden no estar relacionados aún (0).

3. **EMPLEADO (0,N) — ASIGNACIÓN — ZONA (0,N)**   
   - Enlace desde ASIGNACIÓN: ASIGNACIÓN → EMPLEADO `(1,1)` y ASIGNACIÓN → ZONA `(1,1)`.  
   - Atributos de ASIGNACIÓN: `fecha_inicio` (FECHA / Ej. 2024-03-01), `fecha_fin` (FECHA nullable / Ej. 2024-05-31), `puesto` (CADENA / Ej. "Operario"), `observaciones` (CADENA opcional).  
   - Justificación: empleado o zona pueden no tener asignaciones registradas (min=0); cada asignación vincula un empleado y una zona.

4. **CLIENTE (0,N) — HACE — PEDIDO (1,1)**  
   - Atributos en PEDIDO: `id_pedido`, `fecha`, `total`, `id_empleado_responsable` (NOT NULL), `id_vivero_suministro` (opcional), `codigo_zona_suministro` (opcional).  
   - Justificación: cliente puede no haber hecho pedidos (0), pedido siempre tiene un cliente (1).

5. **EMPLEADO (0,N) — GESTIONA — PEDIDO (1,1)**  
   - Implementación: `id_empleado_responsable` en PEDIDO (FK NOT NULL).  
   - Justificación: empleado puede no gestionar pedidos (0); cada pedido tiene exactamente un responsable (1).

6. **PEDIDO (0,N) — DETALLE (entidad asociativa) — PRODUCTO (0,N)**  
   - Enlace desde DETALLE: DETALLE → PEDIDO `(1,1)` y DETALLE → PRODUCTO `(1,1)`.  
   - Atributos de DETALLE: `cantidad`, `precio_unitario_linea`, `descuento_linea` (opc.).  
   - Justificación: pedido puede empezar sin líneas (0) o tener muchas; producto puede no aparecer en pedidos (0).

7. **ZONA (1,1) — IDENTIFICA — PRODUCTIVIDAD_ZONA (0,N)**  *(PRODUCTIVIDAD_ZONA es entidad débil)*  
   - Atributos de PRODUCTIVIDAD_ZONA: `periodo` (YYYY-MM), `ventas_monetarias`, `unidades_vendidas`, `objetivo_periodo` (opc.).  
   - PK completa: (`id_vivero`, `codigo_zona`, `periodo`).  
   - Justificación: una zona puede no tener registros aún (0); cada registro pertenece a una única zona (1).

8. **EMPLEADO (1,1) — IDENTIFICA — PRODUCTIVIDAD_EMPLEADO (0,N)**  *(PRODUCTIVIDAD_EMPLEADO es entidad débil)*  
   - Atributos de PRODUCTIVIDAD_EMPLEADO: `periodo`, `ventas_monetarias`, `pedidos_gestionados`, `cumplimiento_objetivo` (opc.).  
   - PK completa: (`id_empleado`, `periodo`).  
   - Justificación: empleado puede no tener registros (0); cada registro pertenece a un único empleado (1).

---

## Restricciones semánticas

1. **ZONA es entidad débil:** PK completa `(id_vivero, codigo_zona)`. No existe ZONA sin VIVERO.  
2. **PRODUCTIVIDAD_* son ent. débiles:** PK completa incluye la clave del padre + `periodo`. No existe registro de productividad sin su padre (ZONA o EMPLEADO).  
3. **Empleado sin doble destino simultáneo:** para cada `id_empleado` no deben existir dos ASIGNACIÓN cuyos intervalos `[fecha_inicio, fecha_fin]` se solapen (salvo `NULL` en `fecha_fin` que indica vigente). Comprobación a nivel de aplicación o trigger.  
4. **PEDIDO con responsable obligatorio:** `id_empleado_responsable` en PEDIDO es NOT NULL.  
5. **STOCK no negativo:** `cantidad_disponible >= 0`. Al confirmar un pedido, las cantidades en DETALLE se restan del STOCK pertinente (política de elección de zona de depleción fuera del E/R).  
6. **DETALLE_PEDIDO único por línea:** no permitir duplicados `(id_pedido, id_producto)`; actualizar cantidad si se reingresa la misma línea según política.  
7. **Campañas Tajinaste Plus:** para análisis considerar solo PEDIDO con `fecha >= fecha_ingreso_plus` cuando `es_tajinaste_plus = true`.  
8. **Formatos y dominios:** fechas `YYYY-MM-DD`; periodo `YYYY-MM`; lat/long dentro de rangos; cantidades/precios ≥ 0.
