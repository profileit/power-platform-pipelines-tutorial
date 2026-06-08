# Power Platform Pipelines Tutorial

GitHub Actions workflows para despliegue de soluciones Power Platform entre entornos.

## Flujo de despliegue

```
DEV  ──(manual)──▶  PRE  ──(manual)──▶  PROD
```

## Workflows

| Workflow | Archivo | Trigger |
|---|---|---|
| Deploy DEV → PRE | `deploy-dev-to-pre.yml` | Manual (`workflow_dispatch`) |
| Deploy PRE → PROD | `deploy-pre-to-prod.yml` | Manual (`workflow_dispatch`) |

Cada workflow exporta la solución como **managed** desde el entorno origen e importa en el destino.
El zip exportado queda guardado como artifact de GitHub Actions durante 30 días.

## Configuración inicial

### 1. App Registration en Azure AD

Crea una App Registration con un Client Secret y anota:
- **Application (client) ID**
- **Client secret value**
- **Directory (tenant) ID**

Asegúrate de añadir el Service Principal como **System Administrator** en los tres entornos de Power Platform.

### 2. GitHub Secrets

En `Settings → Secrets and variables → Actions → Secrets`, añade:

| Secret | Valor |
|---|---|
| `CLIENT_ID` | Application (client) ID de la App Registration |
| `CLIENT_SECRET` | Client secret value |
| `TENANT_ID` | Directory (tenant) ID |
| `DEV_ENVIRONMENT_URL` | URL del entorno DEV (ej: `https://org-dev.crm4.dynamics.com`) |
| `PRE_ENVIRONMENT_URL` | URL del entorno PRE |
| `PROD_ENVIRONMENT_URL` | URL del entorno PROD |

### 3. GitHub Variable (opcional)

En `Settings → Secrets and variables → Actions → Variables`, añade:

| Variable | Valor |
|---|---|
| `SOLUTION_NAME` | Nombre único de la solución (ej: `MyCompanySolution`) |

Si no la configuras, deberás introducirla manualmente cada vez que lances el workflow.

### 4. GitHub Environments

Los workflows usan los environments `pre` y `prod` de GitHub, lo que permite configurar **required reviewers** (aprobación antes de desplegar).

En `Settings → Environments`, crea los entornos `pre` y `prod` y configura los revisores necesarios.

## Uso

1. Ve a `Actions` en el repositorio.
2. Selecciona el workflow deseado.
3. Pulsa **Run workflow**.
4. (Opcional) Introduce el nombre de la solución si no tienes la variable configurada.
