## Tecnologías

- HTML
- CSS
- Nginx Alpine
- Docker
- Docker Compose
- Git
- GitHub

## Requisitos

- Docker Desktop instalado.
- Docker Compose disponible.
- Navegador web.

## Instalación

Clonar el repositorio:
git clone https://github.com/USUARIO/Devops_1.git
Ingresar al directorio:
cd Devops_1
Crear configuración local en CMD:
copy .env.example .env

## Ejecución
docker compose up -d --build
## Verificación

Abrir:
http://localhost:8080
Consultar contenedores:
docker compose ps
Consultar logs:
docker compose logs --tail 30
Consultar métricas:
docker stats --no-stream

## Detención
docker compose stop

## Seguridad

No publicar contraseñas, tokens, credenciales,
datos de pacientes ni archivos .env reales.

El formulario realiza exclusivamente una simulación
y no almacena reservas ni información personal.
