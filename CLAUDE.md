# CLAUDE.md

## Proyecto

Repositorio de despliegue de la solución Power Platform **ConversationalAgent** usando GitHub Actions.

## Entornos

| Nombre | Uso |
|---|---|
| develop | Entorno de desarrollo (origen de la pipeline DEV→PRE) |
| pre | Preproducción (destino DEV→PRE, origen PRE→PROD) |
| prod | Producción (destino PRE→PROD) |

## Service Principal

- **Nombre**: SP-PowerPlatform-Pipelines
- **Client ID**: `8c378df8-9a5f-4c9c-867a-c4f25b5837e3`
- **Client Secret**: guardado como secreto `CLIENT_SECRET` en GitHub

## Solución

- **Nombre único**: `ConversationalAgent`
- **Formato de versión**: `1.0.1.{github.run_number}` (nunca usar `1.0.0.X`, la última versión manual fue `1.0.0.4`)

## Workflows

### deploy-dev-to-pre.yml

1. Instala PAC CLI (actions-install + dotnet tool para PATH)
2. Valida conexión a DEV
3. Actualiza versión en DEV con `pac solution online-version`
4. Exporta solución como managed
5. Sube artifact a GitHub Actions (retención 30 días)
6. Valida conexión a PRE
7. Importa solución a PRE
8. Crea tag `deploy/pre/1.0.1.{run_number}`

### deploy-pre-to-prod.yml

Input requerido: `pre_tag` (ej: `deploy/pre/1.0.1.5`)

1. Extrae run number del tag
2. Busca run ID en la API de GitHub (`deploy-dev-to-pre.yml`)
3. Descarga artifact del run de DEV→PRE
4. Valida conexión a PROD
5. Importa solución a PROD
6. Crea tag `deploy/prod/1.0.1.{run_number}`

## Secretos GitHub

`CLIENT_ID`, `CLIENT_SECRET`, `TENANT_ID`, `DEV_ENVIRONMENT_URL`, `PRE_ENVIRONMENT_URL`, `PROD_ENVIRONMENT_URL`

## Notas importantes

- La pipeline de PROD **nunca re-exporta** desde PRE — siempre usa el artifact generado por DEV→PRE para garantizar que PROD recibe exactamente lo mismo que se validó en PRE.
- El versionado parte de `1.0.1.X` para no colisionar con versiones manuales previas (`1.0.0.4`).
- PAC CLI requiere tanto `actions-install` (para que funcionen los actions de powerplatform) como instalación via dotnet (para usarlo en steps `run`).
