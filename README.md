# Modelo Entidad-Relación (Tajinaste S.A.) 

---

## Entidades

### Vivero  
**Descripción:** punto de venta/almacén físico.  
**Atributos:**  
- `Código vivero`: entero, atributo identificador. Ejemplo: `1`.  
- `Nombre`: cadena. Ejemplo.: `"Vivero Norte"`.  
- `Dirección`: cadena. Ejemplo: `"C/ Flores 10, Santa Cruz"`.  
- `Latitud`: decimal. Ejemplo: `28.4636`.  
- `Longitud`: decimal. Ejemplo: `-16.2518`.

---

### Zona (entidad débil de Vivero)  
**Descripción:** área dentro del vivero (p. ej. zona exterior, almacén). Depende de Vivero.  
**Atributos:**
- `Código zona`: entero, atributo identificador. Ejemplo: `1`.  
- `Nombre zona`: cadena. Ejemplo: `"Zona Exterior"`.  
- `Descripción`: cadena. Ejemplo: `"Exposición de arbustos"`.  
- `Latitud`: decimal. Ejemplo: `28.4637`.  
- `Longitud`: decimal. Ejemplo: `-16.2519`.

---

### Producto  
**Descripción:** artículo vendible.    
**Atributos:**  
- `Código producto`: entero, atributo identificador. Ej.: `1001`.  
- `nombre`: cadena. Ejemplo: `"Rosal 'Red Eden'"`.  
- `descripcion`: cadena. Ejemplo: `"Rosal trepador, 40cm"`.  
- `tipo`: cadena, atributo multivaluado (un producto puede pertenecer a múltiples categorías). Ejemplo: `"Planta"`.  
- `precio_unitario`: decimal. Ej.: `12.50`.

---

### Empleado  
**Descripción:** trabajador que puede ser destinado a zonas y gestionar pedidos.
**Atributos:**  
- `Código empleado`: entero, atributo identificador. Ejemplo: `501`.  
- `Nombre`: cadena. Ejemplo: `"María"`.  
- `Apellidos`: cadena. Ejemplo: `"González Pérez"`.  
- `DNI`: cadena. Ejemplo: `"12345678A"`.  
- `Fecha Contratación`: fecha. Ejemplo: `2020-06-01`.

---

### Cliente  
**Descripción:** comprador; puede pertenecer a Tajinaste Plus.  
**Atributos:**  
- `Código cliente`: entero, atributo identificador. Ejemplo: `200`.  
- `Nombre`, `Apellidos`: cadena. Ejemplo: `"Luis Martínez"`.  
- `Email`: cadena, atributo multivaluado (un cliente puede tener vinculados múltiples emails). Ejemplo: `"luis@example.com"`.  
- `Teléfono`: cadena, atributo multivaluado (un cliente puede tener vinculados múltiples teléfonos). Ejemplo: `"+34 600 123 456"`.  
- `Es Tajinaste Plus`: booleano. Ejemplo: `true`.  
- `Fecha ingreso Plus`: fecha. Ejemplo: `2023-02-15` o `NULL`.  
- `Bono mensual`: decimal, atributo calculado. Ejemplo: `5.00`.

---

### Pedido  
**Descripción:** orden de compra.  
**Atributos:**  
- `Código pedido`: entero, atributo identificador. Ejemplo: `9001`.  
- `Fecha`: fecha. Ejemplo: `2024-05-12`.  
- `Total`: decimal. Ejemplo: `78.40`.  

---

### Productividad zona (entidad débil de Zona)  
**Descripción:** registro histórico mensual de una zona.   
**Atributos:**
- `Código productividad zona`: cadena, atributo identificador. Ejemplo: `UFZ2`.
- `Periodo`: fechas. Atributo compuesto (fecha inicio, fecha fin). Ejemplo: `2024-05, 2024-07`.  
- `Ventas monetarias`: decimal. Ejemplo: `1540.75`.  
- `Unidades vendidas`: entero. Ejemplo: `320`.  

---

### Productividad empleado (entidad débil de Empleado)  
**Descripción:** registro histórico mensual de un empleado.  
**Atributos:**  
- `Código productividad empleado`: cadena, atributo identificador. Ejemplo: `5FH4`.  
- `Periodo`: fechas. Atributo compuesto (fecha inicio, fecha fin). Ejemplo: `"2024-05, 2024-07"`.  
- `Ventas monetarias`: decimal. Ejemplo: `1200.00`.  
- `Pedidos gestionados`: entero. Ejemplo: `18`. 

---

## Relaciones

1. **Vivero - tiene - Zona: relación identificadora**
- Cardinalidad: 1:N
- Justificación: una zona pertenece a un único vivero, un vivero tiene una o más zonas.
- Atributos de relación: ninguno.

2. **Zona - asigna (stock) - Producto: relación con atributos**
- Atributos:  
  - `Cantidad disponible`: entero. Ejemplo: `25`.  
  - `Fecha última actualización`: fecha. Ejemplo: `2024-05-10`.
- Cardinalidad: N:M
- Justificación: cada producto puede estar asignado en 0 o más zonas, cada zona puede tener asignado 0 o más productos.

3. **Empleado - asignación - Zona: relación con atributos**   
- Atributos:
  - `Puesto`: cadena. Ejemplo: `supervisor`.  
  - `Fecha inicio`: fecha. Ejemplo: `2024-05-10`.
  - `Fecha fin`: fecha. Ejemplo: `2024-05-10`.
- Cardinalidad: N:M
- Justificación: Cada asignación liga exactamente 1 Empleado y 1 Zona. Historial permitido. Restricción semántica: un empleado no puede tener asignaciones con intervalos solapados (fecha_inicio/fecha_fin).

4. **Cliente - hace - Pedido**
- Cardinalidad: 1:N
- Justificación: un cliente puede haber hecho ente 0 y N pedidos, un pedido es realizado por un único cliente.

5. **Empleado - gestiona - Pedido**  
- Cardinalidad: 1:N
- Justificación: un empleado puede no gestionar pedidos, gestionar uno o múltiples de ellos. Cada pedido tiene exactamente un responsable.

6. **Pedido - incluye - Producto: relación con atributos**
- Cardinalidad: N:M
- Atributos:
  - `Precio unitario`: decimal. Ejemplo: `57.25`.  
  - `Cantidad`: entero. Ejemplo: `37`. 
- Justificación: un pedido incluye uno o más productos. Un producto puede no estar incluido en ningún pedido, estarlo en uno o múltiple de ellos.

7. **Zona - se evalua - Productividad zona: relación identificadora**
- Cardinalidad: 1:N
- Justificación: una zona puede no tener registros aún o tener múltiples de ellos. Cada registro pertenece a una única zona.

8. **Empleado - se evalua — Productividad empleado: relación identificadora** 
- Cardinalidad: 1:N
- Justificación: un empleado puede no tener registros aún o tener múltiples de ellos. Cada registro pertenece a un único empleado.

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
