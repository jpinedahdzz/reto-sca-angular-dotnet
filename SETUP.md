# Setup local — T-Volvers

Fecha: 2026-08-28

## Contenido

| Ruta | Qué es |
| ---- | ------ |
| `docs-enunciado.md` | Enunciado original del reto |
| `README.md` | Plantilla del entregable (rellenar durante el trabajo) |
| `angular-app/` | Clone de `cidaluna/ecommerce-angular-14` |
| `catalog-microservice/` | `CatalogMicroservice` + `Middleware` (dependencia de proyecto) |
| `_upstream-microservices/` | Clone completo upstream (referencia; ignorado en git del entregable) |
| `reports/` | Aquí van los Trivy 01/02/03 |

## Baseline Trivy (2026-08-31)

| Proyecto | CRITICAL | HIGH | Reporte |
| -------- | -------- | ---- | ------- |
| Angular | 0 | 13 | `reports/angular/01-before-trivy.txt` |
| CatalogMicroservice | 0 | 4 (únicos en lockfile) | `reports/catalog-microservice/01-before-trivy.txt` |

### Nota importante (.NET)

Trivy no escanea dependencias NuGet hasta que exista `packages.lock.json`. En ambos `.csproj` se habilitó:

```xml
<RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
```

Luego:

```powershell
dotnet restore .\catalog-microservice\CatalogMicroservice\CatalogMicroservice.csproj --force-evaluate
dotnet restore .\catalog-microservice\Middleware\Middleware.csproj --force-evaluate
```

## Comandos de escaneo (reproducir)

```powershell
cd C:\Julio\Documents\T-Volvers

trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app | Tee-Object -FilePath reports/angular/01-before-trivy.txt
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice | Tee-Object -FilePath reports/catalog-microservice/01-before-trivy.txt
```

## Próximo paso

Fase A — remediación mínima (gate 0 CRITICAL / 0 HIGH).
