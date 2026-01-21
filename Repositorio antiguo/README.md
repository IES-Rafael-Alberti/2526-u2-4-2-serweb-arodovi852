# Practica 4.1 - Instalacion y Configuracion de Servidor Web Nginx con Docker

## Descripcion del Proyecto

Esta practica implementa una infraestructura de servidor web Nginx contenerizada con Docker, incluyendo:
- Servidor web Nginx con soporte HTTPS
- Servidor SFTP para transferencia de archivos
- Volumenes compartidos entre contenedores
- Certificados SSL autofirmados
- Redireccion automatica de HTTP a HTTPS

## Estructura del Proyecto

```
InstalacionNginx/
|-- docker-compose.yml          # Configuracion de Docker Compose
|-- nginx/
|   |-- default.conf            # Configuracion de Nginx con SSL
|-- certs/
|   |-- nginx-selfsigned.crt    # Certificado SSL autofirmado
|   |-- nginx-selfsigned.key    # Clave privada SSL
|-- web/
|   |-- index.html              # Web principal (Cloud Academy)
|   |-- assets/                 # Recursos estaticos
|   |-- images/                 # Imagenes
|   |-- reloj/                  # Web secundaria (Reloj)
|-- evidencias/                 # Capturas de pantalla para evaluacion
|   |-- 01_docker_compose_ps.png
|   |-- 02_nginx_config.png
|   |-- 03_hosts_resolucion.png
|   |-- 04_web_cloudacademy.png
|   |-- 05_filezilla_conexion.png
|   |-- 06_filezilla_permisos.png
|   |-- 07_contenedores_activos.png
|   |-- 08_volumen_compartido.png
|   |-- 09_web_reloj.png
|   |-- 10_https_ssl.png
|   |-- 11_redireccion_301.png
|-- README.md                   # Este archivo
|-- Descripcion.md              # Descripcion de la practica
```

## Requisitos Previos

- Docker Desktop instalado y en ejecucion
- Cliente FTP (Filezilla recomendado)
- Navegador web

## Instrucciones de Uso

### 1. Iniciar la Infraestructura

```powershell
cd "c:\Users\alber\Desktop\esizón\InstalacionNginx"
docker compose up -d
```

### 2. Verificar Estado de los Contenedores

```powershell
docker compose ps
```

Deberia mostrar:
- nginx-web: Up, puertos 8080->80, 8443->443
- sftp-server: Up, puerto 2222->22

### 3. Acceso a las Webs

- **Web Principal (HTTPS)**: https://localhost:8443
- **Web del Reloj**: https://localhost:8443/reloj
- **HTTP (redirige a HTTPS)**: http://localhost:8080

**Nota**: Al acceder por HTTPS, el navegador mostrara una advertencia de certificado autofirmado. Es seguro continuar.

### 4. Configuracion del Archivo Hosts (Opcional)

Para acceder mediante nombre en lugar de IP, editar como administrador:
`C:\Windows\System32\drivers\etc\hosts`

Agregar la linea:
```
127.0.0.1       nombre_web
```

### 5. Conexion SFTP con Filezilla

Configuracion de conexion:
- **Host**: sftp://localhost
- **Puerto**: 2222
- **Usuario**: usuario
- **Contrasena**: password
- **Protocolo**: SFTP

Los archivos subidos a `/home/usuario/upload` se veran inmediatamente en la web.

### 6. Detener la Infraestructura

```powershell
docker compose down
```

## Configuracion Tecnica

### Docker Compose

El archivo `docker-compose.yml` define:
- **Servicio nginx**: Imagen oficial nginx:latest, puertos 8080 (HTTP) y 8443 (HTTPS)
- **Servicio sftp**: Imagen atmoz/sftp, puerto 2222
- **Volumen compartido**: Carpeta `./web` montada en ambos contenedores
- **Red**: Red bridge personalizada para comunicacion entre contenedores

### Configuracion Nginx (default.conf)

- **Puerto 80**: Redireccion 301 a HTTPS
- **Puerto 443**: Servidor SSL con certificado autofirmado
- **Protocolos SSL**: TLSv1.2 y TLSv1.3
- **Ubicaciones**: Raiz `/` y subcarpeta `/reloj`

### Certificados SSL

