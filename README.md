# Centro Médico Horizonte — Proyecto integrador DevOps

Sitio estático educativo para simular reservas de horas médicas. Nginx sirve HTML y CSS; el formulario se valida en el navegador y no envía ni almacena datos. No existe API, base de datos ni despliegue externo. Centro ficticio.

## Requisitos
- Docker Desktop iniciado (contenedores Linux; WSL 2 en Windows si corresponde).
- Git y una cuenta GitHub para publicar el repositorio.
- Puerto local 8080 disponible.

## Ejecución local (PowerShell)
Desde la carpeta del proyecto:
```powershell
docker --version
docker compose version
Copy-Item .env.example .env
docker compose config
docker compose build
docker compose up -d
docker compose ps
docker ps
```
Abrir http://localhost:8080. Seleccionar especialidad, fecha de hoy o futura, horario y modalidad; pulsar Simular reserva. Debe aparecer una confirmación de simulación sin envío ni almacenamiento. Probar también campos obligatorios vacíos.

## Verificación y monitoreo
```powershell
curl.exe -I http://localhost:8080/
curl.exe -I http://localhost:8080/styles.css
docker logs --tail 30 reserva-medica-web
docker stats --no-stream reserva-medica-web
docker inspect --format '{{.State.Health.Status}}' reserva-medica-web
docker compose stop
docker compose ps -a
docker compose down
```
HTTP esperado: 200 para ambos archivos; healthcheck healthy tras unos segundos. Los logs deben mostrar peticiones GET/HEAD. CPU, memoria, red y E/S dependen del equipo y de la carga; registrar valores reales. `stop` conserva el contenedor detenido; `down` elimina contenedor y red del proyecto. Para reiniciar: `docker compose up -d`.

## Publicación y trazabilidad
Crear un repositorio público vacío en GitHub, sin README inicial. Desde PowerShell en la carpeta del proyecto (reemplazar USUARIO y correo de ejemplo por el alias no-reply de GitHub):
```powershell
git init -b main
git config user.name "Autor DevOps"
git config user.email "CORREO-NOREPLY-DE-GITHUB"
git add README.md .gitignore .env.example .dockerignore
git commit -m "docs: inicializa proyecto y reglas de seguridad"
git remote add origin https://github.com/USUARIO/proyecto-devops.git
git push -u origin main
git checkout -b feature/reserva-medica
git add src
git commit -m "feat: implementa formulario estático de reserva"
git add Dockerfile docker-compose.yml
git commit -m "build: configura Nginx y Docker Compose"
git add .github/workflows/devops-automation.yml
git commit -m "ci: valida construcción y respuesta HTTP"
git push -u origin feature/reserva-medica
```
En GitHub abrir PR desde feature/reserva-medica hacia main; revisar Files changed, verificar checks y capturar historial/PR. Si se trabaja individualmente, registrar autorrevisión como tal, no fingir aprobación independiente. Una vez validados los cambios, Merge pull request. Luego:
```powershell
git checkout main
git pull --ff-only origin main
git log --oneline --graph --all
git tag -a v1.0.0 -m "Versión local inicial"
git push origin v1.0.0
```
No borrar la rama hasta capturar evidencia. GitHub Actions valida en un runner temporal, sin publicar el servicio.

## Seguridad y decisiones
- No usar nombres, RUT, contactos, fichas clínicas, contraseñas ni tokens reales. El formulario no solicita datos identificatorios.
- `.env.example` documenta el puerto; `.env` queda ignorado. El puerto se enlaza a 127.0.0.1 para limitar acceso local. No copiar secretos al contexto de construcción.
- Revisar `git status`, `git diff --cached` y el historial antes de publicar. Si se filtra un secreto, revocarlo y retirar el historial afectado; ignorarlo no borra commits previos.
- Logs de Nginx contienen metadatos HTTP: usar solo pruebas ficticias y no publicar capturas con datos identificatorios. Rotación local configurada.
- Nginx Alpine reduce componentes; no garantiza ausencia de vulnerabilidades. En producción evaluar imagen fijada por digest, escaneo, usuario no privilegiado, TLS y controles de privacidad.

## Solución de problemas
- Motor no disponible: abrir Docker Desktop y esperar estado activo; verificar `docker info`.
- Puerto ocupado: identificar y detener el proceso que lo utiliza para mantener el requisito 8080; no usar otro puerto en la evidencia final.
- Página sin cambios: ejecutar `docker compose up -d --build` y recargar el navegador.
- Contenedor no saludable: revisar `docker compose logs web` y `docker inspect reserva-medica-web`.


## Planificación del proyecto DevOps

### Contexto
El Centro Médico Horizonte requiere un entorno reproducible
para ejecutar un servicio web de simulación de reserva de
horas médicas.

### Objetivo
Implementar una aplicación web estática utilizando HTML,
CSS y Nginx, ejecutada mediante Docker Desktop y
Docker Compose.

### Requisitos funcionales
- Mostrar un formulario de reserva médica.
- Permitir seleccionar especialidad, fecha y horario.
- Presentar información general de atención.
- Mostrar el estado del servicio.
- Permitir simular una reserva sin almacenar datos.

### Requisitos técnicos
- HTML y CSS.
- Imagen Docker nginx:alpine.
- Dockerfile.
- Docker Compose.
- Puerto local 8080.
- Control de versiones mediante Git y GitHub.
- Sin API ni base de datos.

### Seguridad
- No publicar credenciales reales.
- No incorporar datos personales de pacientes.
- Utilizar .env.example.
- Excluir archivos sensibles mediante .gitignore.

### Criterios de aceptación
1. Construcción satisfactoria de la imagen Docker.
2. Contenedor ejecutándose correctamente.
3. Aplicación accesible desde localhost:8080.
4. Formulario de simulación funcional.
5. Respuesta HTTP satisfactoria.
6. Cambios registrados mediante Git.
7. Logs y métricas disponibles para revisión.
