# README — Modelo Entidad-Relación (Tajinaste S.A.)

Este documento describe las entidades, atributos (dominio y ejemplos), relaciones y restricciones semánticas del modelo E/R diseñado para Tajinaste S.A. (diagrama en notación Chen).

---

## Entidades

### VIVERO
**Descripción:** Punto de venta/almacen físico de la empresa.  
**Clave primaria:** `id_vivero`  
**Atributos:**
- `id_vivero` (ENTERO) — identificador único. Ej.: `1`, `42`.  
- `nombre` (CADENA) — nombre comercial. Ej.: `"Vivero Norte"`.  
- `direccion` (CADENA) — texto con dirección. Ej.: `"C/ Flores 10, Santa Cruz"`.  
- `latitud` (DECIMAL) — coordenada geográfica, rango `[-90.0,90.0]`. Ej.: `28.4636`.  
- `longitud` (DECIMAL) — coordenada geográfica, rango `[-180.0,180.0]`. Ej.: `-16.2518`.

---

### ZONA (entidad débil, dependiente de VIVERO)
**Descripción:** Área dentro de un vivero donde se colocan productos (p. ej. zona exterior, almacén). Su existencia depende del vivero.  
**Clave parcial:** `nombre_zona` — la PK completa se forma combinando `id_vivero` + `nombre_zona`.  
**Atributos:**
- `id_vivero` (ENTERO) — referencia al vivero contenedor. Ej.: `1`.  
- `nombre_zona` (CADENA) — nombre que identifica la zona dentro del vivero. Ej.: `"Zona Exterior"`.  
- `descripcion` (CADENA) — descripción breve. Ej.: `"Zona de exposición de arbustos"`.  
- `latitud` (DECIMAL) — coordenada de la zona (opcional, local). Ej.: `28.4637`.  
- `longitud` (DECIMAL) — coordenada de la zona (opcional, local). Ej.: `-16.2519`.  

---

### PRODUCTO
**Descripción:** Artículo vendible (plantas, herramientas, decoración).  
**Clave primaria:** `id_producto`  
**Atributos:**
- `id_producto` (ENTERO) — identificador único. Ej.: `1001`.  
- `nombre` (CADENA) — nombre comercial. Ej.: `"Rosal 'Red Eden'"`.  
- `descripcion` (CADENA) — descripción técnica o comercial. Ej.: `"Rosal trepador, 40cm"`.  
- `tipo` (CADENA) — categoría/tipo. Ej.: `"Planta"`, `"Herramienta"`.  
- `precio_unitario` (DECIMAL) — precio por unidad en la moneda local. Ej.: `12.50`.

---

### EMPLEADO
**Descripción:** Persona trabajadora que puede ser destinada a viveros y gestionar pedidos.  
**Clave primaria:** `id_empleado`  
**Atributos:**
- `id_empleado` (ENTERO) — identificador único. Ej.: `501`.  
- `nombre` (CADENA). Ej.: `"María"`.  
- `apellidos` (CADENA). Ej.: `"González Pérez"`.  
- `dni` (CADENA) — documento/número identificador. Ej.: `"12345678A"`.  
- `fecha_contratacion` (FECHA, `YYYY-MM-DD`). Ej.: `2020-06-01`.

---

### CLIENTE
**Descripción:** Persona que realiza compras. Algunos clientes pertenecen al programa de fidelización "Tajinaste Plus".  
**Clave primaria:** `id_cliente`  
**Atributos:**
- `id_cliente` (ENTERO). Ej.: `200`.  
- `nombre` (CADENA). Ej.: `"Luis"`.  
- `apellidos` (CADENA). Ej.: `"Martínez"`.  
- `email` (CADENA). Ej.: `"luis@example.com"`.  
- `telefono` (CADENA). Ej.: `"+34 600 123 456"`.  
- `es_tajinaste_plus` (BOOLEANO) — indica si pertenece al programa. Ej.: `true` / `false`.  
- `fecha_ingreso_plus` (FECHA, nullable) — fecha en la que entró al programa. Ej.: `2023-02-15` o `NULL`.  
- `bono_mensual` (DECIMAL, calculable/almacenable) — bonificación asignada según volumen de compras mensuales. Ej.: `5.00` (euros).

---

