# Uni-App

Sistema de gestión académica universitaria. Visualización de pensum, seguimiento de GPA, generación de horarios y más.

🌐 **[uni-app-acm.vercel.app](https://uni-app-acm.vercel.app)**

---

## Funcionalidades

### Pensum
- **4 vistas**: Árbol (por semestre), Tabla, Grafo de prerrequisitos, Estadísticas
- Código de colores por estado: Pendiente / Inscrita / Aprobada / Reprobada / Retirada
- Tipos de materia: Núcleo, Básicas, Socio Humano, Énfasis, Complementarias, Electivas
- Drag & drop entre semestres con validación de prerrequisitos
- Múltiples planes de estudio, copia de datos entre planes
- Simulación de inscripción de semestre

### Calificaciones
- Componentes de evaluación con porcentajes
- Cálculo automático de nota final
- Simulación de notas necesarias

### GPA
- Promedio acumulado en tiempo real
- Gráficas por semestre, distribución de estados y tipos
- Simulación de notas y cálculo de nota necesaria para GPA objetivo
- Alertas de rendimiento

### Horario
- Registro de secciones por materia
- Franjas bloqueadas y preferidas
- Generación automática de combinaciones sin conflictos
- Métricas: días libres, huecos, hora inicio/fin
- Ordenamiento por días libres, menos huecos, entrada tarde, salida temprana
- Exportación a PNG e ICS (calendario)

### Sincronización
- Offline-first: los datos se guardan en localStorage
- Cuenta opcional para sincronizar entre dispositivos vía Supabase
- Indicador de conexión en tiempo real

### General
- Undo/Redo (Ctrl+Z / Ctrl+Y)
- URLs navegables con hash y query params
- Tema oscuro
- Reporte de errores integrado (envío directo a Supabase)

---

## Plantillas disponibles

| Carrera | Materias | Créditos |
|---------|----------|----------|
| Ciencia de Datos | 47 | 138 |
| Ingeniería de Sistemas | 52 | 138 |
| Bioingeniería | 48 | 136 |
| Ingeniería Civil | 46 | 136 |
| Ingeniería Electrónica | 48 | 138 |
| Ingeniería Mecánica | 49 | 133 |
| Ingeniería Mecatrónica | 50 | 136 |
| Ingeniería Industrial | 51 | 138 |

También podés empezar con un pensum vacío o importar tu propio JSON.

---

## Cómo usar

### 1. Elegir un plan
Al entrar por primera vez, seleccioná una carrera de las plantillas o empezá vacío. Después podés crear más planes desde el selector superior.

### 2. Gestionar materias
Cambiá estados, colores, arrastrá entre semestres. Las reglas de prerrequisitos se validan automáticamente.

### 3. Registrar notas
Hacé click en una materia para asignarle nota y componentes de evaluación.

### 4. Generar horario
Andá a la pestaña Horario, registrá las secciones disponibles y generá combinaciones.

### 5. Sincronizar (opcional)
Creá una cuenta para sincronizar tus datos entre dispositivos.

### 6. Reportar errores
Usá el botón ⓘ en la barra de navegación para reportar un problema. Se envía directo a Supabase con diagnóstico automático.

---

## Configuración

### Variables de entorno

| Variable | Requerida | Descripción |
|----------|-----------|-------------|
| `SUPABASE_URL` | Sí | URL de tu proyecto Supabase |
| `SUPABASE_ANON_KEY` | Sí | Anon key de Supabase |
| `SECRET_KEY` | Sí | Clave secreta de Flask |
| `FLASK_ENV` | No | `development`, `production` o `testing` |
| `KEEPALIVE_SECRET` | No | Secreto para endpoint de keepalive (obsoleto) |

### Base de datos

Ejecutá `database/supabase_schema.sql` en el SQL Editor de Supabase para crear las tablas:

- `pensums`, `clases`, `calificaciones`, `configuraciones`, `franjas` — datos de usuario (RLS por `user_id`)
- `errors` — reportes de errores (INSERT público, SELECT solo autenticado)

### Keepalive

El workflow `.github/workflows/keepalive.yml` hace ping a la REST API de Supabase cada 3 días para evitar que la base de datos entre en suspensión. Requiere los secrets `SUPABASE_URL` y `SUPABASE_ANON_KEY` en GitHub.

---

## Stack

| Categoría | Tecnología |
|-----------|------------|
| Backend | Flask (Python) |
| Frontend | Tailwind CSS, Vanilla JS |
| Base de datos | Supabase (PostgreSQL) |
| Autenticación | Supabase Auth (email, OAuth) |
| Gráficas | Canvas API |
| Exportación | PNG (html2canvas), ICS (iCalendar) |
| Hosting | Vercel |
| CI/CD | GitHub Actions |

---

## Estructura

```
app/
├── blueprints/          # Flask blueprints
│   ├── api.py           #   28 endpoints REST (sync, GPA, schedule, export…)
│   ├── pensum.py        #   /pensum
│   ├── semester.py      #   /semester
│   ├── schedule.py      #   /schedule
│   ├── auth.py          #   /auth (confirmación email, reset password)
│   ├── faq.py           #   /faq
│   └── consejos.py      #   /consejos
├── models/              # Pydantic models
│   ├── materia.py       #   Materia, Pensum
│   ├── clase.py         #   Clase, BloqueHorario
│   ├── horario.py       #   Franja, HorarioCombination
│   └── configuracion.py #   Configuracion, Calificacion
├── services/            # Lógica de negocio
│   ├── database.py      #   Supabase CRUD + auth
│   ├── pensum_service.py
│   ├── gpa_service.py
│   ├── schedule_service.py
│   ├── export_service.py
│   ├── parser_service.py
│   └── progress_report_parser.py
├── static/
│   ├── js/              #   8 módulos JS (app, auth, storage, pensum…)
│   ├── css/main.css
│   └── templates/       #   Plantillas JSON de pensums
├── templates/           # Jinja2 templates
├── __init__.py          # App factory + blueprint registration
└── config.py            # Configuración

api/index.py             # Entry point Vercel (WSGI)
database/supabase_schema.sql
.github/workflows/
├── keepalive.yml        # Cron cada 3 días
└── tests.yml            # CI en push/PR
requirements.txt
vercel.json
```

---

## Desarrollo

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements-dev.txt
cp .env.example .env   # completar SUPABASE_URL y SUPABASE_ANON_KEY
flask run
```

---

## Tests

```bash
pytest
```

---

## Licencia

GNU General Public License v3.0. Ver [LICENSE](LICENSE).
