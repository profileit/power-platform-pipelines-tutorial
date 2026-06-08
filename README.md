# Power Platform Pipelines Tutorial

GitHub Actions workflows para despliegue de la solución **ConversationalAgent** entre entornos de Power Platform.

## Flujo de despliegue

```
DEV  ──(manual)──▶  PRE  ──(tag)──▶  PROD
```

- **DEV → PRE**: exporta desde DEV, versiona, importa a PRE y crea un tag git.
- **PRE → PROD**: a partir del tag de PRE, descarga el artifact ya validado e importa a PROD.

## Workflows

| Workflow | Archivo | Trigger | Input |
|---|---|---|---|
| Deploy DEV → PRE | `deploy-dev-to-pre.yml` | Manual | — |
| Deploy PRE → PROD | `deploy-pre-to-prod.yml` | Manual | Tag de PRE |

## Versionado automático

La pipeline DEV→PRE actualiza la versión de la solución en DEV antes de exportar:

```
1.0.1.{run_number}   →   ej: 1.0.1.5
```

Cada despliegue exitoso crea un tag en el repositorio:

| Pipeline | Tag creado |
|---|---|
| DEV → PRE | `deploy/pre/1.0.1.{run}` |
| PRE → PROD | `deploy/prod/1.0.1.{run}` |

Los tags son visibles en **Code → Tags**.

## Configuración inicial

### 1. Service Principal en Azure AD

Crea una App Registration (`SP-PowerPlatform-Pipelines`) con un Client Secret y anota:
- **Application (client) ID**
- **Client secret value**
- **Directory (tenant) ID**

Añade el Service Principal como **System Administrator** en los tres entornos de Power Platform.

### 2. GitHub Secrets

En `Settings → Secrets and variables → Actions → Secrets`:

| Secret | Valor |
|---|---|
| `CLIENT_ID` | Application (client) ID de la App Registration |
| `CLIENT_SECRET` | Client secret value |
| `TENANT_ID` | Directory (tenant) ID |
| `DEV_ENVIRONMENT_URL` | URL del entorno DEV (ej: `https://org-dev.crm4.dynamics.com`) |
| `PRE_ENVIRONMENT_URL` | URL del entorno PRE |
| `PROD_ENVIRONMENT_URL` | URL del entorno PROD |

### 3. GitHub Environments

En `Settings → Environments`, crea los entornos `pre` y `prod`.

Puedes añadir **Required reviewers** en `prod` para requerir aprobación antes de cada despliegue a producción.

## Uso

### Desplegar DEV → PRE

1. Ve a **Actions → Deploy DEV → PRE**.
2. Pulsa **Run workflow**.
3. Espera a que finalice — se creará el tag `deploy/pre/1.0.1.X` en **Code → Tags**.

### Desplegar PRE → PROD

1. Ve a **Code → Tags** y copia el tag que quieres promover (ej: `deploy/pre/1.0.1.5`).
2. Ve a **Actions → Deploy PRE → PROD**.
3. Pulsa **Run workflow** e introduce el tag en el campo `pre_tag`.
4. Al finalizar se creará el tag `deploy/prod/1.0.1.5`.
