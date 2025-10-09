# Modelo Entidad-Relación (Tajinaste S.A.) 

---

## Entidades

### Vivero  
**Descripción:** punto de venta/almacén físico.  
**Atributos:**  
- `Código vivero`: entero, atributo identificador. Ejemplo: `1`.  
- `Nombre`: cadena. Ejemplo.: `"Vivero Norte"`.  
- `Dirección`: cadena, atributo compuesto (Calle, Nombre y Número, las tres cadenas). Ejemplo: `"C/ Flores 10, Santa Cruz"`.  
- `Latitud`: decimal. Ejemplo: `28.4636`.  
- `Longitud`: decimal. Ejemplo: `-16.2518`.

---

### Zona (entidad débil de Vivero)  
**Descripción:** área dentro del vivero (zona exterior, almacén, etc.). Depende de Vivero.  
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
- `Periodo`: fechas. Atributo compuesto (Fecha inicio y Fecha fin, las dos fechas). Ejemplo: `2024-05, 2024-07`.
- `Ventas monetarias`: decimal. Ejemplo: `1540.75`.  
- `Unidades vendidas`: entero. Ejemplo: `320`.  

---

### Productividad empleado (entidad débil de Empleado)  
**Descripción:** registro histórico mensual de un empleado.  
**Atributos:**  
- `Código productividad empleado`: cadena, atributo identificador. Ejemplo: `5FH4`.  
- `Periodo`: fechas. Atributo compuesto (Fecha inicio y Fecha fin, las dos fechas). Ejemplo: `"2024-05, 2024-07"`.  
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

# Restricciones semánticas

1. **Zona es entidad débil**: una Zona no existe fuera de un Vivero.

2. **Productividad es histórica y depende del "padre"**  
   - Regla: cada registro de productividad pertenece a una única Zona o Empleado y se identifica por el periodo. 
   - Aplicación: no permitir registros huérfanos.

3. **Stock único por (Vivero, Zona, Producto) y no negativo**  
   - Regla: existir a lo sumo una entrada de stock por combinación y `cantidad >= 0`.  

4. **Detalle de pedido único por (Pedido, Producto)**  
   - Regla: cada línea de pedido es única. Si se reingresa el mismo producto se actualiza la cantidad.  
   - Ejemplo: un pedido no puede tener dos filas separadas del mismo producto.  

5. **Empleado: un único destino a la vez (no solapamiento)**  
   - Regla: no pueden existir dos asignaciones para el mismo empleado con intervalos que se solapan..  
   - Ejemplo: si A cubre 2024-05-01 -> no puede crearse otra con inicio dentro de ese intervalo.  

6. **Pedido tiene responsable obligatorio**  
   - Regla: todo Pedido debe tener un empleado responsable.  

7. **Responsable debe estar asignado en la fecha del pedido**  
   - Regla: el empleado responsable del pedido debe tener una asignación cuyo intervalo cubra la `fecha` del pedido (y al menos pertenecer al mismo vivero si aplica).  
   - Ejemplo: si el pedido es 2024-06-10, la asignación del empleado debe incluir esa fecha.

8. **Consistencia del total del pedido**  
   - Regla: `Pedido.Total` debe reflejar la suma de sus líneas (cantidad × precio_unitario).  
   - Ejemplo: si las líneas suman 78.40, el total no puede ser 80.00.

9. **Confirmación de pedido requiere stock suficiente**  
   - Regla: no confirmar/servir un pedido si la/s zona/s seleccionadas no tienen suficiente stock.  
   - Ejemplo: stock en zona A = 10, pedido pide 12 -> bloqueo o desvío.  

10. **Pedidos para Tajinaste Plus**  
    - Regla: para análisis/campañas sólo contar pedidos con `Fecha >= Fecha ingreso Plus` y para clientes con `Es Tajinaste Plus = TRUE`.  
    - Ejemplo: cliente entró 2024-03-15 -> sólo pedidos desde esa fecha se consideran.

11. **Dominios válidos y orden temporal**  
    - Regla: latitud ∈ [-90,90], longitud ∈ [-180,180]. `Fecha fin` es NULL o ≥ `Fecha inicio`. Precios/Cantidades ≥ 0. Fechas con formato ISO.  
    - Ejemplo: latitud 95.0 -> inválida.

12. **Identificadores únicos y normalización de multivaluados**  
    - Regla: DNI único por empleado. Normalizar emails/teléfonos a tablas relacionadas (un email/telefono por fila).  
    - Ejemplo: no aceptar dos empleados con mismo DNI.