Generados con OpenSSL:
- Validez: 365 dias
- Algoritmo: RSA 2048 bits
- Ruta en contenedor: `/etc/ssl/certs/`

---

## Checklist de Evaluacion y Evidencias

A continuacion se presenta la checklist de evaluacion con los placeholders para las capturas de pantalla requeridas. Las imagenes deben guardarse en la carpeta `evidencias/` y nombrarse segun se indica.

### Fase 1: Instalacion y Configuracion

#### Requisito 1: Servicio Nginx activo
**Estado**: [COMPLETADO]

**Descripcion**: El servidor web esta instalado y corriendo en el sistema base.

**Evidencia requerida**: Captura del comando `docker compose ps` mostrando el servicio nginx-web con estado Up.

**Como obtener la captura**:
1. Abrir PowerShell
2. Ejecutar: `docker compose ps`
3. Capturar la pantalla mostrando ambos contenedores activos

![Evidencia 1 - Docker Compose PS](evidencias/01_docker_compose_ps.png)

---

#### Requisito 2: Configuracion cargada
**Estado**: [COMPLETADO]

**Descripcion**: Se ha cargado el archivo de configuracion del sitio en Nginx.

**Evidencia requerida**: Captura listando el directorio de configuracion dentro del contenedor donde se vea el archivo default.conf.

**Como obtener la captura**:
1. Abrir PowerShell
2. Ejecutar: `docker exec nginx-web ls -l /etc/nginx/conf.d/`
3. Capturar mostrando el archivo default.conf listado

![Evidencia 2 - Configuracion Nginx](evidencias/02_nginx_config.png)

---

#### Requisito 3: Resolucion de nombres
**Estado**: [PENDIENTE - Requiere permisos de administrador]

**Descripcion**: El alumno ha configurado el archivo hosts de Windows para usar un nombre en vez de la IP.

**Evidencia requerida**: Captura del navegador web donde la barra de direcciones muestre http://nombre_web (no la IP) y se vea la pagina cargada.

**Como obtener la captura**:
1. Abrir PowerShell como Administrador
2. Ejecutar: `Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "127.0.0.1       nombre_web"`
3. Abrir navegador y acceder a: http://nombre_web:8080
4. Capturar mostrando la URL con el nombre y la web cargada

![Evidencia 3 - Resolucion de Nombres](evidencias/03_hosts_resolucion.png)

---

#### Requisito 4: Contenido Web Cloud Academy
**Estado**: [COMPLETADO]

**Descripcion**: Se visualiza la web de "Cloud Academy" en lugar de la pagina por defecto de Nginx.

**Evidencia requerida**: Captura del navegador mostrando claramente el diseno de la web importada de Cloud Academy.

**Como obtener la captura**:
1. Abrir navegador
2. Acceder a: https://localhost:8443
3. Aceptar advertencia de certificado autofirmado
4. Capturar mostrando la web de Cloud Academy completa

![Evidencia 4 - Web Cloud Academy](evidencias/04_web_cloudacademy.png)

---

### Fase 2: Transferencia SFTP (Filezilla)

#### Requisito 5: Conexion SFTP exitosa
**Estado**: [COMPLETADO]

**Descripcion**: Conexion establecida mediante usuario/password al servidor SFTP.

**Evidencia requerida**: Captura de Filezilla mostrando en el panel de registro "Status: Connected to localhost" y en el panel derecho el listado de carpetas remoto.

**Como obtener la captura**:
1. Abrir Filezilla
2. Conectar con los siguientes datos:
   - Host: sftp://localhost
   - Puerto: 2222
   - Usuario: usuario
   - Contrasena: password
3. Capturar mostrando la conexion exitosa y el listado de carpetas

![Evidencia 5 - Conexion SFTP Filezilla](evidencias/05_filezilla_conexion.png)

---

#### Requisito 6: Permisos de escritura
**Estado**: [COMPLETADO]

**Descripcion**: El usuario ha logrado subir archivos sin error de "Permission denied".

**Evidencia requerida**: Captura de Filezilla mostrando la transferencia completada o los archivos ya presentes en /home/usuario/upload.

**Como obtener la captura**:
1. En Filezilla, navegar a /home/usuario/upload en el panel remoto
2. Subir cualquier archivo de prueba desde el panel local
3. Capturar mostrando el mensaje de transferencia exitosa en el log

