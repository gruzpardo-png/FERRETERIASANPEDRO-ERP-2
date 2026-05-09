# Ferretería Cloud Tool - v4.2 Data Safe

Versión actualizada para subir a Render sin perder datos anteriores.

## Cambios principales

- Despacho rápido para operadores.
- Estados de despacho operativos:
  - Entregado por defecto.
  - Pendiente.
- Dashboard solo administradores.
- Administración / Configuración solo administradores.
- Usuarios y permisos.
- Maquinarias.
- Vehículos / patentes.
- Conductores / pionetas.
- Mantenciones.
- Auditoría.
- Exportación Excel.
- Backup completo de base de datos desde el sistema.
- Respaldo automático antes de migraciones.

## Regla importante de actualización

Esta versión está preparada para actualizar sin borrar datos, siempre que la base SQLite esté en un Disk persistente de Render.

La variable debe ser:

```txt
DATABASE_PATH=/data/ferreteria_cloud_tool.db
```

Y el servicio debe tener Disk:

```txt
Mount path: /data
```

Si la base queda dentro del contenedor normal, por ejemplo `ferreteria_cloud_tool.db` sin `/data`, Render puede reemplazarla en cada deploy.

## Qué NO hace esta versión

- No usa `DROP TABLE`.
- No borra tablas.
- No borra usuarios.
- No borra despachos.
- No borra maquinarias.
- No borra mantenciones.
- No reinicia la configuración.
- No reemplaza la base existente.

## Qué SÍ hace esta versión

- Usa `CREATE TABLE IF NOT EXISTS`.
- Usa `ALTER TABLE ADD COLUMN` solo si falta una columna.
- Crea respaldo automático antes de migrar.
- Permite descargar backup manual desde:
  - Exportar Excel / Reportes > Backup base de datos.

## Usuarios iniciales

Solo se crean si no existen.

```txt
Admin:
usuario: admin
clave: admin123

Operador:
usuario: operador
clave: operador123
```

Si esos usuarios ya existen en tu base, no se reemplazan.

## Antes de subir una actualización

1. Entra como admin.
2. Ve a Exportar Excel / Reportes.
3. Descarga `Backup base de datos`.
4. Sube el nuevo código.
5. Verifica que `DATABASE_PATH` siga siendo `/data/ferreteria_cloud_tool.db`.
6. Verifica que el Disk siga montado en `/data`.

## Render

El archivo `render.yaml` ya viene configurado con:

```yaml
DATABASE_PATH: /data/ferreteria_cloud_tool.db
disk:
  mountPath: /data
```

Si ya tienes un servicio creado en Render, revisa manualmente que el Disk esté agregado.


## v4.4 Ventas IA Elias

Incluye:

- Módulo Ventas / Cotización IA.
- Asistente de ventas Elias con OpenAI.
- Entrada por texto y por imagen.
- Importador Excel de maestra de productos.
- Cálculo de:
  - venta bruta
  - venta neta estimada
  - precio compra neto
  - contribución en pesos
  - margen porcentual
  - stock disponible
- Registro de última actualización de productos.
- Exportación de cotización a Excel.
- Hora del sistema configurada para Chile (`America/Santiago`).

### Variables de entorno en Render

```txt
OPENAI_API_KEY=tu_api_key
OPENAI_MODEL=gpt-5.4-mini
IVA_RATE=0.19
DATABASE_PATH=/data/ferreteria_cloud_tool.db
```

Si tu cuenta no tiene acceso a `gpt-5.4-mini`, cambia `OPENAI_MODEL` por el modelo disponible en tu panel de OpenAI.

### Columnas esperadas para importar productos

```txt
Código Producto
Descripción
Precio Compra Neto
Precio Venta Bruto
Stock
Activo
```

También soporta planillas con más columnas, como la exportación de Facturación.cl/ERP.


## v4.4.1 Disk Safe

Corrige diagnóstico de SQLite en Render.

Si ves:
sqlite3.OperationalError: unable to open database file

Revisa:
DATABASE_PATH=/data/ferreteria_cloud_tool.db
Disk Mount Path=/data

