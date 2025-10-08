# Modelo Entidad-Relación (Tajinaste S.A.) 

---

## Entidades

### Vivero  
**Descripción:** punto de venta/almacén físico.  
**Atributos:**  
- `id_vivero`: entero, atributo identificador. Ejemplo: `1`.  
- `nombre`: cadena. Ejemplo.: `"Vivero Norte"`.  
- `direccion`: cadena. Ejemplo: `"C/ Flores 10, Santa Cruz"`.  
- `latitud`: decimal. Ejemplo: `28.4636`.  
- `longitud`: decimal. Ejemplo: `-16.2518`.

---

### Zona (entidad débil de Vivero)  
**Descripción:** área dentro del vivero (p. ej. zona exterior, almacén). Depende de Vivero.  
**Atributos:**  
- `id_zona`: entero. Formado a partir del id_vivero (clave foránea) y codigo_zona.
- `id_vivero`: entero. Ejemplo: `1`.  
- `codigo_zona`: cadena. Ejemplo: `"ZEX"`.  
- `nombre_zona`: cadena. Ejemplo: `"Zona Exterior"`.  
- `descripcion`: cadena. Ejemplo: `"Exposición de arbustos"`.  
- `latitud`: decimal. Ejemplo: `28.4637`.  
- `longitud`: decimal. Ejemplo: `-16.2519`.

---

### Producto  
**Descripción:** artículo vendible.    
**Atributos:**  
- `id_producto`: entero, atributo identificador. Ej.: `1001`.  
- `nombre`: cadena. Ejemplo: `"Rosal 'Red Eden'"`.  
- `descripcion`: cadena. Ejemplo: `"Rosal trepador, 40cm"`.  
- `tipo`: cadena. Ejemplo: `"Planta"`.  
- `precio_unitario`: decimal. Ej.: `12.50`.

---

### Empleado  
**Descripción:** trabajador que puede ser destinado a zonas y gestionar pedidos.
**Atributos:**  
- `id_empleado`: entero, atributo identificador. Ejemplo: `501`.  
- `nombre`: cadena. Ejemplo: `"María"`.  
- `apellidos`: cadena. Ejemplo: `"González Pérez"`.  
- `dni`: cadena. Ejemplo: `"12345678A"`.  
- `fecha_contratacion`: fecha. Ejemplo: `2020-06-01`.

---

### Cliente  
**Descripción:** comprador; puede pertenecer a Tajinaste Plus.  
**Atributos:**  
- `id_cliente`: entero, atributo identificador. Ejemplo: `200`.  
- `nombre`, `apellidos`: cadena. Ejemplo: `"Luis Martínez"`.  
- `email`: cadena. Ejemplo: `"luis@example.com"`.  
- `telefono`: cadena. Ejemplo: `"+34 600 123 456"`.  
- `es_tajinaste_plus`: booleano. Ejemplo: `true`.  
- `fecha_ingreso_plus`: fecha. Ejemplo: `2023-02-15` o `NULL`.  
- `bono_mensual`: decimal. Ejemplo: `5.00`.

---

### Pedido  
**Descripción:** orden de compra.  
**Atributos:**  
- `id_pedido`: entero, atributo identificador. Ejemplo: `9001`.  
- `id_cliente`: entero. Ejemplo: `200`.  
- `id_empleado_responsable`: entero. Ejemplo: `501`.  
- `fecha`: fecha. Ejemplo: `2024-05-12`.  
- `total`: decimal. Ejemplo: `78.40`.  

---

### Productividad zona (entidad débil de Zona)  
**Descripción:** registro histórico mensual de una zona.   
**Atributos:**
- `id_prod_zona`: entero, atributo identificador. Ejemplo: `9001`.  
- `periodo`: cadena. Ejemplo: `"2024-05"`.  
- `ventas_monetarias`: decimal. Ejemplo: `1540.75`.  
- `unidades_vendidas`: entero. Ejemplo: `320`.  

---

### Productividad empleado (entidad débil de Empleado)  
**Descripción:** registro histórico mensual de un empleado.  
**Atributos:**  
- `id_prod_empleado`: entero, atributo identificador. Ejemplo: `9001`.  
- `periodo`: fecha. Ejemplo: `"2024-05"`.  
- `ventas_monetarias`: decimal. Ejemplo: `1200.00`.  
- `pedidos_gestionados`: entero. Ejemplo: `18`. 

---

## Relaciones

1. **Vivero - contiene - Zona**  
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
