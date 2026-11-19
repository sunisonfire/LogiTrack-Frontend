# Sunnie & Moonie · LogiTrack Frontend

Frontend estático responsive para el backend Spring Boot incluido en el proyecto.

## Estructura
- `index.html`
- `css/styles.css`
- `js/config.js` — URL del backend
- `js/app.js` — autenticación, vistas, formularios y consumo REST

## Ejecutar
1. Inicia MySQL y el backend Spring Boot.
2. Abre `frontend/index.html` con Live Server (puerto 5500).
3. Inicia sesión con una cuenta existente.
4. Para entrar desde el celular, el teléfono y el computador deben estar en la misma red Wi-Fi.

## Configuración del backend
Edita `js/config.js`:
```js
window.LOGITRACK_CONFIG = Object.freeze({
  API_BASE_URL: "http://localhost:8080",
  REQUEST_TIMEOUT_MS: 20000
});
```

### Acceso desde celular (LAN)
`localhost` en el celular apunta al propio celular, no al computador. Cambia `API_BASE_URL` por la IPv4 local del computador, por ejemplo:
`http://192.168.1.20:8080`

Abre desde el teléfono la URL de Live Server usando también la IP del computador:
`http://192.168.1.20:5500/LogiTrack/frontend/` (ajusta la ruta según dónde abras el proyecto).

El backend del ZIP limita CORS a `http://127.0.0.1:5500` y `http://localhost:5500`. Para permitir la IP/puerto real de Live Server, agrega ese origen exacto a `config.setAllowedOrigins(...)` en `SecurityConfig.java`. Si pruebas desde móvil, el origen es el host/IP y puerto que aparecen en la barra del navegador del teléfono. No uses `0.0.0.0` como URL del navegador. Además, permite los puertos 5500 y 8080 en el firewall del computador y configura Spring para escuchar en la interfaz de red (no solo loopback).

## Funcionalidades conectadas
- Login JWT `POST /auth/login` y solicitud de acceso `POST /auth/register`.
- Productos: listar, consultar (a través de lista), filtrar por categoría/nombre desde UI, crear, actualizar y eliminar.
- Marcas, ciudades, bodegas: CRUD.
- Empleados y administradores: CRUD (solo administrador según SecurityConfig).
- Inventario por bodega: listar, crear, actualizar y eliminar.
- Movimientos: listar, consultar detalle, crear entrada/salida/transferencia con múltiples líneas.
- Solicitudes pendientes: listar, aprobar con bodega asignada y rechazar con motivo.
- Personas: consulta.
- Auditoría: consulta y filtros visuales.
- Reporte general: stock por bodega y productos más movidos.

## Roles y permisos
El frontend muestra opciones administrativas solo cuando el login devuelve `rol: ADMIN`. El backend sigue siendo la autoridad: según `SecurityConfig`, los endpoints de administración/personas/auditoría/solicitudes son solo ADMIN; catálogo y consultas de movimientos son accesibles a ADMIN y EMPLEADO; crear movimientos también. Las demás operaciones quedan protegidas por backend.

## Notas importantes
- El DTO `ProductoDtoResponse` no contiene URL de imagen ni archivo multimedia. Por eso las tarjetas muestran una composición gráfica de marca; no se inventan fotografías ni se envía una propiedad de imagen inexistente.
- El inventario no expone un endpoint de imagen ni de carga de archivos.
- La API de auditoría ofrece filtros adicionales; esta interfaz incluye búsqueda local y filtro por tipo sobre los registros descargados.
- No existe endpoint de actualización/eliminación de movimientos; el frontend no presenta esas acciones.
- Las categorías admitidas son las definidas en el enum `Categoria` del backend.
- El registro público solicita acceso como empleado; no crea una cuenta activa. La aprobación la realiza una administradora.
- Las operaciones pueden ser rechazadas por reglas de negocio o relaciones existentes; el frontend muestra el mensaje que devuelve el servidor.
