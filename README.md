# Sunnie & Moonie · LogiTrack Frontend

Frontend web estático y responsive para el sistema de gestión de inventario **LogiTrack**, desarrollado para consumir la API REST del backend Spring Boot.

La interfaz utiliza HTML, CSS y JavaScript, e integra autenticación JWT, gestión de inventario, bodegas, productos, movimientos y consultas administrativas.

## 1. Estructura del proyecto

```text
Frontend/
├── index.html
├── css/
│   └── styles.css
└── js/
    ├── config.js
    └── app.js
```

- `index.html`: estructura principal de la aplicación.
- `css/styles.css`: estilos, diseño visual y adaptación responsive.
- `js/config.js`: configuración de la URL del backend.
- `js/app.js`: autenticación, navegación, formularios y consumo de servicios REST.

## 2. Requisitos previos

- Navegador web actualizado.
- Python 3 o Visual Studio Code con Live Server.
- Backend Spring Boot de LogiTrack.
- MySQL configurado y ejecutándose en el equipo donde funciona el backend.
- Conexión a Internet para cargar las fuentes tipográficas externas.

## 3. Configuración del backend

Antes de ejecutar el frontend, es necesario configurar la dirección de la API.

Abrir el archivo:

`js/config.js`

Configurar la dirección correspondiente al computador donde se ejecuta Spring Boot:

```javascript
window.LOGITRACK_CONFIG = Object.freeze({
    API_BASE_URL: "http://localhost:8080",
    REQUEST_TIMEOUT_MS: 20000
});
```

**Importante:** `localhost` solamente funciona cuando el frontend y el backend se ejecutan en el mismo computador.

Si el backend está en otro equipo, se debe reemplazar `localhost` por la dirección IPv4 de ese equipo:

```javascript
API_BASE_URL: "http://IP_DEL_SERVIDOR:8080",
```

Por ejemplo:

```javascript
API_BASE_URL: "http://192.168.200.100:8080",
```

La dirección IP debe corresponder al computador que ejecuta Spring Boot, no necesariamente al que sirve el frontend.

## 4. Ejecución local del frontend

### Alternativa A. Utilizando Python

1. Abrir una terminal dentro de la carpeta `Frontend`.
2. Ejecutar:

```powershell
python -m http.server 8000 --bind 0.0.0.0
```

3. Mantener la terminal abierta.
4. Abrir el navegador en:

```text
http://localhost:8000
```

También se puede acceder mediante la IP local del computador:

```text
http://IP_DEL_COMPUTADOR:8000
```

### Alternativa B. Utilizando Visual Studio Code

1. Abrir la carpeta `Frontend` en Visual Studio Code.
2. Instalar la extensión Live Server, si todavía no está instalada.
3. Abrir `index.html`.
4. Seleccionar **Open with Live Server**.
5. Utilizar la dirección y el puerto indicados por Live Server.

## 5. Acceso desde celulares y otros computadores (red local)

El frontend permite acceder desde otros dispositivos conectados a la misma red Wi-Fi, siempre que la red permita la comunicación entre ellos.

### 5.1. Identificar la dirección IP

En el computador que sirve el frontend, abrir CMD o PowerShell y ejecutar:

```powershell
ipconfig
```

Identificar la dirección IPv4 del adaptador Wi-Fi.

Ejemplo:

```text
192.168.200.214
```

### 5.2. Abrir el frontend desde otro dispositivo

Si se utiliza Python en el puerto 8000, ingresar desde el celular o computador:

```text
http://192.168.200.214:8000
```

Se debe reemplazar la IP por la dirección real del computador que sirve el frontend.

**Nota:** `localhost` desde un celular apunta al propio celular, no al computador que ejecuta el servidor.

### 5.3. Configurar la dirección del backend

Si el backend está ejecutándose en otro computador, modificar `js/config.js`:

```javascript
window.LOGITRACK_CONFIG = Object.freeze({
    API_BASE_URL: "http://IP_DEL_BACKEND:8080",
    REQUEST_TIMEOUT_MS: 20000
});
```

Todos los dispositivos que utilicen ese frontend enviarán sus peticiones al mismo backend.

### 5.4. Configurar Spring Boot para la red local

En el archivo `application.properties` del backend se puede configurar:

```properties
server.address=0.0.0.0
server.port=8080
```

Esto permite que Spring Boot escuche conexiones a través de las interfaces de red del equipo.

### 5.5. Configurar CORS

En `SecurityConfig.java`, dentro del método `corsConfigurationSource()`, se debe autorizar el origen exacto desde donde se abre el frontend.

Por ejemplo, si el frontend se sirve desde:

```text
http://192.168.200.214:8000
```

Se agrega a la lista de orígenes permitidos:

```java
"http://192.168.200.214:8000"
```

Se deben conservar los demás orígenes necesarios, métodos HTTP y encabezados permitidos.

Si cambia la IP o el puerto del frontend, se debe actualizar el origen autorizado.

Después de modificar la configuración, es necesario reiniciar Spring Boot.

### 5.6. Firewall de Windows

En el computador que ejecuta cada servicio, se deben permitir las conexiones entrantes correspondientes:

- Puerto `8000`: servidor del frontend con Python.
- Puerto `8080`: servidor backend Spring Boot.

Las reglas deben limitarse a la red local de confianza y al perfil privado de Windows.

También se debe comprobar que el router no tenga aislamiento entre dispositivos Wi-Fi.

