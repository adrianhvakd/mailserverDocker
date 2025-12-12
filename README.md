# Docker Mailserver Local

Este proyecto contiene un servidor de correo local utilizando [docker-mailserver](https://github.com/docker-mailserver/docker-mailserver). Permite recibir y enviar correos de forma local y hacer pruebas de envío a buzones externos mediante un relay SMTP.

---

## Requisitos

- [Docker](https://www.docker.com/) >= 20.10  
- [Docker Compose](https://docs.docker.com/compose/) >= 1.29  
- Conexión a internet (solo si se usan relays externos)

---

## Levantar el servidor

1. **Clona el repositorio y entra en la carpeta:**

```bash
git clone <TU_REPOSITORIO>
cd tarea2
```

2. **Arranca el contenedor:**

```bash
docker compose up -d
```

3. **Verifica que el contenedor esté corriendo:**

```bash
docker ps
```

Deberías ver algo como:

```
CONTAINER ID   IMAGE         STATUS   NAMES
xxxxxxxxxxxx   mailserver    Up       mailserver
```

---

## Comprobar logs

Para ver los logs en tiempo real:

```bash
docker logs -f mailserver
```

O desde dentro del contenedor:

```bash
docker exec -it mailserver tail -f /var/log/mail.log
```

---

## Enviar un correo de prueba

### Opción 1: Solo local

1. **Instala swaks dentro del contenedor** (si no está):

```bash
docker exec -it mailserver bash
apt update && apt install -y swaks
exit
```

2. **Envía un correo a un buzón local:**

```bash
docker exec -it mailserver swaks \
  --to usuario@mail.local.test \
  --from usuario@mail.local.test \
  --server mailserver \
  --header "Subject: Prueba local" \
  --body "Este es un correo de prueba local."
```

### Opción 2: Enviar a correos externos (Gmail, Outlook, etc.)

Por seguridad, docker-mailserver no permite relay externo por defecto. Para enviar correos a buzones externos:

1. Configura un usuario con contraseña en tu servidor.
2. Configura el servidor como relay host apuntando a un SMTP externo (ej. Gmail).
3. Envía usando swaks con autenticación:

```bash
docker exec -it mailserver swaks \
  --to prueba@gmail.com \
  --from usuario@mail.local.test \
  --server mailserver \
  --auth LOGIN \
  --auth-user usuario@mail.local.test \
  --auth-password TU_CONTRASEÑA \
  --header "Subject: Prueba externa" \
  --body "Correo de prueba enviado desde Docker Mailserver a Gmail"
```

⚠️ **Nota:** Algunos servicios de correo (Gmail, Hotmail) pueden bloquear correos enviados desde servidores sin dominio real. Se recomienda usar un relay SMTP real para pruebas externas.

---

## Conexión con clientes de correo

Puedes configurar cualquier cliente de correo (Thunderbird, Outlook, etc.) para conectarte a tu servidor local:

- **Servidor IMAP/POP3:** `mail.local.test`
  - Puerto IMAP: `143` (sin SSL) o `993` (con SSL)
- **Servidor SMTP:** `mail.local.test`
  - Puerto SMTP: `25` o `587`

---

## Notas finales

- El servidor docker-mailserver ya incluye **Postfix**, **Dovecot**, **Amavis**, **OpenDKIM** y **OpenDMARC**.
- Las advertencias de Amavis sobre archivos `.doc`, `.zoo` o `.zst` son normales y no afectan la funcionalidad básica del correo.
- Para pruebas locales sin enviar correos reales, se recomienda integrar **MailHog** o **Mailtrap**.

---

## Referencias

- [docker-mailserver GitHub](https://github.com/docker-mailserver/docker-mailserver)
- [Swaks - Swiss Army Knife SMTP](http://www.jetmore.org/john/code/swaks/)

---

## ¿Dudas o problemas?

Si encuentras algún error o tienes preguntas, revisa los logs con `docker logs -f mailserver` o consulta la documentación oficial de docker-mailserver.