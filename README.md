# Pipes centralizados · Esumer

Los pipelines de **todos** los proyectos del curso viven aquí. Cada proyecto no copia
pipelines: los **llama** con `uses:`. Arreglas un pipe una vez y lo heredan todos.

| Pipe | Cuándo corre | Rol de AWS (OIDC, sin llaves) | Qué hace |
|---|---|---|---|
| `plan.yml` | en cada pull request | `<proyecto>-plan` (solo mirar) | `terraform plan` y el resumen en el PR |
| `apply.yml` | en cada push a `main` | `<proyecto>-apply` | `terraform apply` y los outputs |
| `destroy.yml` | a mano (Run workflow) | `<proyecto>-apply` | `terraform destroy` |

## Cómo lo usa un proyecto

Crea tu proyecto desde la plantilla
[ab-esumer-plantilla](https://github.com/alejandrobarreracorrea/ab-esumer-plantilla)
(botón **Use this template**). Ya trae este `.github/workflows/pipeline.yml`:

```yaml
jobs:
  plan:
    if: github.event_name == 'pull_request'
    uses: alejandrobarreracorrea/ab-esumer-pipelines/.github/workflows/plan.yml@main
    with:
      proyecto: ${{ github.event.repository.name }}
      cuenta: ${{ vars.AWS_ACCOUNT_ID }}
```

## Lo que espera cada pipe

- El nombre del repo es el **nombre del proyecto**: en minúsculas, sin espacios.
- La variable del repo **AWS_ACCOUNT_ID** (Settings → Secrets and variables → Actions → Variables).
- El bootstrap de la plantilla ya corrido: crea el bucket `<proyecto>-estado-<cuenta>`,
  el proveedor OIDC y los roles `<proyecto>-plan` y `<proyecto>-apply`.
- Terraform en la carpeta `terraform/`, que recibe la variable `proyecto`.