Si usas otro mount path, por ejemplo /var/data:
DATABASE_PATH=/var/data/ferreteria_cloud_tool.db

Ruta admin de diagnóstico:
https://tu-dominio/debug-db


## v4.5 Ventas Chat Elias

Cambios:
- Elias ahora funciona como chat conversacional para vendedores.
- La cotización NO se genera automáticamente.
- El vendedor conversa con Elias, adjunta imagen o pega lista, y luego presiona "Generar cotización".
- Matching de productos más estricto para evitar productos equivocados.
- Las coincidencias dudosas quedan como REVISAR y no se suman al total.
- Agrega tablas ventas_chat_sesiones y ventas_chat_mensajes.
- No borra base de datos ni tablas existentes.


## v4.5.1 Fix OpenAI

Corrige el problema de despliegue cuando Render no instala la librería OpenAI.

`requirements.txt` debe contener:

```txt
openai>=1.100.0
```

Después de subir esta versión, en Render ejecutar:

```txt
Manual Deploy -> Clear build cache & deploy
```

Variables necesarias:

```txt
OPENAI_API_KEY=tu_api_key
OPENAI_MODEL=gpt-5.4-mini
DATABASE_PATH=/data/ferreteria_cloud_tool.db
IVA_RATE=0.19
```


## v4.6 Chat Ventas Integrado

- Conversación de Elias rediseñada como chat moderno.
- Los mensajes se muestran en orden natural y el chat baja automáticamente al último mensaje.
- La cotización generada aparece dentro del mismo chat como tarjeta comercial.
- Desde la tarjeta se puede abrir detalle, descargar PDF o exportar Excel.
- El vendedor puede seguir conversando después de generar la cotización.
- Agrega exportación PDF con formato comercial básico.
- Matching estricto: líneas dudosas quedan como REVISAR y no se suman al total.


## v4.7 Simplificado Operación

Cambios principales:
- Se elimina del menú la sección Integraciones y el acceso a Facturación.cl.
- Exportar Excel pasa a Control.
- Administración se simplifica:
  - Usuarios
  - Maquinarias / Vehículos en una sola pestaña
  - Conductores
- Se elimina duplicidad de Productos dentro de Administración; queda en Ventas.
- Despachos mantiene formulario rápido en dos filas:
  - Fila 1: documento, tipo, estado, monto.
  - Fila 2: patente, chofer, pioneta, observación.
- Consulta y exportación de despachos incluyen patente, chofer, pioneta y observación.
- No borra base de datos ni tablas existentes.


## v4.7.1 Login Brand

Cambio visual menor:
- En el login se reemplaza "RUZ AI Systems" por "RUZ Technology company".
- No modifica estructura ni base de datos.
- No borra datos.


## v4.8 Cotizador PDF

Nuevo módulo en Ventas: Cotizador.

Funciones:
- Importar PDF de cotización.
- Extraer Código, Descripción, Cantidad, Precio Unitario bruto y Valor.
- Cruzar Código Producto contra maestra de productos para obtener Costo Neto y Stock.
- Calcular margen de producto según: PV bruto / (Costo Neto * 1.19) - 1.
- Calcular contribución: PV bruto / 1.19 - Costo Neto.
- Tabla tipo Excel editable por vendedor.
- Descuento general.
- Margen objetivo general sobre costo.
- Cálculo de margen y contribución nuevo.
- Guardar cambios y exportar Excel.

No borra base de datos. Solo agrega tablas cotizador_sesiones y cotizador_items.


## v4.9 Cotizador Corrección Totales

Correcciones del Cotizador:
- El total bruto nuevo ahora parte igual al total bruto oficial del PDF.
- Si el PDF trae ajuste global/retención/descuento, se aplica proporcionalmente a los precios finales iniciales.
- Se agrega herramienta "Venta bruta final objetivo ($)" para fijar el total final, por ejemplo 6.000.000.
- Se agrega herramienta "Contribución total objetivo ($)" para fijar la ganancia total esperada.
- Se reemplaza "Margen objetivo sobre costo" por controles de venta bruta final y contribución objetivo.
- La columna queda como "Contrib. total línea" y usa la fórmula:
  cantidad * ((precio venta bruto final / 1,19) - costo neto).