### PEDIDO
**Descripción:** Orden de compra realizada por un cliente. Cada pedido tiene exactamente un responsable (empleado).  
**Clave primaria:** `id_pedido`  
**Atributos:**
- `id_pedido` (ENTERO). Ej.: `9001`.  
- `id_cliente` (ENTERO, FK→CLIENTE). Ej.: `200`.  
- `id_empleado_responsable` (ENTERO, FK→EMPLEADO, no nulo). Ej.: `501`.  
- `fecha` (FECHA). Ej.: `2024-05-12`.  
- `total` (DECIMAL) — importe total del pedido. Ej.: `78.40`.

---

### DETALLE_PEDIDO (entidad asociativa PEDIDO–PRODUCTO)
**Descripción:** Líneas de cada pedido (relación N:M).  
**Clave primaria compuesta:** (`id_pedido`, `id_producto`)  
**Atributos:**
- `id_pedido` (ENTERO, FK→PEDIDO).  
- `id_producto` (ENTERO, FK→PRODUCTO).  
- `cantidad` (ENTERO) — unidades pedidas. Ej.: `3`.  
- `precio_unitario_linea` (DECIMAL) — precio aplicado en la línea. Ej.: `12.50`.

---

### PRODUCTIVIDAD_ZONA
**Descripción:** Histórico de métricas de venta por zona y periodo (sirve para análisis de productividad).  
**Clave primaria:** `id_prod_zona` (o PK compuesta `id_vivero`+`nombre_zona`+`periodo`)  
**Atributos:**
- `id_prod_zona` (ENTERO) — identificador de registro. Ej.: `7001`.  
- `id_vivero` (ENTERO, FK→VIVERO).  
- `nombre_zona` (CADENA, FK→ZONA).  
- `periodo` (CADENA o `YYYY-MM`) — periodo de agregación. Ej.: `"2024-05"`.  
- `ventas_monetarias` (DECIMAL). Ej.: `1540.75`.  
- `unidades_vendidas` (ENTERO). Ej.: `320`.

---

### PRODUCTIVIDAD_EMPLEADO
**Descripción:** Histórico de resultados por empleado y periodo.  
**Clave primaria:** `id_prod_emp` (o PK compuesta `id_empleado`+`periodo`)  
**Atributos:**
- `id_prod_emp` (ENTERO). Ej.: `8001`.  
- `id_empleado` (ENTERO, FK→EMPLEADO).  
- `periodo` (CADENA o `YYYY-MM`). Ej.: `"2024-05"`.  
- `ventas_monetarias` (DECIMAL). Ej.: `1200.00`.  
- `pedidos_gestionados` (ENTERO). Ej.: `18`.

---

## Relaciones (nombre, participantes, atributos de relación y cardinalidad)

> Notación de cardinalidad: `(min,max)` junto a cada participante, donde `1` indica exactamente uno y `N` indica muchos.

### 1. VIVERO — IDENTIFICA — ZONA  
**Tipo:** relación de identificación (identifying).  
**Participantes y cardinalidad:**  
- VIVERO `(1,1)` — IDENTIFICA — ZONA `(1,N)`  
**Semántica:** Un vivero identifica (contiene) una o muchas zonas; una zona pertenece a exactamente un vivero. ZONA es entidad débil.  
**Atributos de relación:** ninguno.  
**Ejemplo:** VIVERO `1` identifica `Zona Exterior`, `Almacén`.

---

### 2. ZONA — STOCK/UBICACIÓN — PRODUCTO  
**Tipo:** relación N:M (relación con atributos) — modela el stock por zona.  
**Participantes y cardinalidad:**  
- ZONA `(1,N)` — STOCK — PRODUCTO `(1,N)`  
(Interpretación: una zona puede contener muchos productos; un producto puede estar presente en muchas zonas.)  
**Atributos de la relación STOCK:**  
- `cantidad_disponible` (ENTERO) — unidades en esa zona. Ej.: `25`.  
- `fecha_ultima_actualizacion` (FECHA) — fecha del último ajuste de stock. Ej.: `2024-05-10`.  
**Semántica:** Permite saber cuánto hay disponible de cada producto en cada zona del vivero.  
**Ejemplo:** En `Vivero 1 / Zona Exterior`, `Producto 1001` tiene `cantidad_disponible = 12`.

---

