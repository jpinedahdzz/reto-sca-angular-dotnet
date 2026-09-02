# Trivy report — CatalogMicroservice .NET (AFTER Fase B / modernización)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Tras Fase B (bumps de paquetes directos + DevSecOps) |
| **Fecha del escaneo** | 2026-09-01 |
| **Herramienta** | Trivy 0.74.0 |
| **Comando** | `trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice` |
| **TFM** | net8.0 |

## Resumen

| Severidad | Cantidad |
| --------- | -------- |
| CRITICAL | **0** |
| HIGH | **0** |
| **Total (gate)** | **0** |

```text
Report Summary
Target                                                         Type         Vulnerabilities
CatalogMicroservice/.../CatalogMicroservice.deps.json          dotnet-core  0
CatalogMicroservice/packages.lock.json                         nuget        0
Middleware/.../Middleware.deps.json                            dotnet-core  0
Middleware/packages.lock.json                                  nuget        0
```

## Cambios Fase B (además del gate)

- HealthChecks 8.0.1, Serilog 8.0.3, Swashbuckle 6.9.0, JwtBearer 8.0.11.
- `MongoDB.Driver` se mantuvo en **2.24.0** (2.30+ rompía build con HealthChecks UI).
- CI Trivy, Dependabot, SBOM CycloneDX.

## Cómo reproducir

```powershell
git checkout phase-b-modernized
# o: git checkout main
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice
```