![Evidencia 6 - Permisos Escritura SFTP](evidencias/06_filezilla_permisos.png)

---

### Fase 3: Infraestructura Docker

#### Requisito 7: Contenedores activos
**Estado**: [COMPLETADO]

**Descripcion**: Nginx y SFTP estan corriendo simultaneamente.

**Evidencia requerida**: Captura del comando `docker compose ps` donde se vean los dos servicios con estado Up y los puertos mapeados correctamente.

**Como obtener la captura**:
1. Abrir PowerShell
2. Ejecutar: `docker compose ps`
3. Capturar mostrando:
   - nginx-web: Up, puertos 0.0.0.0:8080->80/tcp y 0.0.0.0:8443->443/tcp
   - sftp-server: Up, puerto 0.0.0.0:2222->22/tcp

![Evidencia 7 - Contenedores Activos](evidencias/07_contenedores_activos.png)

---

#### Requisito 8: Persistencia (Volumen Compartido)
**Estado**: [COMPLETADO]

**Descripcion**: Lo que se sube al SFTP se ve en la Web inmediatamente.

**Evidencia requerida**: Captura cruzada con Filezilla abierto a un lado (mostrando archivos en /upload) y el navegador al otro (mostrando localhost:8080), demostrando que son los mismos archivos.

**Como obtener la captura**:
1. Abrir Filezilla conectado al SFTP
2. Abrir navegador con https://localhost:8443
3. Colocar ambas ventanas lado a lado
4. Capturar mostrando los mismos archivos en ambos

![Evidencia 8 - Volumen Compartido](evidencias/08_volumen_compartido.png)

---

#### Requisito 9: Despliegue Multi-sitio
**Estado**: [COMPLETADO]

**Descripcion**: Se ha desplegado la segunda web (Reloj) en una subcarpeta /reloj.

**Evidencia requerida**: Captura del navegador en la ruta https://localhost:8443/reloj mostrando el reloj funcionando.

**Como obtener la captura**:
1. Abrir navegador
2. Acceder a: https://localhost:8443/reloj
3. Capturar mostrando el reloj digital funcionando

![Evidencia 9 - Web del Reloj](evidencias/09_web_reloj.png)

---

### Fase 4: Seguridad HTTPS

#### Requisito 10: Cifrado SSL
**Estado**: [COMPLETADO]

**Descripcion**: El servidor responde a peticiones seguras mediante HTTPS.

**Evidencia requerida**: Captura del navegador accediendo por https:// mostrando el candado (o la alerta de certificado autofirmado) y el puerto configurado.

**Como obtener la captura**:
1. Abrir navegador
2. Acceder a: https://localhost:8443
3. Capturar mostrando:
   - La URL con https://
   - El icono de candado o advertencia de certificado
   - La web cargada correctamente

![Evidencia 10 - Cifrado SSL](evidencias/10_https_ssl.png)

---

#### Requisito 11: Redireccion Forzada HTTP a HTTPS
**Estado**: [COMPLETADO]

**Descripcion**: Al acceder por HTTP, el servidor redirige automaticamente a HTTPS con codigo 301.

**Evidencia requerida**: Captura de la pestana "Red" (Network) de las herramientas de desarrollador (F12) mostrando un codigo de estado 301 Moved Permanently.

**Como obtener la captura**:
1. Abrir navegador
2. Presionar F12 para abrir herramientas de desarrollador
3. Ir a la pestana "Red" o "Network"
4. Acceder a: http://localhost:8080
5. Capturar mostrando la peticion con codigo 301 Moved Permanently

![Evidencia 11 - Redireccion 301](evidencias/11_redireccion_301.png)

---

## Resumen de Imagenes Requeridas