## 6. Escenario de demostración con dos computadores

Cuando el frontend y el backend se ejecutan en equipos diferentes:

| Componente | Equipo |
|---|---|
| Frontend HTML, CSS y JS | Computador del usuario |
| Servidor Python, puerto 8000 | Computador del usuario |
| Backend Spring Boot, puerto 8080 | Computador del docente |
| Base de datos MySQL | Computador donde se ejecuta el backend |
| Dispositivos de prueba | Celular y computadores conectados a la misma Wi-Fi |

Procedimiento:

1. Conectar los dispositivos a la misma red Wi-Fi.
2. Iniciar MySQL en el equipo del backend.
3. Ejecutar Spring Boot en el computador del docente.
4. Identificar la IPv4 del computador del backend.
5. Actualizar `API_BASE_URL` en `js/config.js` con esa dirección.
6. Autorizar el origen del frontend en el CORS del backend.
7. Iniciar el servidor Python en el computador del frontend.
8. Abrir el frontend desde el celular y los demás computadores.
9. Probar el inicio de sesión y las operaciones principales.

Las direcciones IP pueden cambiar dependiendo de la red. Se deben verificar antes de cada demostración.

## 7. Funcionalidades conectadas

El frontend consume los servicios REST del backend y ofrece las siguientes funcionalidades:

- **Autenticación:** inicio de sesión mediante JWT y solicitud de registro.
- **Productos:** listar, consultar mediante listado, filtrar por categoría o nombre, crear, actualizar y eliminar.
- **Marcas:** operaciones CRUD.
- **Ciudades:** operaciones CRUD.
- **Bodegas:** operaciones CRUD.
- **Empleados:** operaciones CRUD según permisos.
- **Administradores:** operaciones CRUD según permisos.
- **Inventario:** consulta y gestión de inventario por bodega.
- **Movimientos:** consulta, detalle y registro de entradas, salidas y transferencias con múltiples productos.
- **Solicitudes de registro:** consulta, aprobación con bodega asignada y rechazo con motivo.
- **Personas:** consulta de registros.
- **Auditoría:** consulta y filtros visuales.
- **Reportes:** resumen general de stock por bodega y productos más movidos.

## 8. Roles y permisos

El frontend adapta las opciones disponibles según el rol devuelto por el backend durante el inicio de sesión.

**ADMIN**
- Acceso a las opciones administrativas.
- Gestión de administradores, empleados y demás recursos autorizados.
- Consulta de auditorías y solicitudes de registro.

**EMPLEADO**
- Acceso a las operaciones permitidas para su rol.
- Consulta de catálogos y recursos autorizados.
- Registro de movimientos de inventario.

La seguridad real está implementada en el backend mediante Spring Security y JWT. Ocultar opciones en la interfaz no sustituye la autorización del servidor.

## 9. Consideraciones técnicas

- La autenticación utiliza tokens JWT para las peticiones protegidas.
- La comunicación con el backend se realiza mediante HTTP REST y JSON.
- Las operaciones están sujetas a las validaciones y reglas de negocio del backend.
- El frontend no reemplaza las validaciones de seguridad ni de integridad de datos del servidor.
- Las categorías disponibles corresponden a los valores definidos en el enum `Categoria` del backend.
- El registro público genera una solicitud de acceso como empleado. La cuenta requiere aprobación administrativa.
- El DTO `ProductoDtoResponse` no contiene URL de imagen ni archivos multimedia; por eso las tarjetas utilizan composiciones gráficas de marca.
- El inventario no expone un endpoint de imágenes ni de carga de archivos.
- La auditoría ofrece filtros adicionales en el backend; la interfaz incluye búsqueda local y filtro por tipo sobre los registros descargados.
- No existe endpoint de actualización o eliminación de movimientos; por tanto, la interfaz no presenta esas acciones.
- Las operaciones pueden ser rechazadas por reglas de negocio o relaciones existentes. El frontend presenta los mensajes devueltos por el servidor.

## 10. Consideraciones de seguridad

- No publicar credenciales ni secretos del backend en el repositorio.
- Mantener activa la autenticación JWT.
- No utilizar `*` como origen CORS cuando se utilizan credenciales.
- No exponer MySQL directamente a otros dispositivos de la red.
- Para demostraciones locales, utilizar redes Wi-Fi de confianza.
- GitHub Pages utiliza HTTPS. Un frontend publicado allí no puede consumir directamente un backend HTTP por las restricciones de contenido mixto del navegador. Para ese escenario se necesita un backend accesible mediante HTTPS.

## 11. Solución de problemas frecuentes

| Problema | Posible solución |
|---|---|
| `Load failed` al iniciar sesión | Comprobar URL del backend, CORS y conectividad. |
| Error CORS | Autorizar el origen exacto del frontend en `SecurityConfig.java`. |
| No se puede acceder desde el celular | Verificar que ambos dispositivos estén en la misma Wi-Fi y revisar el firewall. |
| Error de conexión con `localhost` | Utilizar la IPv4 del computador que ejecuta el backend. |
| Error 401 | Verificar las credenciales y el proceso de autenticación. |
| Error 403 | Revisar los permisos asociados al rol del usuario. |
| Error 404 | Verificar la ruta del endpoint y su disponibilidad en el backend. |
| El frontend no carga los cambios | Recargar la página y limpiar la caché del navegador. |

---

**Sunnie & Moonie · LogiTrack**

*Beauty in every detail. Stock with a little soul.*
