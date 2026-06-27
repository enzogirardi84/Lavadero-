# Lavadero Car Wash - Sistema Híbrido de Gestión y Ventas

Este proyecto es una solución híbrida de nivel enterprise diseñada para un **Lavadero de Autos Completo (Car Wash)**. Combina **Java (Spring Boot)** en el backend principal por su robustez, tipado fuerte y manejo de transacciones, junto con **Python** para automatizaciones de marketing digital, segmentación de datos con Pandas y envíos automatizados (WhatsApp / Mailing).

---

## 📸 Mockup de Interfaz Propuesta (UI/UX)

Para brindar una experiencia premium, proponemos un diseño moderno con colores oscuros (Dark Mode), acentos en violeta eléctrico (`#7C3AED`) y azul neon.

![Diseño del Dashboard de Gestión del Lavadero](file:///c:/Lavadero/carwash_dashboard.png)

### Características de la UI/UX:
1. **Dashboard Consolidado:** Métricas clave del día (facturación, servicios en curso, caja diaria y alertas de bajo stock).
2. **Calendario de Turnos Interactivo:** Visualización tipo *timeline* de los lavaderos en progreso (Lavado simple, completo, de motor, tapizados, encerado) clasificados por color según su estado (Pendiente, En Progreso, Completado).
3. **Punto de Venta Rápido (POS):** Atajos rápidos para vender productos del lavadero (ceras, aromatizantes, microfibras, siliconas) y facturar con un solo clic.

---

## 1. Arquitectura del Proyecto

El sistema se organiza bajo una estructura de **Monorepo** que unifica la base de datos, el backend Java y los servicios de automatización de Python.

### Estructura de Directorios

```text
lavadero-monorepo/
├── database/                       # Base de datos relacional
│   └── schema.sql                  # Creación de tablas, índices y datos semilla
├── backend-java/                   # Servidor principal Java (Spring Boot 3.x)
│   ├── pom.xml                     # Dependencias Maven
│   └── src/main/
│       ├── java/com/lavadero/
│       │   ├── controller/         # Endpoints REST (Caja, POS, Turnos, Marketing)
│       │   ├── service/            # Lógica e integración con scripts de Python
│       │   └── LavaderoApplication.java # Clase principal
│       └── resources/
│           └── application.yml     # Configuración y credenciales de conexión
├── automation-python/              # Scripts de automatización y marketing
│   ├── requirements.txt            # Dependencias Python
│   ├── api/
│   │   └── main.py                 # FastAPI microservicio para análisis de segmentación
│   └── scripts/
│       ├── customer_loyalty.py     # Script de fidelización (Inactivos > 20 días)
│       └── whatsapp_sender.py      # Script de automatización de envío de WhatsApp
└── README.md                       # Documentación del sistema
```

### Flujo de Integración Híbrida

El backend Java se integra con los componentes Python a través de dos mecanismos estratégicos:

```mermaid
graph TD
    JavaApp[Spring Boot App] -->|Llamada HTTP REST| FastAPI[FastAPI :8000]
    JavaApp -->|ProcessBuilder| ScriptPython[Python Scripts]
    FastAPI -->|Pandas Analytics| PostgreSQL[(PostgreSQL)]
    ScriptPython -->|WhatsApp / Cupones| PostgreSQL
```

1. **Llamadas REST Síncronas (FastAPI):** Usado para procesos de respuesta inmediata que involucran manipulación de datos en memoria (ej. consultar la segmentación de clientes calculada por Pandas).
2. **Ejecución Asíncrona / CLI (ProcessBuilder):** Usado para disparar tareas en segundo plano que ejecutan herramientas locales (ej. scripts que simulan interacciones de interfaz mediante PyAutoGUI o Selenium para mandar recordatorios de turnos por WhatsApp).

---

## 2. Descripción de Módulos

### MÓDULO 1: Arquitectura Híbrida (Conectividad)
* **Java (Spring Boot):** Expone APIs REST, gestiona el Punto de Venta (POS), controla el stock y la caja diaria de forma segura con JPA/Hibernate.
* **Integración Java-Python:** El servicio `PythonIntegrationService.java` ejecuta comandos del sistema operativo localizando el entorno de Python configurado (`python.executable`) y capturando los flujos de salida (`stdout` y `stderr`) en tiempo de ejecución.
* **Base de Datos Unificada (PostgreSQL):** Almacena de forma centralizada todas las entidades comerciales y de fidelización, permitiendo a Python leer y procesar datos transaccionales de forma nativa.

### MÓDULO 2: Ventas, Facturación y Caja (Java)
* **Registro de Servicios:** Soporte para lavados segmentados con duración estimada para control de tiempos de entrega y asignación de empleados.
* **Punto de Venta (POS) y Stock:** Registro de ventas de productos físicos de retail del lavadero. Incluye validación de stock y alertas automáticas cuando un artículo está por debajo del `stock_minimo`.
* **Caja Diaria:** Control del flujo de caja diaria (monto apertura, egresos por compras, ingresos por ventas/servicios, y balance de cierre).

### MÓDULO 3: Publicidad, WhatsApp y Fidelización (Python)
* **Envío Programado por WhatsApp (`whatsapp_sender.py`):** Automatiza el contacto con el cliente al agendar un turno o al finalizar el lavado. Utiliza la API de WhatsApp Web y simula el envío físico mediante control de teclado con `pyautogui`.
* **Script de Fidelización de Clientes (`customer_loyalty.py`):**
  * Busca clientes cuya última visita fue hace más de 20 días.
  * Genera automáticamente un cupón del 15% de descuento (`VOLVEXXXXXX`) que expira en 15 días.
  * Registra el cupón en la base de datos para que el POS Java pueda validarlo al momento de la facturación.
* **Segmentación de Clientes (`api/main.py`):** Endpoint `/segmentacion` que recupera las transacciones históricas de los clientes, y mediante **Pandas** los agrupa en:
  * **VIP:** Clientes con gasto acumulado > $30,000 ARS o más de 10 visitas.
  * **FRECUENTE:** Clientes con gasto entre $10,000 y $30,000 ARS, o de 4 a 10 visitas.
  * **OCASIONAL:** Clientes con consumos menores a los anteriores.

---

## 3. Guía de Ejecución y Despliegue

### Paso 1: Configurar la Base de Datos (Local o Supabase)
Puedes usar una base de datos PostgreSQL local, o bien la instancia en la nube de **Supabase**:

#### Opción A: Inicialización Automatizada en Supabase
El script interactivo en Python inicializa todas las tablas e índices en Supabase con datos semilla:
1. Asegúrate de tener instalado `psycopg2-binary` y `pandas`.
2. Corre el script de configuración:
   ```bash
   python database/setup_supabase_db.py
   ```
3. El script te solicitará la contraseña de tu base de datos Supabase, conectará a `db.sqczmyaoqplrmrgyczjy.supabase.co` e inicializará el esquema completo.

#### Opción B: Base de Datos Local
1. Instale y asegúrese de que su servidor PostgreSQL esté corriendo.
2. Cree una base de datos llamada `lavadero`.
3. Ejecute el script `database/schema.sql`:
   ```bash
   psql -U postgres -d lavadero -f database/schema.sql
   ```

### Paso 2: Ejecutar / Desplegar Python (Vercel Serverless o Local)

#### Opción A: Despliegue en Vercel (Recomendado)
El microservicio FastAPI de Python está pre-configurado para desplegarse como Serverless Functions en **Vercel** mediante Git:
1. Sube tu código a tu repositorio GitHub:
   ```bash
   git push -u origin main
   ```
2. Agrega la variable de entorno `SUPABASE_DB_PASSWORD` en los ajustes de Vercel para permitir la conectividad.
3. Vercel expondrá tu API en `https://lavadero.vercel.app`.

#### Opción B: Ejecución Local
1. Diríjase a la carpeta `automation-python`:
   ```bash
   cd automation-python
   ```
2. Cree e instale el entorno virtual (`venv`):
   ```bash
   python -m venv .venv
   # Activar en Windows:
   .venv\Scripts\activate
   # Instalar:
   pip install -r requirements.txt
   ```
3. Inicie el servidor de desarrollo:
   ```bash
   python api/main.py
   ```

### Paso 3: Ejecutar Java Spring Boot
1. Define la variable de entorno `SUPABASE_DB_PASSWORD` con la clave de tu base de datos Supabase.
2. Si usas Vercel para Python, define `PYTHON_FASTAPI_URL` con tu URL de Vercel.
3. Ejecute el backend:
   ```bash
   # En Windows (PowerShell):
   $env:SUPABASE_DB_PASSWORD="tu_contraseña_supabase"
   mvn clean spring-boot:run
   ```
   *(Estará disponible en http://localhost:8080)*

---

## 👥 Módulos Avanzados Incorporados

### 🗣️ Satisfacción del Cliente y NPS (Net Promoter Score)
* **Visualización en Dashboard:** Una tarjeta integrada calcula en tiempo real el Net Promoter Score del lavadero.
* **Clasificación NPS:**
  * Promotores (5★): Clientes altamente leales.
  * Pasivos (4★): Satisfechos pero neutros.
  * Detractores (1-3★): Oportunidades de mejora.
* **Registro AJAX:** Formulario rápido para guardar la calificación de un lavado directamente.

### 👥 Equipo de Trabajo (Staff)
* Permite dar de alta empleados (Lavadores, Cajeros, Administradores).
* **Control de Disponibilidad:** Un interruptor AJAX permite marcar operarios como activos o inactivos de inmediato para asignar turnos de forma eficiente.