| Archivo | Descripcion | Requisito |
|---------|-------------|-----------|
| `evidencias/01_docker_compose_ps.png` | Salida de docker compose ps | 1 |
| `evidencias/02_nginx_config.png` | Listado de /etc/nginx/conf.d/ | 2 |
| `evidencias/03_hosts_resolucion.png` | Navegador con http://nombre_web | 3 |
| `evidencias/04_web_cloudacademy.png` | Web de Cloud Academy cargada | 4 |
| `evidencias/05_filezilla_conexion.png` | Filezilla conectado al SFTP | 5 |
| `evidencias/06_filezilla_permisos.png` | Transferencia exitosa en Filezilla | 6 |
| `evidencias/07_contenedores_activos.png` | Ambos contenedores Up con puertos | 7 |
| `evidencias/08_volumen_compartido.png` | Filezilla + Navegador lado a lado | 8 |
| `evidencias/09_web_reloj.png` | Reloj funcionando en /reloj | 9 |
| `evidencias/10_https_ssl.png` | Navegador con HTTPS y candado | 10 |
| `evidencias/11_redireccion_301.png` | DevTools mostrando codigo 301 | 11 |

---

## Comandos Utiles para Evidencias

```powershell
# 1. Estado de contenedores (Requisito 1 y 7)
docker compose ps

# 2. Verificar configuracion Nginx (Requisito 2)
docker exec nginx-web ls -l /etc/nginx/conf.d/

# 3. Ver contenido de configuracion Nginx
docker exec nginx-web cat /etc/nginx/conf.d/default.conf

# 4. Verificar logs de Nginx
docker logs nginx-web

# 5. Verificar logs de SFTP
docker logs sftp-server

# 6. Verificar redireccion HTTP a HTTPS (Requisito 11)
# Abrir herramientas de desarrollador (F12) > Pestana Red
# Navegar a http://localhost:8080
# Observar codigo 301 Moved Permanently

# 7. Reiniciar servicios
docker compose restart

# 8. Detener y eliminar contenedores
docker compose down

# 9. Ver contenido del volumen web
docker exec nginx-web ls -la /usr/share/nginx/html/
```

## Notas Adicionales

1. **Certificado Autofirmado**: El navegador mostrara una advertencia de seguridad. Hacer clic en "Avanzado" y "Continuar" para acceder.

2. **Persistencia de Datos**: Los archivos web se almacenan en la carpeta `./web` del proyecto, por lo que persisten incluso si se eliminan los contenedores.

3. **Permisos SFTP**: El usuario SFTP tiene UID 1001 para garantizar permisos de escritura en el volumen compartido.

4. **Puertos Utilizados**:
   - 8080: HTTP (redirige a HTTPS)
   - 8443: HTTPS
   - 2222: SFTP

---

## Autor

Practica realizada para el modulo de Despliegue de Aplicaciones Web.

---

## GUIA PASO A PASO COMPLETA PARA WINDOWS

Esta seccion contiene todos los pasos necesarios para replicar la practica desde cero en Windows y obtener todas las capturas de pantalla requeridas.

### REQUISITOS PREVIOS

Antes de comenzar, asegurate de tener instalado:

1. **Docker Desktop para Windows**
   - Descargar de: https://www.docker.com/products/docker-desktop/
   - Instalarlo y reiniciar el equipo si es necesario
   - Asegurarse de que Docker Desktop este en ejecucion (icono en la barra de tareas)

2. **Git para Windows**
   - Descargar de: https://git-scm.com/download/win
   - Instalarlo con las opciones por defecto

3. **Filezilla Client**
   - Descargar de: https://filezilla-project.org/download.php?type=client
   - Instalarlo

4. **Navegador web** (Chrome, Firefox, Edge, etc.)

---

### PASO 1: PREPARAR EL ENTORNO DE TRABAJO

Abrir PowerShell y ejecutar los siguientes comandos:

```powershell
# 1.1 Crear la carpeta del proyecto
mkdir "C:\Users\$env:USERNAME\Desktop\InstalacionNginx"
cd "C:\Users\$env:USERNAME\Desktop\InstalacionNginx"

# 1.2 Crear la estructura de carpetas
mkdir web, nginx, certs, evidencias

# 1.3 Verificar que Docker esta funcionando
docker version
```

Si Docker no responde, iniciar Docker Desktop desde el menu de inicio.

---

### PASO 2: CREAR EL ARCHIVO DOCKER-COMPOSE.YML

