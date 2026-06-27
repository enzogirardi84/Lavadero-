# Plan de Implementación: Integración con Supabase y Publicación en GitHub

En esta fase, configuraremos la conexión de datos de todo el ecosistema (Java y Python) para utilizar la base de datos PostgreSQL hospedada en **Supabase** y prepararemos el repositorio local de **Git** para subir el código a **GitHub**.

---

## 1. Conexión a la Base de Datos de Supabase

El ID de proyecto de tu base de datos Supabase es `sqczmyaoqplrmrgyczjy`. Por lo tanto:
* **Host de PostgreSQL:** `db.sqczmyaoqplrmrgyczjy.supabase.co`
* **Puerto:** `5432`
* **Base de Datos:** `postgres`
* **Usuario:** `postgres`

### Cambios de Configuración:
1. **Java (`application.yml`):** Cambiaremos el datasource URL a `jdbc:postgresql://db.sqczmyaoqplrmrgyczjy.supabase.co:5432/postgres`. Usaremos una variable de entorno `${SUPABASE_DB_PASSWORD}` para la contraseña del usuario postgres.
2. **Python (`DB_URL`):** Modificaremos la cadena por defecto a `postgresql://postgres:{PASSWORD}@db.sqczmyaoqplrmrgyczjy.supabase.co:5432/postgres` en todos los archivos de analítica y scripts (`main.py`, `customer_loyalty.py`, `feedback_nps_analyzer.py`).

---

## 2. Publicación en GitHub

Inicializaremos Git localmente en `c:\Lavadero`, añadiremos todos los archivos y configuraremos el remote a `https://github.com/enzogirardi84/Lavadero-`.

---

## Proposed Changes

### [Componente: Configuración de Base de Datos (Supabase)]
*Redirección de la persistencia de datos a la nube.*

#### [MODIFY] [application.yml](file:///c:/Lavadero/backend-java/src/main/resources/application.yml)
- Actualizar la URL de base de datos a Supabase:
  `jdbc:postgresql://db.sqczmyaoqplrmrgyczjy.supabase.co:5432/postgres`
- Cambiar la contraseña por defecto a `${SUPABASE_DB_PASSWORD:}` para que el usuario la ingrese de forma segura.

#### [MODIFY] [main.py](file:///c:/Lavadero/automation-python/api/main.py)
- Actualizar `DB_URL` por defecto con la estructura de Supabase PostgreSQL.

#### [MODIFY] [customer_loyalty.py](file:///c:/Lavadero/automation-python/scripts/customer_loyalty.py)
- Actualizar `DB_URL` por defecto con la estructura de Supabase PostgreSQL.

#### [MODIFY] [feedback_nps_analyzer.py](file:///c:/Lavadero/automation-python/scripts/feedback_nps_analyzer.py)
- Actualizar `DB_URL` por defecto con la estructura de Supabase PostgreSQL.

---

### [Componente: Control de Versiones Git]
*Preparación y enlace del repositorio.*

#### [NEW] [.gitignore](file:///c:/Lavadero/.gitignore)
- Configuración para excluir archivos generados, entornos virtuales (`.venv`), binarios compilados de Java (`target/`), claves y configuraciones temporales de IDEs (`.idea`, `.vscode`).

---

## 3. Plan de Verificación y Empuje

### Inicialización y Commit
1. Ejecutar `git init` en el workspace `c:\Lavadero`.
2. Crear el archivo `.gitignore` para no subir basura.
3. Hacer `git add .` y `git commit -m "Initial commit - Car Wash Hybrid System"`.
4. Añadir el origen remoto: `git remote add origin https://github.com/enzogirardi84/Lavadero-`.
5. Mostrar los comandos necesarios para realizar el `git push -u origin main`.
