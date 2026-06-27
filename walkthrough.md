# Walkthrough: Integración de Supabase y Publicación en GitHub

Hemos migrado las conexiones del ecosistema del Lavadero a la base de datos PostgreSQL hospedada en la nube con **Supabase**, e inicializado el control de versiones local con **Git** para subir el código a **GitHub**.

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

## 🐙 Control de Versiones (Git & GitHub)

1. **Ignorado de Archivos Basura:**
   * Creado el archivo [.gitignore](file:///c:/Lavadero/.gitignore) con reglas de exclusión para Maven (`target/`), Python (`.venv`, `__pycache__`), IDEs (`.idea`, `.vscode`) y secretos.
2. **Repositorio Local Inicializado:**
   * Ejecutado `git init`, `git add .`, y creado el primer commit con el mensaje `"Initial commit - Car Wash Hybrid System with Supabase integration"`.
   * Renombrada la rama por defecto a `main`.
3. **Control Remoto Configurado:**
   * Enlazado a tu repositorio remoto en GitHub: `https://github.com/enzogirardi84/Lavadero-`.