```powershell
# 2.1 Crear el archivo docker-compose.yml
@"
services:
  # Servicio Web Nginx
  nginx:
    image: nginx:latest
    container_name: nginx-web
    ports:
      - "8080:80"    # HTTP
      - "8443:443"   # HTTPS
    volumes:
      - ./web:/usr/share/nginx/html:ro
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro
      - ./certs:/etc/ssl/certs:ro
    depends_on:
      - sftp
    restart: unless-stopped
    networks:
      - webnet

  # Servicio SFTP para transferencia de archivos
  sftp:
    image: atmoz/sftp
    container_name: sftp-server
    ports:
      - "2222:22"
    volumes:
      - ./web:/home/usuario/upload
    command: usuario:password:1001
    restart: unless-stopped
    networks:
      - webnet

networks:
  webnet:
    driver: bridge
"@ | Out-File -FilePath "docker-compose.yml" -Encoding UTF8
```

---

### PASO 3: CREAR LA CONFIGURACION DE NGINX CON SSL

```powershell
# 3.1 Crear el archivo de configuracion de Nginx
@"
# Configuracion Nginx con SSL y redireccion HTTP a HTTPS

# Bloque servidor HTTP - Redirige todo el trafico a HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name localhost nombre_web;
    
    # Redireccion permanente a HTTPS
    return 301 https://`$host:8443`$request_uri;
}

# Bloque servidor HTTPS - Sirve el contenido seguro
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name localhost nombre_web;

    # Certificados SSL
    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/certs/nginx-selfsigned.key;

    # Configuracion SSL recomendada
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # Directorio raiz del sitio web
    root /usr/share/nginx/html;
    index index.html index.htm;

    # Logs especificos
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Ubicacion principal
    location / {
        try_files `$uri `$uri/ =404;
    }

    # Ubicacion para la subcarpeta /reloj
    location /reloj {
        alias /usr/share/nginx/html/reloj;
        try_files `$uri `$uri/ =404;
    }

    # Denegar acceso a archivos ocultos
    location ~ /\. {
        deny all;
    }
}
"@ | Out-File -FilePath "nginx/default.conf" -Encoding UTF8
```

---

### PASO 4: GENERAR LOS CERTIFICADOS SSL

```powershell
# 4.1 Generar certificados SSL usando Docker (ya que OpenSSL no viene en Windows)
docker run --rm -v "${PWD}/certs:/certs" alpine/openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /certs/nginx-selfsigned.key -out /certs/nginx-selfsigned.crt -subj "/C=ES/ST=Valencia/L=Valencia/O=Practica/OU=IT/CN=localhost"

# 4.2 Verificar que se han creado los certificados
Get-ChildItem certs
```

Deberia mostrar:
- nginx-selfsigned.crt
- nginx-selfsigned.key

---

### PASO 5: DESCARGAR EL CONTENIDO WEB

```powershell
# 5.1 Clonar el repositorio de la web principal (Cloud Academy)
git clone https://github.com/cloudacademy/static-website-example.git temp_web

# 5.2 Mover el contenido a la carpeta web
Move-Item -Path "temp_web\*" -Destination "web\" -Force
Remove-Item -Path "temp_web" -Recurse -Force

# 5.3 Clonar el repositorio del reloj en la subcarpeta /reloj
git clone https://github.com/ArchiDep/static-clock-website.git web/reloj

# 5.4 Verificar el contenido
Get-ChildItem web
Get-ChildItem web/reloj
```

---

### PASO 6: LEVANTAR LA INFRAESTRUCTURA DOCKER

```powershell
# 6.1 Iniciar los contenedores
docker compose up -d

# 6.2 Esperar unos segundos a que arranquen
Start-Sleep -Seconds 5

# 6.3 Verificar que estan funcionando
docker compose ps
```

---

### PASO 7: CONFIGURAR EL ARCHIVO HOSTS (Requiere Administrador)

```powershell
# 7.1 Abrir PowerShell como ADMINISTRADOR y ejecutar:
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "`n127.0.0.1       nombre_web"

