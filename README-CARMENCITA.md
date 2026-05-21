# Lycet Carmencita

Este proyecto queda separado del backend FastAPI para que pueda correr como servicio independiente en local y en despliegue.

## Desarrollo local

1. Copia `.env.example` como `.env`.
2. Ajusta `CLIENT_TOKEN`, `SOL_USER`, `SOL_PASS` y las credenciales reales si aplica.
3. Verifica que `data/cert.pem` y `data/logo.png` existan localmente.
4. Levanta Lycet:

```bash
docker compose up --build
```

Lycet quedara disponible en:

```text
http://localhost:8001
```

En el backend local usa:

```env
SUNAT_ENV=beta
SUNAT_PROVIDER=lycet
LYCET_API_URL=http://localhost:8001
LYCET_CLIENT_TOKEN=el_mismo_valor_de_CLIENT_TOKEN
```

## Despliegue en Render

Crea un servicio separado para Lycet usando este repositorio/carpeta.

Variables necesarias en Render:

```env
APP_ENV=prod
APP_SECRET=una_clave_larga_segura
CLIENT_TOKEN=token_privado_para_backend
SOL_USER=RUC_USUARIO_SOL
SOL_PASS=CLAVE_SOL
FE_URL=https://e-beta.sunat.gob.pe/ol-ti-itcpfegem-beta/billService
RE_URL=https://e-beta.sunat.gob.pe/ol-ti-itemision-otroscpe-gem-beta/billService
GUIA_URL=https://e-beta.sunat.gob.pe/ol-ti-itemision-guia-gem-beta/billService
AUTH_URL=https://gre-test.nubefact.com/v1
API_URL=https://gre-test.nubefact.com/v1
CLIENT_ID=client_id_de_guia_si_aplica
CLIENT_SECRET=client_secret_de_guia_si_aplica
WKHTMLTOPDF_PATH=wkhtmltopdf
CORS_ALLOW_ORIGIN=.
TRUSTED_PROXIES=127.0.0.1,REMOTE_ADDR
```

Render normalmente inyecta `PORT`; el entrypoint usa `${PORT:-8000}`.

En el backend desplegado usa:

```env
SUNAT_ENV=beta
SUNAT_PROVIDER=lycet
LYCET_API_URL=https://url-de-lycet-en-render
LYCET_CLIENT_TOKEN=el_mismo_valor_de_CLIENT_TOKEN
```

Si Lycet queda como servicio privado en Render, usa la URL interna que Render muestre para el servicio.

### Certificado y logo en despliegue

La carpeta `data` no debe subirse con certificados reales. Para produccion/beta tienes dos caminos:

1. Usar los endpoints de configuracion de Lycet despues del deploy:

```bash
curl -X PUT "https://url-de-lycet-en-render/api/v1/configuration/company/RUC?token=CLIENT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"SOL_USER\":\"RUC_USUARIO_SOL\",\"SOL_PASS\":\"CLAVE_SOL\",\"certificate\":\"CERTIFICADO_PEM_EN_BASE64\"}"
```

2. Subir `cert.pem`, `logo.png` o `empresas.json` como archivos privados del servicio/plataforma, evitando commitarlos al repositorio.

## Archivos sensibles

No subas `.env` ni certificados reales al repositorio. La carpeta `data` esta pensada para archivos locales/sensibles como:

```text
data/cert.pem
data/logo.png
data/empresas.json
```
