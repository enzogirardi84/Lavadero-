# Walkthrough: Integración de Supabase y Publicación en Vercel / GitHub

Hemos migrado las conexiones del ecosistema del Lavadero a la base de datos PostgreSQL en la nube con **Supabase**, estructurado el despliegue serverless en **Vercel**, e inicializado el control de versiones local con **Git** para sincronizar el código con **GitHub**.

---

## ☁️ Transición a Supabase (PostgreSQL en la Nube)

Redirigimos todas las conexiones de base de datos del backend Java y analíticas en Python hacia la instancia de Supabase del proyecto `sqczmyaoqplrmrgyczjy`:

### 1. Backend Java (Spring Boot)
* Modificado [application.yml](file:///c:/Lavadero/backend-java/src/main/resources/application.yml):
  * URL JDBC: `jdbc:postgresql://db.sqczmyaoqplrmrgyczjy.supabase.co:5432/postgres`
  * Contraseña: Vinculada de forma segura a la variable de entorno `${SUPABASE_DB_PASSWORD:}`.

### 2. Microservicio y Scripts de Python
* Modificados [main.py](file:///c:/Lavadero/automation-python/api/main.py), [customer_loyalty.py](file:///c:/Lavadero/automation-python/scripts/customer_loyalty.py) y [feedback_nps_analyzer.py](file:///c:/Lavadero/automation-python/scripts/feedback_nps_analyzer.py):
  * URL SQLALchemy / Psycopg2: Apunta a `db.sqczmyaoqplrmrgyczjy.supabase.co` interpolando de manera dinámica el valor de la variable de entorno `SUPABASE_DB_PASSWORD`.

---

## ⚡ Despliegue Serverless en Vercel (FastAPI)

Para que Vercel compile y ejecute la aplicación FastAPI de analíticas cada vez que hagas push a GitHub, creamos las configuraciones requeridas en la raíz:

1. [vercel.json](file:///c:/Lavadero/vercel.json):
   * Configura el enrutamiento para redirigir todo el tráfico a `api/index.py` y define el uso del runtime `@vercel/python`.
2. [requirements.txt](file:///c:/Lavadero/requirements.txt):
   * Contiene las dependencias necesarias de analítica y FastAPI. Se omitió la librería `pyautogui` para evitar fallas de compilación ya que Vercel opera sin interfaz gráfica (headless).
3. [index.py](file:///c:/Lavadero/api/index.py):
   * Handler de entrada para Vercel. Añade la ruta de analíticas de Python al path del sistema y expone el objeto `app`.

---

## 🐙 Control de Versiones (Git & GitHub)

1. **Ignorado de Archivos Basura:**
   * Creado el archivo [.gitignore](file:///c:/Lavadero/.gitignore) con reglas de exclusión para Maven (`target/`), Python (`.venv`, `__pycache__`), IDEs (`.idea`, `.vscode`) y secretos.
2. **Repositorio Local Inicializado:**
   * Ejecutado `git init`, `git add .`, y creado el primer commit con el mensaje `"Initial commit - Car Wash Hybrid System with Supabase integration"`.
   * Renombrada la rama por defecto a `main`.
3. **Control Remoto Configurado:**
   * Enlazado a tu repositorio remoto en GitHub: `https://github.com/enzogirardi84/Lavadero-`.