# 7.2 Verificar que se ha anadido
Get-Content "C:\Windows\System32\drivers\etc\hosts" | Select-String "nombre_web"
```

**IMPORTANTE**: Este paso requiere ejecutar PowerShell como Administrador:
1. Click derecho en el icono de PowerShell
2. Seleccionar "Ejecutar como administrador"
3. Ejecutar el comando

---

### PASO 8: OBTENER LAS CAPTURAS DE PANTALLA

A continuacion se describen los pasos exactos para obtener cada captura:

---

#### CAPTURA 1: Docker Compose PS (Requisitos 1 y 7)

```powershell
# Ejecutar en PowerShell:
docker compose ps
```

**Que capturar**: La ventana de PowerShell mostrando:
- nginx-web con estado "Up" y puertos 8080->80, 8443->443
- sftp-server con estado "Up" y puerto 2222->22

**Guardar como**: `evidencias/01_docker_compose_ps.png`

---

#### CAPTURA 2: Configuracion Nginx cargada (Requisito 2)

```powershell
# Ejecutar en PowerShell:
docker exec nginx-web ls -l /etc/nginx/conf.d/
```

**Que capturar**: La salida mostrando el archivo `default.conf` listado

**Guardar como**: `evidencias/02_nginx_config.png`

---

#### CAPTURA 3: Resolucion de nombres (Requisito 3)

1. Abrir el navegador web
2. Escribir en la barra de direcciones: `http://nombre_web:8080`
3. Aceptar la redireccion a HTTPS si aparece advertencia de certificado

**Que capturar**: El navegador mostrando:
- La URL con "nombre_web" (no localhost ni IP)
- La web de Cloud Academy cargada

**Guardar como**: `evidencias/03_hosts_resolucion.png`

---

#### CAPTURA 4: Web de Cloud Academy (Requisito 4)

1. Abrir el navegador web
2. Ir a: `https://localhost:8443`
3. Si aparece advertencia de certificado, hacer clic en "Avanzado" y "Continuar"

**Que capturar**: La pagina web de Cloud Academy completamente cargada

**Guardar como**: `evidencias/04_web_cloudacademy.png`

---

#### CAPTURA 5: Conexion SFTP en Filezilla (Requisito 5)

1. Abrir Filezilla
2. En la barra superior introducir:
   - **Servidor**: `sftp://localhost`
   - **Nombre de usuario**: `usuario`
   - **Contrasena**: `password`
   - **Puerto**: `2222`
3. Hacer clic en "Conexion rapida"
4. Si pregunta por la clave del host, aceptar

**Que capturar**: Filezilla mostrando:
- En el panel superior: "Estado: Conectado a localhost"
- En el panel derecho (Sitio remoto): La carpeta `/home/usuario/upload` con los archivos

**Guardar como**: `evidencias/05_filezilla_conexion.png`

---

#### CAPTURA 6: Permisos de escritura SFTP (Requisito 6)

1. En Filezilla, ya conectado al servidor
2. En el panel izquierdo (Sitio local), navegar a cualquier carpeta con archivos
3. Arrastrar un archivo al panel derecho (Sitio remoto) en `/home/usuario/upload`
4. O hacer clic derecho en un archivo y seleccionar "Subir"

**Que capturar**: Filezilla mostrando:
- El log con mensaje de transferencia exitosa
- El archivo apareciendo en el panel remoto

**Guardar como**: `evidencias/06_filezilla_permisos.png`

---

#### CAPTURA 7: Contenedores activos (Requisito 7)

```powershell
# Ejecutar en PowerShell:
docker compose ps
```

**Que capturar**: Igual que la captura 1, asegurandose de que se vean claramente:
- Los dos servicios con estado "Up"
- Los puertos mapeados: 8080->80, 8443->443, 2222->22

**Guardar como**: `evidencias/07_contenedores_activos.png`

**Nota**: Puede ser la misma captura que la 1 si se ven todos los datos.

---

#### CAPTURA 8: Volumen compartido (Requisito 8)

1. Abrir Filezilla conectado al servidor (ver captura 5)
2. Navegar a `/home/usuario/upload` en el panel remoto
3. Abrir el navegador en `https://localhost:8443`
4. Colocar las dos ventanas lado a lado (Filezilla a la izquierda, navegador a la derecha)

**Que capturar**: Ambas ventanas mostrando que los archivos son los mismos:
- Filezilla mostrando los archivos en el servidor
- El navegador mostrando la web servida por esos archivos

**Guardar como**: `evidencias/08_volumen_compartido.png`

---

#### CAPTURA 9: Web del reloj (Requisito 9)

1. Abrir el navegador web
2. Ir a: `https://localhost:8443/reloj`
3. Aceptar advertencia de certificado si aparece