### 3. EMPLEADO — ASIGNACIÓN — ZONA  
**Tipo:** relación N:N con atributos temporales (registra histórico).  
**Participantes y cardinalidad:**  
- EMPLEADO `(1,N)` — ASIGNACIÓN — ZONA `(1,N)`  
(Interpretación temporal: un empleado puede tener múltiples asignaciones a lo largo del tiempo; una zona puede recibir distintos empleados a lo largo del tiempo.)  
**Atributos de la relación ASIGNACIÓN:**  
- `fecha_inicio` (FECHA). Ej.: `2024-03-01`.  
- `fecha_fin` (FECHA, nullable). Ej.: `2024-09-30` o `NULL` si vigente.  
- `puesto` (CADENA). Ej.: `"Encargado de ventas"`.  
- `observaciones` (CADENA, opcional).  
**Semántica:** Registra en qué zona y en qué periodo trabajó un empleado (necesario para la trazabilidad y cálculo de productividad).  
**Ejemplo:** EMPLEADO `501` asignado a `Vivero 1 / Zona Exterior` desde `2024-03-01` hasta `2024-05-31`.

---

### 4. CLIENTE — HACE — PEDIDO  
**Tipo:** relación 1:N (un cliente hace muchos pedidos).  
**Participantes y cardinalidad:**  
- CLIENTE `(1,N)` — HACE — PEDIDO `(1,1)` (desde la perspectiva de PEDIDO: pertenece a exactamente un CLIENTE)  
**Atributos de la relación:** ninguno (los atributos están en PEDIDO).  
**Semántica:** Historial de compras por cliente; para campañas se consideran los pedidos realizados desde `fecha_ingreso_plus` si `es_tajinaste_plus = true`.  
**Ejemplo:** CLIENTE `200` hizo PEDIDO `9001` el `2024-05-12`.

---

### 5. EMPLEADO — GESTIONA — PEDIDO  
**Tipo:** relación 1:N (un empleado gestiona muchos pedidos; cada pedido tiene exactamente un responsable).  
**Participantes y cardinalidad:**  
- EMPLEADO `(1,N)` — GESTIONA — PEDIDO `(1,1)` (desde PEDIDO: `id_empleado_responsable` no nulo)  
**Atributos de la relación:** ninguno adicional (el FK `id_empleado_responsable` se almacena en PEDIDO).  
**Semántica:** Asociación obligatoria: cada pedido tiene un empleado responsable. Esta relación permite medir el cumplimiento de objetivos de venta por empleado.  
**Ejemplo:** EMPLEADO `501` responsable del PEDIDO `9001`.

---

### 6. PEDIDO — DETALLE_PEDIDO — PRODUCTO  
**Tipo:** relación N:M implementada mediante entidad asociativa DETALLE_PEDIDO.  
**Participantes y cardinalidad:**  
- PEDIDO `(1,N)` — DETALLE_PEDIDO — PRODUCTO `(1,N)`  
**Atributos en DETALLE_PEDIDO:** `cantidad`, `precio_unitario_linea`.  
**Semántica:** Describe las líneas de cada pedido (qué productos y cuántas unidades).  
**Ejemplo:** En PEDIDO `9001`, DETALLE: `id_producto=1001`, `cantidad=3`, `precio_unitario_linea=12.50`.

---

### 7. PRODUCTIVIDAD_ZONA — registra métricas por (VIVERO,ZONA,PERIODO)
**Tipo:** entidad de histórico (N:1 respecto a ZONA por periodo).  
**Participantes y cardinalidad:**  
- PRODUCTIVIDAD_ZONA `(N,1)` → ZONA `(1,1)` (varios registros históricos por una misma zona, uno por periodo)  
**Atributos:** `periodo`, `ventas_monetarias`, `unidades_vendidas`.  
**Semántica:** Almacena agregados para análisis y comparativas.

---

### 8. PRODUCTIVIDAD_EMPLEADO — registra métricas por (EMPLEADO,PERIODO)
**Tipo:** entidad de histórico (N:1 respecto a EMPLEADO por periodo).  
**Participantes y cardinalidad:**  
- PRODUCTIVIDAD_EMPLEADO `(N,1)` → EMPLEADO `(1,1)`  
**Atributos:** `periodo`, `ventas_monetarias`, `pedidos_gestionados`.  
**Semántica:** Almacena métricas para evaluación de objetivos.

---

## Claves primarias / foráneas (resumen)
- PK VIVERO: `id_vivero`.  
- PK ZONA (componente débil): (`id_vivero`, `nombre_zona`).  
- PK PRODUCTO: `id_producto`.  
- PK EMPLEADO: `id_empleado`.  
- PK CLIENTE: `id_cliente`.  
- PK PEDIDO: `id_pedido`.  
- PK DETALLE_PEDIDO: (`id_pedido`, `id_producto`).  
- PRODUCTIVIDAD_ZONA y PRODUCTIVIDAD_EMPLEADO pueden usar PK compuestas por (`id_vivero`,`nombre_zona`,`periodo`) y (`id_empleado`,`periodo`) respectivamente, o un id autonumérico.

