# Introducción
El proyecto busca modelar la operación de un comercio dedicado a la compra y venta de repuestos de karting. La base de datos permitirá organizar el catálogo de piezas, gestionar clientes y proveedores, registrar pedidos y pagos, controlar stock en depósitos y dar trazabilidad a cada venta.
# Objetivo
    • Centralizar la información de productos de karting (motores, neumáticos, chasis, accesorios). 
    • Facilitar la gestión de pedidos y facturación. 
    • Controlar inventario y reposición de repuestos críticos. 
    • Permitir análisis de ventas por tipo de repuesto, cliente y temporada de carreras. 
# Situación problemática
Actualmente, muchos comercios pequeños de karting llevan su gestión en planillas dispersas o sistemas genéricos que no contemplan las particularidades del rubro (ej. compatibilidad de piezas con modelos de chasis, control de stock de neumáticos por medida). Esto genera errores de inventario y dificultades para responder rápido a clientes. La base de datos propuesta soluciona estas brechas con un esquema especializado.
Modelo de negocio
# La organización es un comercio minorista de repuestos de karting que:
    • Compra piezas a proveedores nacionales e internacionales. 
    • Vende a clientes finales pilotos, equipos y talleres. 
    • Gestiona stock en depósitos. 
    • Procesa pagos y emite facturas. 
    • Coordina envíos a distintas provincias. 
# Listado de Tablas:
* Categorias
  * Catálogo de categorías de productos.
* Proveedores
    * Información de proveedores (razón social, CUIT, contacto).
* Productos
    * Catálogo de productos con referencia a categoría y proveedor.
* Clientes
  * Datos de clientes (nombre, email, teléfono, dirección).
* Ordenes
  * Órdenes de compra realizadas por clientes.
* Lineas_orden
  * Detalle de productos dentro de cada orden.
* Pagos
  * Registro de pagos asociados a órdenes.
* Facturas
  * Facturas emitidas para las órdenes.
* Envios
  * Información de envíos asociados a órdenes.
* Eventos_envio
  * Eventos de seguimiento logístico de cada envío.
 Para ver el contenido de las tablas puede hacerlo desde este link [Lista de Tablas](https://scanntech365-my.sharepoint.com/:x:/g/personal/horacio_buyo_scanntech_com/IQDoIRBsYImKTKiKgJQnRcOIARxqp4KeJKX6Dg6N7Gsedok?e=1dcaUg).

# Vistas
## vista_productos_detalle
  * Descripción: Muestra el catálogo de productos con su categoría y proveedor.
  * Objetivo: Facilitar la consulta de información completa de cada producto para ventas y stock.
  * Tablas: productos, categorias, proveedores.

## vista_clientes_ordenes
  * Descripción: Lista de clientes junto con sus órdenes realizadas.
  * Objetivo: Consultar historial de compras por cliente.
  * Tablas: clientes, ordenes.

## vista_ordenes_detalle
  * Descripción: Detalle completo de cada orden con sus líneas de productos.
  * Objetivo: Reporte de ventas y control de facturación.
  * Tablas: ordenes, lineas_orden, productos.
    
## vista_pagos_facturas
 * Descripción: Relación entre pagos y facturas emitidas por cada orden.
 * Objetivo: Control financiero y conciliación de ingresos.
 * Tablas: pagos, facturas, ordenes.

## vista_envios_eventos
 * Descripción: Información de envíos y sus eventos asociados.
 * Objetivo: Seguimiento logístico y estado de entregas.
 * Tablas: envios, eventos_envio.

# Funciones
## fn_calcular_subtotal
  * Descripción: Calcula el subtotal de una línea de orden multiplicando cantidad por precio unitario y restando descuento.
  * Objetivo: Automatizar el cálculo de subtotales en la tabla lineas_orden.
  * Tablas/Datos: lineas_orden (campos cantidad, precio_unitario, descuento)

## fn_calcular_total_orden
  * Descripción: Suma los subtotales de todas las líneas de una orden para obtener el total bruto.
  * Objetivo: Facilitar el cálculo del total de cada orden.
  * Tablas/Datos: ordenes, lineas_orden.

## fn_estado_pago
  * Descripción: Devuelve el estado del pago de una orden (Pagado, Pendiente, Parcial).
  * Objetivo: Consultar rápidamente la situación financiera de una orden.
  * ablas/Datos: pagos, ordenes.

## fn_cliente_activo
  * Descripción: Verifica si un cliente está activo en el sistema.
  * Objetivo: Validar clientes antes de generar órdenes o facturas.
  * Tablas/Datos: clientes.

## fn_calcular_stock_producto
  * Descripción: Calcula el stock disponible de un producto restando las cantidades vendidas de las ingresadas.
  * Objetivo: Controlar inventario y disponibilidad de productos.
  * Tablas/Datos: productos, lineas_orden (ventas).

# Stored Procedures
## sp_crear_orden
  * Descripción: Inserta una nueva orden para un cliente, inicializando totales en cero.
  * Objetivo/Beneficio: Centralizar la creación de órdenes, asegurando que siempre se registren con estado inicial y valores consistentes.
  * Tablas involucradas: ordenes, clientes.
    
## sp_agregar_linea_orden
  * Descripción: Agrega una línea de producto a una orden y recalcula los totales.
  * Objetivo/Beneficio: Automatizar la actualización de totales al añadir productos, evitando cálculos manuales.
  * Tablas involucradas: lineas_orden, ordenes, productos.

## sp_agregar_linea_orden
  * Descripción: Agrega una línea de producto a una orden y recalcula los totales.
  * Objetivo/Beneficio: Automatizar la actualización de totales al añadir productos, evitando cálculos manuales.
  * Tablas involucradas: lineas_orden, ordenes, productos.

## sp_emitir_factura
  * Descripción: Genera una factura para una orden ya pagada.
  * Objetivo: Automatizar la emisión de facturas con consistencia en montos y estado.
  * Tablas: facturas, ordenes.

## sp_registrar_envio
  * Descripción: Crea un registro de envío para una orden.
  * Objetivo: Integrar la logística al flujo de órdenes con seguimiento desde despacho hasta entrega.
  * Tablas: envios, ordenes.

# Trigger
## Trigger 1
    * Descripción: Actualizar stock al insertar una línea de orden
    * Objetivo: Este trigger descuenta automáticamente del stock inicial cada vez que se agrega una línea de orden. 
## Trigger 2
    * Descripción: Cambiar estado de orden al registrar pago
    * Objetivo: Este trigger actualiza el estado de la orden a “pagada” cuando se inserta un pago que cubre el total.