**Que capturar**: El reloj digital funcionando (mostrando la hora actual)

**Guardar como**: `evidencias/09_web_reloj.png`

---

#### CAPTURA 10: Cifrado SSL/HTTPS (Requisito 10)

1. Abrir el navegador web
2. Ir a: `https://localhost:8443`
3. Hacer clic en el icono del candado o advertencia junto a la URL

**Que capturar**: El navegador mostrando:
- La URL con `https://`
- El candado o la advertencia de certificado autofirmado
- Informacion del certificado si es posible

**Guardar como**: `evidencias/10_https_ssl.png`

---

#### CAPTURA 11: Redireccion HTTP a HTTPS (Requisito 11)

1. Abrir el navegador web (preferiblemente Chrome o Firefox)
2. Presionar F12 para abrir las Herramientas de desarrollador
3. Ir a la pestana "Red" o "Network"
4. Marcar la casilla "Conservar registro" o "Preserve log"
5. En la barra de direcciones, escribir: `http://localhost:8080`
6. Presionar Enter

**Que capturar**: La pestana Red/Network mostrando:
- La primera peticion a `http://localhost:8080` con codigo `301 Moved Permanently`
- La redireccion a `https://localhost:8443`

**Guardar como**: `evidencias/11_redireccion_301.png`

---

### COMANDOS UTILES ADICIONALES

```powershell
# Ver logs del contenedor Nginx
docker logs nginx-web

# Ver logs del contenedor SFTP
docker logs sftp-server

# Entrar dentro del contenedor Nginx
docker exec -it nginx-web /bin/bash

# Ver el contenido web desde dentro del contenedor
docker exec nginx-web ls -la /usr/share/nginx/html/

# Reiniciar los contenedores
docker compose restart

# Detener los contenedores
docker compose stop

# Eliminar los contenedores (los datos en ./web se mantienen)
docker compose down

# Eliminar todo incluyendo volumenes
docker compose down -v

# Reconstruir y reiniciar
docker compose down
docker compose up -d
```

---

### SOLUCION DE PROBLEMAS COMUNES

#### Error: "docker: command not found"
- Asegurarse de que Docker Desktop esta instalado e iniciado
- Reiniciar PowerShell despues de instalar Docker

#### Error: "port already in use"
- Algun otro servicio esta usando el puerto 8080, 8443 o 2222
- Cambiar los puertos en docker-compose.yml (ej: 9080:80)

#### Error: "Permission denied" en Filezilla
- Verificar que se esta conectando a la carpeta `/home/usuario/upload`
- No intentar escribir en `/` o `/home/usuario` directamente

#### La web no carga o muestra error 502
- Ejecutar `docker compose logs nginx` para ver errores
- Verificar que los archivos estan en la carpeta `web/`

#### El certificado no es aceptado
- Es normal con certificados autofirmados
- Hacer clic en "Avanzado" y "Continuar de todos modos"

#### El archivo hosts no se puede editar
- Ejecutar PowerShell como Administrador
- O editar manualmente: Abrir Notepad como Administrador y abrir `C:\Windows\System32\drivers\etc\hosts`

---

### ESTRUCTURA FINAL DEL PROYECTO

Despues de completar todos los pasos, la estructura debe ser:

```
InstalacionNginx/
|-- docker-compose.yml
|-- nginx/
|   |-- default.conf
|-- certs/
|   |-- nginx-selfsigned.crt
|   |-- nginx-selfsigned.key
|-- web/
|   |-- index.html
|   |-- assets/
|   |-- images/
|   |-- error/
|   |-- reloj/
|       |-- index.html
|       |-- script.js
|       |-- style.css
|-- evidencias/
|   |-- 01_docker_compose_ps.png
|   |-- 02_nginx_config.png
|   |-- 03_hosts_resolucion.png
|   |-- 04_web_cloudacademy.png
|   |-- 05_filezilla_conexion.png
|   |-- 06_filezilla_permisos.png
|   |-- 07_contenedores_activos.png
|   |-- 08_volumen_compartido.png
|   |-- 09_web_reloj.png
|   |-- 10_https_ssl.png
|   |-- 11_redireccion_301.png
|-- README.md
|-- Descripcion.md
```