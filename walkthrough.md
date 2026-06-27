# Walkthrough: Mejora Híbrida del Lavadero (SaaS Completo en Vercel)

Hemos completado la reestructuración completa del sistema del lavadero, transformándolo en un **SaaS completo con Landing Page, Portal de Autenticación y Dashboard de Control de Roles**, desplegado serverless en **Vercel** y conectado en tiempo real con **Supabase**.

---

## 🎨 Nuevos Componentes Visuales y Funcionales

### 1. Página de Publicidad y Tarifas (`index.html`)
* Ubicación: [index.html](file:///c:/Lavadero/index.html) (Raíz).
* **Diseño Premium:** Una landing page moderna con gradientes oscuros y violetas dedicada a captar clientes y promocionar los servicios de **Mobile Wash**.
* **Secciones:**
  * Hero Section de alto impacto con CTA ("Acceder al Sistema").
  * Tarjetas de Precios interactivas para los servicios de lavado (Simple: $1500, Completo: $2500, Pulido: $6000).
  * Estadísticas de satisfacción general (NPS +75).

### 2. Portal de Acceso Seguro (`login.html`)
* Ubicación: [login.html](file:///c:/Lavadero/login.html).
* **Características:**
  * Formulario de login glassmorphic centrado.
  * Realiza consultas AJAX contra el endpoint `/api/auth/login`.
  * En caso de autenticación exitosa, guarda de manera local en `localStorage` el perfil y rol del usuario e inicia la redirección a `/dashboard`.
  * Muestra credenciales de prueba autocompletadas para facilitar los tests.

### 3. Dashboard con Control de Roles (`dashboard.html`)
* Ubicación: [dashboard.html](file:///c:/Lavadero/dashboard.html).
* **Seguridad:** Si el usuario intenta abrir el panel sin una sesión activa, se redirige automáticamente a `/login`.
* **RBAC (Role-Based Access Control):**
  * Si el rol del usuario es administrador o superadmin, tiene acceso a todos los módulos.
  * Si el rol es operario/operador (mozo/cocina, como `enzo` / `1234`), la interfaz bloquea el POS, la Caja Diaria, el Growth Marketing y la edición de empleados mediante un **overlay glassmorphic de bloqueo de permisos** con un ícono de candado, permitiendo solo la administración de turnos y lavados en progreso.

### 4. APIs de Autenticación (`api/main.py`)
* Ubicación: [main.py](file:///c:/Lavadero/automation-python/api/main.py).
* Implementado el endpoint `POST /api/auth/login` que valida contraseñas y usernames consultando directamente la tabla `usuarios` en Supabase.
* Incluye fallback automático a mock de usuarios de prueba si la base de datos Supabase estuviera caída temporalmente.

### 5. Enrutamiento Limpio (`vercel.json`)
* Ubicación: [vercel.json](file:///c:/Lavadero/vercel.json).
* Habilitado `"cleanUrls": true` para que los usuarios puedan navegar a `/`, `/login` y `/dashboard` sin extensiones `.html`.

---

## 🐙 Repositorio Actualizado
Todos los cambios ya fueron empujados y publicados en tu repositorio remoto en GitHub:
* [enzogirardi84/Lavadero-](https://github.com/enzogirardi84/Lavadero-)
* Esto disparó la compilación de Vercel en caliente, actualizando tu sitio público al instante.
