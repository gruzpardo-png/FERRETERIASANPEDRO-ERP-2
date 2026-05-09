# Ferretería Cloud ERP v1.0 Render MVP

Aplicación base para iniciar el ERP propio de Ferretería San Pedro, adaptada para desplegar en Render y usar el dominio:

```txt
cloud.ferreteriasanpedro.cl
```

## Qué incluye

- Login y usuarios.
- Permiso superior exclusivo para `gus`.
- Dashboard ejecutivo.
- Productos y precios.
- Importación de maestro de productos por Excel.
- Stock Inteligente / Kardex Integrado.
- POS / ventas rápidas.
- Cotizaciones con PDF comercial brandeado.
- Compras y recepción de mercadería.
- Caja y turnos.
- Clientes / CRM.
- Proveedores.
- Despachos con control anti-duplicado.
- DTE como módulo preparado para Facturación.cl.
- Asistente IA Elias preparado para OpenAI.
- Auditoría.

## Usuarios iniciales

```txt
gus / gus123
admin / admin123
vendedor / vendedor123
bodega / bodega123
```

Cambiar claves inmediatamente en producción.

## Render

Variables recomendadas:

```txt
DATABASE_PATH=/data/ferreteria_cloud_erp.db
IVA_RATE=0.19
SUPERIOR_USERNAME=gus
OPENAI_API_KEY=tu_api_key
OPENAI_MODEL=gpt-5.4-mini
```

Disk persistente:

```txt
Mount Path: /data
```

## PostgreSQL

La app soporta PostgreSQL si se define:

```txt
DATABASE_URL=postgresql://...
```

Si no existe DATABASE_URL, usa SQLite persistente en `/data`.

## Custom Domain

Configurar en Render:

```txt
Custom Domains -> cloud.ferreteriasanpedro.cl
```

En cPanel crear CNAME:

```txt
cloud -> <tu-servicio>.onrender.com
```

## Importación de productos

Columnas aceptadas:

```txt
Código Producto
Descripción
Precio Compra Neto
Precio Venta Bruto
Stock
Activo
Categoría
Marca
Código Barras
```

## Nota técnica

Esta versión es un MVP funcional inicial, no reemplaza todavía todos los módulos de Random.
Está diseñada para avanzar por fases hacia un ERP propio completo.
