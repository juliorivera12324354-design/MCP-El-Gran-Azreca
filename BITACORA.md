# BITÁCORA — MCP El Gran Azteca ↔ Bind ERP

- **Fecha:** 2026-10-13
- **Versión de la bitácora:** v0.1
- **Versión del .py:** ninguna en el repositorio todavía (pendiente de que Julio lo suba)

## Estado actual
- El repositorio solo tiene `README.md`. No hay ningún archivo `.py` del servidor MCP.
- Las instrucciones del mentor ya están definidas (vienen de la página "Mentor MCP El Gran Azteca").
- Se revisó antes una copia **cortada** del .py (se cortó dentro de `plan_produccion`). Esa copia no está en el repo.

## Qué se hizo y por qué
1. **2026-10-13** — Se creó esta bitácora, porque las instrucciones piden crearla si no existe.

## Qué falta (en orden de prioridad)
1. Julio llena su contexto (los 7 puntos marcados como [sin llenar]).
2. Julio confirma o ajusta las instrucciones del mentor.
3. Julio sube el `.py` completo y vigente al repositorio (sin la API key).
4. Auditoría en este orden: (1) que arranque, (2) `crear_pedido` y todo lo que escriba en Bind, (3) hallazgos previos 1–5, (4) otros riesgos.
5. Plan de correcciones por gravedad.

## Hallazgos de la revisión previa (copia cortada; hay que confirmarlos en el archivo completo)
Pueden tronar el sistema:
1. `auditoria_general` hace `json.loads` del resultado de `revisar_caducidades`, que ya viene recortado a `MAX_CARACTERES` → `JSONDecodeError` con muchos lotes; `seguro` solo atrapa `BindError`.
2. Sin `BIND_SUBSCRIPTION_KEY`, `ventas_por_producto` y `plan_produccion` (150 ventas = 150 consultas) rebasan el corte de 140 de `_respetar_limite`.
3. `_detalles_ventas` usa `asyncio.gather` sin `return_exceptions` → una venta con 404 o timeout cancela todo.

Dan datos incorrectos:
4. `_cerrar_grupo` infla el margen: suma ingreso de todas las ventas, pero costo solo de las que traen costo.
5. Falsos positivos: "posible venta duplicada" con clientes genéricos y "valor concentrado sospechoso" en almacenes con pocos artículos.

No se alcanzó a ver: cierre de `plan_produccion` (variable `aviso`), `crear_pedido`, `exportar_csv`, modelos pydantic, arranque `if __name__ == "__main__": mcp.run()`.

## Decisiones tomadas
- Ninguna todavía.

## Pruebas pendientes
- Arranque sin errores.
- `perfil_empresa` (primera prueba de conexión).
- Cada herramienta en solo lectura con datos reales.
- `crear_pedido`: vista previa (`confirmar=false`) y luego pedido mínimo a cliente de prueba, con `BIND_PERMITIR_ESCRITURA` encendida solo para esa prueba.

## Reglas de seguridad vigentes
- La API key nunca se pide ni va en el código ni en el repo.
- Todo es solo lectura excepto `crear_pedido` (vista previa + confirmación explícita).
- `BIND_PERMITIR_ESCRITURA` apagada hasta auditar `crear_pedido`.
- Hallazgos fiscales/contables = señales para revisar con el contador, no dictámenes.