- No borra datos ni tablas existentes.


## v4.10 Cotizador PDF y Eliminación

Cambios:
- Desde Últimos análisis se agrega botón PDF por cada cotización.
- Admin puede eliminar análisis del Cotizador desde Últimos análisis.
- En el interior del Cotizador se agregan botones:
  - Generar PDF
  - Generar PDF y guardar
- "Generar PDF" usa los últimos datos guardados.
- "Generar PDF y guardar" guarda los cambios del tablero y luego genera el PDF.
- Operadores con permiso Ventas pueden generar PDF.
- Solo administradores pueden eliminar análisis.


## v4.11 PDF Logo San Pedro

Cambios en PDF del Cotizador:
- Se reemplaza cabecera "SOCOM RUZ SPA" por marca comercial "Ferreteria San Pedro".
- Se agrega logo de Ferretería San Pedro en esquina superior izquierda.
- Se agrega bajo la marca: "Socom Ruz Spa · R.U.T. 77.351.584-0".
- Se agrega parámetro editable "Validez oferta (días)" en el Cotizador.
- Valor predeterminado: 2 días.
- El PDF usa la validez configurada por el usuario.
- Condiciones comerciales:
  1.- Validez de la oferta: X días.
  2.- Plazo de entrega: sujeto a disponibilidad de stock.
- No borra datos.


## v4.12 Superior Cotizador

Cambios:
- En Cotizador > Últimos análisis se agregan columnas:
  - Total nuevo
  - Contribución nueva
  - Margen nuevo
  entre Margen actual y Fecha.
- La tabla de Últimos análisis queda más compacta, con tipografía más pequeña y columnas ajustadas.
- Se crea permiso superior exclusivo:
  - Solo el usuario `gus` puede eliminar análisis del Cotizador.
  - El administrador no hereda esta atribución.
- El usuario `gus` tiene acceso superior incluso si no se marca como admin.
- La hoja de permisos se amplía con permisos sugeridos:
  - Cotizador PDF
  - Cotizador PDF/Excel
  - Cotizador precios/descuentos
  - Productos ver/importar
  - Despachos crear/editar
  - Backup base de datos
- No borra datos ni tablas existentes.


## v4.13 Costo Total Cotizador

Cambios:
- Agrega variable "Costo total venta" en el panel interno del Cotizador.
- Costo total venta = suma de cantidad * costo neto de todos los productos.
- En Últimos análisis del Cotizador se agrega la columna "Costo total" entre Margen nuevo y Fecha.
- El costo total se recalcula al importar PDF, guardar cambios, abrir detalle y cargar Últimos análisis.
- No borra datos ni tablas existentes.


## v4.14 Costo Bruto y Vendedor

Cambios:
- El costo total ahora se muestra como costo total bruto: costo neto total * 1,19.
- Dentro del Cotizador, el panel de simulación muestra "Costo total bruto".
- En Últimos análisis, la columna queda como "Costo total bruto".
- En Últimos análisis se agrega columna "Vendedor" después de Cliente y antes de Contacto.
- La tabla compacta se ajusta para mantener todo en una fila armónica.
- La base conserva el costo neto internamente para cálculos; la visualización muestra costo bruto para evitar confusión.
- No borra datos.


## v4.15 Despacho Único y PDF Brand

Cambios críticos:
- Despachos ahora bloquea ingreso duplicado por Tipo documento + Número documento.
- Si el documento ya existe, muestra alerta crítica grande y no permite guardar.
- Registra el intento duplicado en auditoría.
- Agrega índice no único para búsqueda rápida por tipo y número de documento.
- PDF de Cotizador agrega bloque de direcciones:
  - Sucursal 1: Av. 21 de Mayo 73, Quillota.
  - Sucursal 2: Av. 21 de Mayo 420, Quillota.
- PDF agrega WhatsApp:
  - +56 9 3563 0950
  - +56 9 4907 3615
- Agrega línea de contacto en el pie del PDF.
- No borra datos.
