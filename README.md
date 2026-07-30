# Task Manager

A simple task management application built with Ruby on Rails 8.1.

> **Status:** early stage. The `Task` model and the home page exist; CRUD screens for tasks are not implemented yet.

## Stack

| Component | Choice |
| --- | --- |
| Language | Ruby 3.4.10 |
| Framework | Rails 8.1 |
| Database | SQLite 3 |
| Web server | Puma |
| Front end | Hotwire (Turbo + Stimulus), Propshaft, importmap-rails |
| Background jobs | Solid Queue |
| Cache / Cable | Solid Cache, Solid Cable |
| Deployment | Kamal + Thruster (Docker) |

## Requirements

* Ruby 3.4.10 (see `.ruby-version`; `rbenv`, `asdf` or `mise` will pick it up automatically)
* Bundler
* SQLite 3 (version 2.1 or newer of the `sqlite3` gem)
* Docker — only needed for deployment

## Getting started

```bash
git clone <repository-url>
cd task_manager
bin/setup
```

`bin/setup` installs the gems, prepares the database and starts the development server. To set things up without booting the server:

```bash
bin/setup --skip-server
```

### Running the app

```bash
bin/dev            # or: bin/rails server
```

The app is then available at <http://localhost:3000>. The health-check endpoint lives at <http://localhost:3000/up> and returns 200 when the app boots cleanly.

## Database

The application uses SQLite, with separate databases for the primary data, the cache, the queue and Action Cable (see `config/database.yml`). Files are stored under `storage/`.

```bash
bin/rails db:prepare     # create and load the schema
bin/rails db:migrate     # run pending migrations
bin/rails db:seed        # load seed data from db/seeds.rb
bin/rails db:reset       # drop, recreate and reseed
```

### Data model

| Model | Fields |
| --- | --- |
| `Task` | `title` (string), `created_at`, `updated_at` |

## Configuration

Environment-specific settings live in `config/environments/`. Secrets are managed with Rails encrypted credentials:

```bash
bin/rails credentials:edit
```

`config/master.key` is required to decrypt them and must never be committed. In production the same value is supplied through the `RAILS_MASTER_KEY` environment variable.

## Tests and checks

```bash
bin/ci                   # full pipeline: style, security, tests
bin/rails test           # unit and integration tests
bin/rails test:system    # system tests
bin/rubocop              # style (rails-omakase)
bin/brakeman             # static security analysis
bin/bundler-audit        # known vulnerabilities in dependencies
```

The pipeline run by `bin/ci` is defined in `config/ci.rb`.

## Background jobs

Solid Queue handles background jobs. It can run inside Puma (`SOLID_QUEUE_IN_PUMA=true`) or as a separate process:

```bash
bin/jobs
```

Recurring jobs are configured in `config/recurring.yml`.

## Deployment

