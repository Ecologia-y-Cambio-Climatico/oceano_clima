# Oceano Clima

Plataforma reproducible de análisis estadístico, gestión de bases de datos espaciales, reportes automatizados y visualización interactiva de datos oceanográficos y climáticos desarrollada con **R**, **RMarkdown**, **Shiny**, **PostgreSQL / PostGIS** y **Docker**.

## Descripción general

Este repositorio contiene el flujo de trabajo completo para el almacenamiento, procesamiento, modelado y visualización de variables oceanográficas y climáticas (como temperatura superficial del mar, salinidad, precipitación e índices climáticos). La arquitectura está completamente contenerizada mediante **Docker Compose**, integrando una base de datos geoespacial en PostgreSQL/PostGIS con entornos reproducibles de RStudio y despliegue de aplicaciones Shiny.

## Arquitectura y Stack Tecnológico

* **Base de datos espacial:** PostgreSQL con extensión **PostGIS** para almacenamiento, indexación espacial y consultas geográficas de series de tiempo, estaciones y capas vectoriales/raster.
* **Entorno de desarrollo y análisis:** R (`tidyverse`, `sf`, `terra`, `ncdf4`, `DBI`, `RPostgres`, `pool`) ejecutado sobre la imagen personalizada [`davidmacer/rstudio`](https://docker.com).
* **Reportes reproducibles:** Documentos dinámicos en **RMarkdown** / Quarto con generación automatizada de boletines y reportes HTML/PDF.
* **Despliegue de aplicaciones web:** Dashboards interactivos en **Shiny** (`leaflet`, `ggplot2`, `plotly`) servidos mediante la imagen personalizada [`davidmacer/shiny-geospatial`](https://docker.com).
* **Orquestación:** Docker y Docker Compose para levantar todo el ecosistema con un solo comando.

## Estructura del repositorio

```text
oceano_clima/
├── R/                  # Funciones modulares, conexiones a BD (pool/DBI) y scripts de análisis
├── sql/                # Scripts de inicialización, esquemas de tablas e índices PostGIS
├── data-raw/           # Scripts ETL de descarga, limpieza e ingesta hacia PostgreSQL
├── reports/            # Documentos RMarkdown (.Rmd) y reportes renderizados
├── app/                # Aplicación web interactiva en Shiny (ui.R, server.R, módulos)
├── docker-compose.yml  # Orquestación de servicios (PostGIS, RStudio, Shiny)
├── .env.example        # Plantilla de variables de entorno para Docker y PostgreSQL
└── README.md           # Documentación del proyecto
```

## Requisitos previos

* **Docker** (>= 24.0) y **Docker Compose** (v2)
* Git

*(No es necesario instalar R, PostgreSQL ni librerías geoespaciales como GDAL/GEOS/PROJ en el sistema anfitrión, ya que todo viene preconfigurado en las imágenes de Docker).*

## Instalación y despliegue con Docker

1. **Clonar el repositorio:**
   ```bash
   git clone git@github.com:davidmacer/oceano_clima.git
   cd oceano_clima
   ```

2. **Configurar variables de entorno:**
   Copia la plantilla de entorno y ajusta tus credenciales de base de datos y RStudio:
   ```bash
   cp .env.example .env
   ```
   Ejemplo de configuración en `.env`:
   ```text
   POSTGRES_DB=oceano_clima
   POSTGRES_USER=usuario_clima
   POSTGRES_PASSWORD=tu_contraseña_segura
   POSTGRES_PORT=5432

   RSTUDIO_USER=davidmacer
   RSTUDIO_PASSWORD=tu_password_rstudio
   RSTUDIO_PORT=8787
   SHINY_PORT=3838
   ```

3. **Levantar los servicios con Docker Compose:**
   ```bash
   docker compose up -d
   ```
   Esto descargará las imágenes necesarias (`postgis/postgis`, `davidmacer/rstudio` y `davidmacer/shiny-geospatial`), inicializará la base de datos con la extensión PostGIS y levantará los servicios en segundo plano.

4. **Verificar el estado de los contenedores:**
   ```bash
   docker compose ps
   ```

## Uso

Una vez levantados los contenedores, puedes acceder a cada servicio desde tu navegador o cliente SQL:

* **Aplicación Shiny (Dashboard interactivo):** `http://localhost:3838`
* **RStudio Server (Desarrollo y RMarkdown):** `http://localhost:8787` *(inicia sesión con las credenciales definidas en `.env`)*
* **PostgreSQL / PostGIS:** `localhost:5432` (accesible desde QGIS, DBeaver, `psql` o directamente desde R)

### Ejecutar flujos de trabajo desde RStudio o terminal

* **Ingesta de datos hacia PostGIS:**
  ```r
  source("data-raw/etl_ingesta_postgis.R")
  ```

* **Renderizar reportes en RMarkdown:**
  ```r
  rmarkdown::render("reports/analisis_climatico.Rmd", output_dir = "reports/output")
  ```

### Detener los servicios
Para detener los contenedores conservando los volúmenes de datos de PostgreSQL:
```bash
docker compose down
```

## Fuentes de datos

* **Nombre de la fuente (ej. NOAA / Copernicus Marine / CONAGUA):** [Enlace a la fuente](https://example.com) — Descripción breve de las variables, resolución espacial y cobertura temporal.

## Contribución

Las contribuciones, sugerencias y reportes de errores son bienvenidos. Por favor abre un *issue* para discutir cambios importantes antes de enviar un *pull request*.

## Licencia

Este proyecto está bajo la [Licencia MIT](LICENSE) — consulta el archivo `LICENSE` para más detalles.

