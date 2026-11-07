# AQUACONTROL — Revisión técnica v2.1

**Base:** AQUACONTROL_iPhone.html.  **Alcance:** prototipo HTML autónomo, navegador local, interfaz adaptada a móviles.

## Cambios implementados
1. Motor de comprobación de integridad: identificadores duplicados, productos y clientes referenciados, totales de ventas frente a partidas, consistencia de cobros, saldo de botellones por cliente, vínculos de lotes, movimientos y productos.
2. Se valida el motor de integridad antes de cada persistencia y al restaurar datos. Los respaldos incompatibles o inconsistentes se rechazan sin reemplazar datos vigentes.
3. Doble control de materiales dentro de la transacción de producción, y existencia suficiente dentro de ajustes de inventario.
4. En ventas se comprueba de nuevo stock, presentación y precio; también saldo retornable previo a registrar el movimiento.
5. En retornos se vuelve a comprobar el saldo por cliente antes de actualizar inventario.
6. No se permite cambiar el formato de un producto que tenga stock o movimientos históricos; debe crearse otro SKU.
7. Ventas conservan SKU por partida y exportan moneda histórica por operación.
8. Nuevo apartado **Diagnóstico técnico** en Configuración y descarga de informe TXT.

## Pruebas realizadas
- `node --check`: sintaxis JavaScript correcta.
- Test aislado de lógica: estado inicial sin alertas; persistencia de movimiento de producción; rechazo de venta con total incorrecto; rechazo de retorno superior al saldo del cliente.
- Limitación: el entorno de Chromium bloqueó las navegaciones del test automatizado, por lo que **no se acredita prueba de representación en navegador ni en Safari/iPhone físico**.

## Límites importantes para producción real
- No existe servidor, cuentas ni autorización por roles. Un usuario con acceso al dispositivo puede modificar los datos locales.
- localStorage no soporta operaciones transaccionales robustas ni sincronización confiable entre usuarios, dispositivos o pestañas. La comprobación de revisión minimiza algunos conflictos, pero no elimina carreras.
- Los respaldos son manuales, no están cifrados ni centralizados. En iOS el almacenamiento del navegador puede ser eliminado por el sistema o el usuario.
- Los controles de pH y TDS son registros informativos sin reglas de liberación aprobadas por laboratorio, calibración, firma ni gestión normativa.
- No existen factura tributaria, gestión de impuestos, integración de pagos ni trazabilidad de distribución/rutas.

## Próxima arquitectura sugerida
- Cloudflare Pages para frontend; Supabase/PostgreSQL para estado centralizado; Auth y RLS, roles (administrador, producción, almacén, vendedor, reparto), funciones RPC transaccionales para ventas, consumo de materiales, lotes, devoluciones y cobranzas.
- Registro de eventos append-only y auditoría por actor, timestamp, entidad, antes/después; copias automáticas verificadas y monitoreo.
- Gestión de lotes con estado borrador / en análisis / liberado / rechazado; producto terminado solo a partir de lote liberado por autorizado.
- QA: unitarias, integración, prueba de concurrencia, tests E2E en Safari iPhone y Android, recuperación ante fallos y control de permisos.

**No publicar esta versión como sistema empresarial multiusuario de producción sin resolver los pendientes.** Puede utilizarse para prototipado y pruebas locales con respaldos.