Deployment uses [Kamal](https://kamal-deploy.org) with the Docker image built from the `Dockerfile`. Adjust the servers, registry and domain in `config/deploy.yml`, then:

```bash
bin/kamal setup      # first deployment
bin/kamal deploy     # subsequent deployments
bin/kamal logs -f    # tail the logs
bin/kamal console    # Rails console on the server
```

Secrets used at deploy time are read from `.kamal/secrets`; `RAILS_MASTER_KEY` is required. SQLite files and Active Storage uploads are persisted on a Docker volume so they survive redeployments.

## Project layout

```
app/         Models, controllers, views, Stimulus controllers
config/      Application, routes, database and deployment configuration
db/          Migrations, schema and seeds
test/        Test suite
bin/         Executable scripts (setup, dev, ci, kamal, …)
storage/     SQLite databases and local file storage
```

---

# Task Manager (Español)

Una aplicación sencilla de gestión de tareas construida con Ruby on Rails 8.1.

> **Estado:** fase inicial. El modelo `Task` y la página de inicio existen; las pantallas CRUD de tareas aún no están implementadas.

## Tecnologías

| Componente | Elección |
| --- | --- |
| Lenguaje | Ruby 3.4.10 |
| Framework | Rails 8.1 |
| Base de datos | SQLite 3 |
| Servidor web | Puma |
| Front end | Hotwire (Turbo + Stimulus), Propshaft, importmap-rails |
| Tareas en segundo plano | Solid Queue |
| Caché / Cable | Solid Cache, Solid Cable |
| Despliegue | Kamal + Thruster (Docker) |

## Requisitos

* Ruby 3.4.10 (ver `.ruby-version`; `rbenv`, `asdf` o `mise` lo detectan automáticamente)
* Bundler
* SQLite 3 (versión 2.1 o superior de la gema `sqlite3`)
* Docker — solo necesario para el despliegue

## Puesta en marcha

```bash
git clone <url-del-repositorio>
cd task_manager
bin/setup
```

`bin/setup` instala las gemas, prepara la base de datos y arranca el servidor de desarrollo. Para preparar el entorno sin arrancar el servidor:

```bash
bin/setup --skip-server
```

### Ejecutar la aplicación

```bash
bin/dev            # o bien: bin/rails server
```

La aplicación queda disponible en <http://localhost:3000>. El endpoint de estado está en <http://localhost:3000/up> y devuelve 200 cuando la aplicación arranca sin errores.

## Base de datos

La aplicación usa SQLite, con bases de datos separadas para los datos principales, la caché, la cola y Action Cable (ver `config/database.yml`). Los archivos se guardan en `storage/`.

```bash
bin/rails db:prepare     # crear y cargar el esquema
bin/rails db:migrate     # ejecutar las migraciones pendientes
bin/rails db:seed        # cargar los datos de db/seeds.rb
bin/rails db:reset       # borrar, recrear y volver a poblar
```

### Modelo de datos

| Modelo | Campos |
| --- | --- |
| `Task` | `title` (string), `created_at`, `updated_at` |

## Configuración

Los ajustes por entorno están en `config/environments/`. Los secretos se gestionan con las credenciales cifradas de Rails:

```bash
bin/rails credentials:edit
```

`config/master.key` es necesario para descifrarlas y nunca debe subirse al repositorio. En producción ese mismo valor se pasa mediante la variable de entorno `RAILS_MASTER_KEY`.

## Pruebas y verificaciones

```bash
bin/ci                   # pipeline completo: estilo, seguridad, pruebas
bin/rails test           # pruebas unitarias y de integración
bin/rails test:system    # pruebas de sistema
bin/rubocop              # estilo (rails-omakase)
bin/brakeman             # análisis estático de seguridad
bin/bundler-audit        # vulnerabilidades conocidas en las dependencias
```

El pipeline que ejecuta `bin/ci` está definido en `config/ci.rb`.

## Tareas en segundo plano

Solid Queue se encarga de los trabajos en segundo plano. Puede ejecutarse dentro de Puma (`SOLID_QUEUE_IN_PUMA=true`) o como proceso independiente:

```bash
bin/jobs
```

Los trabajos recurrentes se configuran en `config/recurring.yml`.

## Despliegue

El despliegue se realiza con [Kamal](https://kamal-deploy.org) usando la imagen Docker construida a partir del `Dockerfile`. Ajusta los servidores, el registro y el dominio en `config/deploy.yml` y después:

```bash
bin/kamal setup      # primer despliegue
bin/kamal deploy     # despliegues posteriores
bin/kamal logs -f    # ver los logs en tiempo real
bin/kamal console    # consola de Rails en el servidor
```

Los secretos del despliegue se leen desde `.kamal/secrets`; `RAILS_MASTER_KEY` es obligatorio. Los archivos de SQLite y las subidas de Active Storage se guardan en un volumen de Docker para que sobrevivan a los redespliegues.

## Estructura del proyecto

```
app/         Modelos, controladores, vistas y controladores Stimulus
config/      Configuración de la aplicación, rutas, base de datos y despliegue
db/          Migraciones, esquema y semillas
test/        Suite de pruebas
bin/         Scripts ejecutables (setup, dev, ci, kamal, …)
storage/     Bases de datos SQLite y almacenamiento local de archivos
```
