
# Plantilla Backend – Django 5.2

Este proyecto es un esqueleto de backend basado en **Django 5.2** y **Django REST Framework**, pensado para integrarse con frontends modernos desarrollados en Angular 16.2.16 o cualquier otro framework de tu elección. Sirve como base para construir APIs REST robustas y escalables.

## Requisitos del entorno

- **Python 3.12.1** o superior (instalado y accesible en tu `PATH`).
- **pip** actualizado (`python -m pip install --upgrade pip`).
- **MySQL 5.7+** o **MariaDB 10+** con un usuario y base de datos disponibles.
- **Node 18.19.0**, **npm 10.2.3** y **Angular 16.2.16** (solo si vas a trabajar en el frontend; no son necesarios para el backend).
- Acceso opcional a **Google Cloud Storage** si vas a usar almacenamiento de archivos en la nube.
- **Google Cloud SDK** (`gcloud`) configurado para desplegar en App Engine.

## Instalación y configuración

### 1. Clona el repositorio

```bash
git clone https://github.com/jovas-007/plantilla_backend.git
cd plantilla_backend
```

### 2. Crea y activa un entorno virtual

```bash
python3.12 -m venv venv
# Linux/Mac
source venv/bin/activate
# Windows
venv\Scripts\activate
```

### 3. Instala las dependencias

Asegúrate de que `pip` está actualizado y ejecuta:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

El archivo `requirements.txt` incluye las siguientes bibliotecas:

- Django >= 5.2.5
- djangorestframework >= 3.16.1
- django-filter >= 25.1
- django-cors-headers >= 4.7.0
- pymysql >= 1.1
- requests >= 2.32
- cryptography >= 42.0
- google-cloud-storage >= 2.15
- python-dateutil >= 2.8.2

> Nota: No utilizamos `mysqlclient`; `pymysql` se registra automáticamente como `MySQLdb` en `consultorio_api/__init__.py`.

### 4. Configura la base de datos

El proyecto lee los parámetros de conexión de `my.cnf`, ubicado en la raíz. Puedes modificar este archivo para apuntar a tu servidor MySQL:

```ini
[client]
user = tu_usuario
password = tu_contraseña
host = 127.0.0.1
port = 3306
database = nombre_de_tu_bd
default-character-set = utf8mb4
```

Alternativamente, puedes definir la conexión a través de variables de entorno y modificar `DATABASES` en `consultorio_api/settings.py` según tus necesidades.

### 5. Ejecuta las migraciones

```bash
python manage.py migrate
```

Esto creará todas las tablas necesarias en tu base de datos.

### 6. Crea un superusuario (opcional)

```bash
python manage.py createsuperuser
```

Sigue las instrucciones para establecer nombre de usuario y contraseña. Podrás acceder al panel de administración en `/admin`.

### 7. Configura variables de entorno

Para producción, **no** dejes la `SECRET_KEY` ni los parámetros sensibles en el código. Usa variables de entorno o un archivo `.env`. Algunos parámetros importantes:

- `DJANGO_SECRET_KEY`: clave secreta para Django.
- `DJANGO_ALLOWED_HOSTS`: hosts permitidos separados por comas.
- `DJANGO_DEBUG`: establece a `False` en producción.
- `CORS_ALLOWED_ORIGINS`: lista de URLs permitidas para solicitudes CORS (p. ej., `http://localhost:4200`).

Puedes cargar estas variables con python-decouple o tu gestor de configuración favorito.

### 8. Lanza el servidor de desarrollo

```bash
python manage.py runserver 0.0.0.0:8000
```

Accede a `http://localhost:8000/` para interactuar con la API.

## Características principales

- **Autenticación con tokens** propia (`consultorio_api.models.BearerTokenAuthentication`) y soporte opcional de `rest_framework.authtoken`.
- **API REST** con vistas genéricas y serializadores listos para extender.
- **Gestión de usuarios y perfiles**: creación de usuarios, asignación de grupos y perfiles asociados.
- **CORS configurado** con `django-cors-headers` para permitir solicitudes desde frontends externos.
- **Soporte para almacenamiento en Google Cloud Storage** mediante `google-cloud-storage`.
- **Código modular**: utilidades para cifrado (`cypher_utils.py`), validación de archivos (`data_utils.py`) y envío de correos (`puentes/mail.py`).

## Despliegue en Google App Engine

El archivo `app.yaml` incluye la configuración de despliegue:

```yaml
service: default
runtime: python312   # actualizado para Python 3.12
instance_class: F2
handlers:
  - url: /static
    static_dir: static/
  - url: /.*
    script: auto
```

Para desplegar, asegúrate de tener instalado y autenticado el Google Cloud SDK:

```bash
gcloud app deploy --project tu-proyecto-gcp
```

Puedes personalizar la clase de instancia, el escalado y los buckets de GCS en función de tus necesidades.

## Personalización

Esta plantilla está pensada para que la adaptes a tu propio frontend. Algunos puntos de extensión:

- Crea nuevos `serializers` en `consultorio_api/serializers.py` para tus modelos.
- Añade vistas en `consultorio_api/views/` siguiendo el patrón de DRF (clases basadas en vistas o viewsets).
- Configura permisos personalizados en `REST_FRAMEWORK` según tus reglas de negocio.
- Modifica `CORS_ALLOWED_ORIGINS` para autorizar el dominio de tu frontend de producción.

## Contribuciones y soporte

Este repositorio es mantenido internamente como plantilla de referencia. Si detectas errores o tienes sugerencias, abre un Issue o un Pull Request en GitHub. Para soporte interno, contacta con el equipo de desarrollo de backend de la BUAP.
