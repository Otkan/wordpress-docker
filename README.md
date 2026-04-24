# WordPress with Docker Compose

## Table of Contents

* [Description](#description)
* [Repository Contents](#repository-contents)
* [Requirements](#requirements)
* [Quickstart](#quickstart)
* [Usage](#usage)
* [Configuration](#configuration)
* [Access](#access)
* [Persistence](#persistence)
* [Testing](#testing)
* [Security Notes](#security-notes)

---

## Description

This repository contains a Docker Compose based setup for running a self-hosted WordPress instance with a MariaDB database.

The project includes:

* a `docker-compose.yaml` file with two services: `wordpress` and `db`
* a `.gitignore` file to exclude irrelevant and sensitive local files
* a `README.md` file for project documentation
* an `env.example` file that documents the required environment variables without exposing real secrets

The purpose of this repository is to provide a simple, reproducible, and documented way to run WordPress in containers while keeping sensitive configuration outside the repository.

---

## Repository Contents

* `docker-compose.yaml` – defines the `wordpress` and `db` services
* `.gitignore` – excludes `.env` and local OS-specific files
* `README.md` – project documentation
* `env.example` – example environment variable file without real credentials

No additional files are required for the basic setup unless they are explicitly documented.

---

## Requirements

* Docker
* Docker Compose
* A local machine, VPS, or cloud VM
* Port `8080` available on the host system

---

## Quickstart

1. Copy the example environment file:

```bash
cp env.example .env
```

2. Edit `.env` and set a secure database password.

3. Start the project:

```bash
docker compose up -d
```

4. Open WordPress in your browser:

```text
http://localhost:8080
```

---

## Usage

### Start the services

```bash
docker compose up -d
```

### Stop the services

```bash
docker compose down
```

### Restart the services

```bash
docker compose restart
```

### Show running containers

```bash
docker compose ps
```

### View logs

```bash
docker compose logs -f
```

### Rebuild and restart

```bash
docker compose up -d --build
```

---

## Configuration

The setup uses environment variables for configuration.

### Example environment file

```env
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress
MYSQL_PASSWORD=your_secure_password_here
WORDPRESS_DB_HOST=db:3306
WORDPRESS_PORT=8080
```

### Variables

| Variable            | Description                                      | Example                     |
| ------------------- | ------------------------------------------------ | --------------------------- |
| `MYSQL_DATABASE`    | Name of the WordPress database                   | `wordpress`                 |
| `MYSQL_USER`        | Database user for WordPress                      | `wordpress`                 |
| `MYSQL_PASSWORD`    | Database password                                | `your_secure_password_here` |
| `WORDPRESS_DB_HOST` | Database host and port inside the Docker network | `db:3306`                   |
| `WORDPRESS_PORT`    | Host port used to expose WordPress               | `8080`                      |

### Notes on configuration

* Sensitive values such as passwords must not be stored directly in `docker-compose.yaml`.
* Real secrets should be stored in a local `.env` file.
* The `.env` file is ignored by Git.
* Default values are only used where they make sense, such as for the database name, WordPress port, and database host.

---

## Access

### WordPress site

Open the website in your browser:

```text
http://localhost:8080
```

### WordPress admin panel

Open the admin area here:

```text
http://localhost:8080/wp-admin
```

Log in with the WordPress admin credentials created during the initial browser-based setup.

For deployment on a cloud VM, replace `localhost` with the VM IP address:

```text
http://<YOUR_SERVER_IP>:8080
http://<YOUR_SERVER_IP>:8080/wp-admin
```

---

## Persistence

Database data is stored in the Docker volume `db_data`.

This ensures that WordPress content and configuration stored in the database are preserved across restarts.

As a result:

* created posts and pages remain available after restarting the setup
* login data and WordPress settings are not lost
* the database is not reinitialized on every restart

---

## Testing

### 1. Reachability

WordPress should be reachable at:

```text
http://localhost:8080
```

On a cloud VM, WordPress should be reachable at:

```text
http://<YOUR_SERVER_IP>:8080
```

### 2. Admin login

The admin panel is available at:

```text
http://localhost:8080/wp-admin
```

A successful login with the configured WordPress admin credentials confirms that the application is working correctly.

### 3. Persistence test

To test persistence:

1. Create a post or page in the WordPress admin panel.
2. Restart the setup:

```bash
docker compose restart
```

3. Open WordPress again and verify that the content still exists.

This confirms that configured data remains available after a restart.

### 4. Automatic restart test

The services are configured with:

```yaml
restart: always
```

To simulate a failure, terminate the main process inside one of the containers.

Example:

```bash
docker exec wordpress-db pkill mariadbd
docker exec wordpress-app pkill -f apache2
```

Then verify that the containers are started again automatically:

```bash
docker compose ps
```

This confirms that the services restart automatically after a failure.

---

## Security Notes

* Do not store passwords, tokens, usernames, SSH keys, IP addresses, or other sensitive data in the repository.
* Use a local `.env` file for secrets.
* Commit `env.example`, but never commit the real `.env`.
* The `.gitignore` file excludes `.env` and local system files.

---