**FKs importantes:**  
- ZONA.id_vivero → VIVERO.id_vivero.  
- PRODUCTIVIDAD_ZONA → ZONA (id_vivero + nombre_zona).  
- PEDIDO.id_cliente → CLIENTE.id_cliente.  
- PEDIDO.id_empleado_responsable → EMPLEADO.id_empleado.  
- DETALLE_PEDIDO.id_pedido → PEDIDO.id_pedido.  
- DETALLE_PEDIDO.id_producto → PRODUCTO.id_producto.  
- STOCK (si se implementa como tabla/entidad) FK a ZONA y PRODUCTO.

---

## Restricciones semánticas (reglas de negocio y restricciones adicionales)

1. **Entidad débil ZONA:** una zona sólo existe si existe su vivero contenedor; la PK real de la zona es la combinación `(id_vivero, nombre_zona)`.  
2. **Un empleado nunca tiene dos destinos simultáneos:** no deben existir dos registros de ASIGNACIÓN para el mismo `id_empleado` cuyos intervalos `[fecha_inicio, fecha_fin]` se solapen (salvo si `fecha_fin` es NULL y otro periodo vigente). Esta restricción requiere comprobación temporal al insertar/actualizar.  
3. **Pedido con responsable obligatorio:** `PEDIDO.id_empleado_responsable` es NOT NULL; cada pedido tiene exactamente un empleado responsable.  
4. **Stock coherente:** `STOCK.cantidad_disponible` ≥ 0. Al confirmar un pedido, las cantidades en DETALLE_PEDIDO deben restarse del stock correspondiente en las zonas afectadas; la política de depleción (desde qué zona) se define por reglas operativas (no modeladas aquí).  
5. **Pedidos para campañas Tajinaste Plus:** para campañas o cálculos basados en pertenencia `es_tajinaste_plus = true`, sólo se consideran los pedidos cuya `fecha` ≥ `fecha_ingreso_plus` del CLIENTE.  
6. **Integridad referencial:** no se puede eliminar un VIVERO si existen ZONAS asociadas (o en su lugar eliminar en cascada según política). Similar para otros FKs.  
7. **PKs y unicidad:** `id_*` deben ser únicos por entidad. DETALLE_PEDIDO no debe permitir duplicados `(id_pedido,id_producto)`; si se repite, sumar cantidades o impedir la inserción.  
8. **Formato y dominios:**  
   - Fechas en formato `YYYY-MM-DD`.  
   - Periodo en `YYYY-MM` para agregaciones mensuales.  
   - Coordenadas lat/long dentro de sus rangos.  
9. **Atributos derivados:** `bono_mensual` en CLIENTE es calculable desde `PEDIDO` agregados por periodo; puede ser almacenado para rendimiento, pero su valor debe poder recalcularse.

---

## Ejemplos ilustrativos rápidos
- **Stock:** `(id_vivero=1, nombre_zona="Zona Exterior", id_producto=1001) -> cantidad_disponible=12, fecha_ultima_actualizacion=2024-05-10`.  
- **Asignación:** `(id_empleado=501, id_vivero=1, nombre_zona="Almacén") -> fecha_inicio=2024-03-01, fecha_fin=2024-04-30, puesto="Operario"`.  
- **Pedido y detalle:** `PEDIDO id=9001, id_cliente=200, id_empleado_responsable=501, fecha=2024-05-12, total=78.40`. DETALLE: `(9001,1001,cantidad=3,precio_unitario_linea=12.50)`.

---

## Notas finales
- El modelo aplica **única y exclusivamente** los conceptos del modelo Entidad-Relación (entidad, atributo, relación, entidad débil, atributos de relación, cardinalidad y participación, y registro histórico mediante relaciones con atributos temporales).  
- Para pasar del esquema E/R a la implementación relacional conviene transformar: entidades → tablas; entidades débiles → tabla con PK compuesta; relaciones N:M con atributos → tablas intermedias (ej. STOCK, DETALLE_PEDIDO); relaciones 1:N → FK en la tabla del lado N (ej. PEDIDO → FK a CLIENTE y EMPLEADO).

---
