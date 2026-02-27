# 🏗️ Infra — Orquestación Central

Este directorio contiene el `docker-compose.yml` que levanta **todo el sistema** usando imágenes pre-construidas de GHCR.

## Requisitos

- Docker Engine ≥ 24
- Docker Compose v2 (o `docker-compose` v1.29+)

## Quick Start

```bash
# 1. Copiar variables de entorno
cp .env.example .env

# 2. (Opcional) Editar .env para cambiar image tags, passwords, etc.

# 3. Levantar todo
docker-compose up -d

# 4. Ver logs
docker-compose logs -f

# 5. Bajar todo (sin borrar datos)
docker-compose down

# 6. Bajar todo Y borrar volúmenes (reset completo)
docker-compose down -v
```

## Mapeo de Puertos

| Servicio | URL local | Puerto interno |
|----------|-----------|---------------|
| Frontend | http://localhost:3000 | 80 (nginx) |
| Ticket Service | http://localhost:8000 | 8000 |
| Notification Service | http://localhost:8001 | 8001 |
| Assignment Service | http://localhost:8002 | 8001 |
| Users Service | http://localhost:8003 | 8001 |
| RabbitMQ Management | http://localhost:15672 | 15672 |
| Ticket DB | localhost:5432 | 5432 |
| Assignment DB | localhost:5433 | 5432 |
| Notification DB | localhost:5434 | 5432 |
| Users DB | localhost:5435 | 5432 |

## Contenedores

El compose levanta **14 contenedores**:

| Contenedor | Rol |
|------------|-----|
| `infra-rabbitmq` | Broker de mensajería |
| `infra-ticket-db` | PostgreSQL para tickets |
| `infra-users-db` | PostgreSQL para users |
| `infra-notification-db` | PostgreSQL para notifications |
| `infra-assignment-db` | PostgreSQL para assignments |
| `infra-ticket-service` | API REST de tickets (Django) |
| `infra-users-service` | API REST de users (Django) |
| `infra-notification-service` | API REST de notifications (Django + SSE) |
| `infra-assignment-service` | API REST de assignments (Django) |
| `infra-users-consumer` | Consumer RabbitMQ (users) |
| `infra-notification-consumer` | Consumer RabbitMQ (notifications) |
| `infra-assignment-consumer` | Consumer RabbitMQ (assignments) |
| `infra-assignment-celery` | Celery worker (assignments) |
| `infra-frontend` | SPA React (Nginx) |

## Image Tags

Por defecto usa `develop` para backends y `main` para frontend. Para la demo, podés fijar tags específicos en `.env`:

```bash
TICKET_IMAGE_TAG=sha-abc1234
USERS_IMAGE_TAG=latest
NOTIFICATION_IMAGE_TAG=develop
ASSIGNMENT_IMAGE_TAG=develop
FRONTEND_IMAGE_TAG=main
```

## Troubleshooting

```bash
# Ver estado de todos los contenedores
docker-compose ps

# Ver logs de un servicio específico
docker-compose logs -f ticket-service

# Recrear un servicio sin tocar los demás
docker-compose up -d --force-recreate ticket-service

# Conectarse a una DB
docker exec -it infra-ticket-db psql -U postgres -d sistema_tickets
```
