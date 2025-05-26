# Práctica: Despliegue con Docker, Docker Compose y Git

Este proyecto consiste en el despliegue de una aplicación web compuesta por tres servicios: **frontend**, **backend** y **base de datos**. Se utilizan contenedores Docker para aislar cada componente y Docker Compose para orquestar todos los servicios. Todo el proyecto está versionado con Git y estructurado en ramas independientes para facilitar la organización y el mantenimiento.

---

## 📦 Composición de los servicios

| Servicio  | Tecnología          | Descripción                                                                  |
|-----------|---------------------|-------------------------------------------------------------------------------|
| frontend  | `nginx:alpine`      | Sirve una página HTML estática (`index.html`) en el puerto `8080`.           |
| backend   | `Flask` en Debian   | API en Python con Flask, en el puerto `5000`, con código montado por bind mount. |
| db        | `postgres:15`       | Base de datos PostgreSQL con volumen persistente y script `init.sql` de inicialización. |

---
## 🔧 Requisitos y uso básico

Para ejecutar este proyecto necesitas tener instaladas las siguientes herramientas:

- [Docker](https://www.docker.com/products/docker-desktop/) (versión recomendada: 20.x o superior)
- [Docker Compose](https://www.docker.com/products/docker-desktop/) (incluido en Docker Desktop)
- [Git](https://git-scm.com/) (cualquier versión estable)

### 🔁 Clonar el repositorio

```bash
git clone https://github.com/manuelsantosiglesias/PPSTarefaFinal.git
cd PPSTarefaFinal
```

### 🚀 Ejecutar la aplicación

Una vez clonado el proyecto, simplemente ejecuta:

```bash
docker compose up -d
```

El entorno se construirá y desplegará automáticamente con los tres servicios funcionando: frontend, backend y base de datos.

## 🚀 Ejecución y pruebas

### 1. Lanzar los servicios

```bash
docker compose up -d
```

### 2. Acceder desde el navegador

- Frontend: [http://localhost:8080](http://localhost:8080)
- Backend: [http://localhost:5000](http://localhost:5000)
- DB: docker compose logs db (para revisar los logs)

### 3. Apagar los contenedores

```bash
docker compose down
```

### 4. Verificar persistencia de la base de datos

Tras volver a lanzar el proyecto con `docker compose up`, ejecuta:

```bash
docker compose logs db
```

Deberías ver:

```
PostgreSQL Database directory appears to contain a database; Skipping initialization
```

Esto indica que los datos se han conservado en el volumen `db_data` correctamente.

---

## ⚙️ Explicación del archivo `docker-compose.yml`

El archivo `docker-compose.yml` define y coordina los tres servicios que forman parte de este proyecto. A continuación se explica cada uno:

### 🔹 frontend
- Usa la imagen ligera `nginx:alpine`.
- Expone el puerto `8080` en el host.
- Sirve la página `index.html` utilizando un **bind mount** desde el directorio local `./frontend`.

### 🔹 backend
- Se construye a partir del Dockerfile localizado en `./backend`.
- Expone el puerto `5000` para servir la API.
- Utiliza un **bind mount** (`./backend:/app`) para que los cambios en el código Python se reflejen sin necesidad de recompilar la imagen.
- Ejecuta automáticamente la aplicación Flask gracias a la configuración del `Dockerfile`.

### 🔹 db (base de datos)
- Utiliza la imagen oficial `postgres:15`.
- Se inicializa mediante el script `init.sql`, montado en el contenedor en la ruta `/docker-entrypoint-initdb.d/`.
- Guarda los datos en un **volumen llamado `db_data`**, lo que asegura la persistencia incluso si se eliminan los contenedores.
- Expone el puerto `5432`, útil para conectarse desde herramientas.

### 🔹 Volúmenes
- El volumen `db_data` se define al final del archivo y se utiliza únicamente para almacenar de forma persistente los datos de PostgreSQL.

Este archivo permite desplegar todo el entorno de desarrollo con un solo comando: `docker compose up`.

---

## 📂 Estructura del proyecto

```
.
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── app.py
├── frontend/
│   └── index.html
├── db/
│   └── init.sql
├── docker-compose.yml
├── README.md
└── entrega.txt
```

---

## 🧰 Comandos principales

```bash
# Construir y lanzar los servicios
docker compose up --build

# Ejecutar en segundo plano
docker compose up -d

# Apagar los servicios
docker compose down

# Ver logs de la base de datos
docker compose logs db
```

---

## 🔀 Uso de Git y ramas

- Ramas utilizadas:
  - `frontend`: contiene el HTML.
  - `backend`: contiene el Dockerfile, `app.py` y `requirements.txt`.
  - `main`: integración completa del proyecto.
- Commits frecuentes y con mensajes claros.
- Uso de `merge` y `push` para mantener un historial limpio y bien organizado.
- Proyecto publicado en un repositorio público de GitHub.

---

## 👤 Autor

**Manuel Enrique Santos Iglesias**  
Curso de Docker + Git + Compose · 2025
---

## 🐞 Errores comunes y cómo solucionarlos

### ❌ Error: El nombre del contenedor ya está en uso

**Mensaje:**
```
Error response from daemon: Conflict. The container name "/backend" is already in use by container "XXXXXXXXXXXX".
```

**Causa:**  
Ya existe un contenedor con el nombre `xxxxx` en tu sistema, aunque esté parado.

**Solución:**

1. Verifica los contenedores existentes (incluso los parados):

```bash
docker ps -a
```

2. Elimina el contenedor antiguo:

```bash
docker rm -f xxxxx
```

3. Ejecuta de nuevo el proyecto:

```bash
docker compose up -d
```

**Consejo:**  
Antes de relanzar el proyecto, puedes usar `docker compose down` para limpiar contenedores y evitar conflictos.
