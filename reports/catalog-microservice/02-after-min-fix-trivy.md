# Trivy report — CatalogMicroservice .NET (AFTER Fase A / gate)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Tras remediación mínima (Fase A) |
| **Fecha del escaneo** | 2026-08-31 |
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

## Remediación aplicada (PackageReference directo)

| Paquete | Antes (transitivo) | Después (directo) |
| ------- | ------------------ | ----------------- |
| `Snappier` | 1.0.0 | **1.3.1** |
| `Microsoft.Extensions.Caching.Memory` | 8.0.0 | **8.0.1** |
| `System.Text.Json` | 8.0.0 | **8.0.5** |

## Cómo reproducir

```powershell
git checkout phase-a-gate-green
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice
```

**Resultado esperado:** 0 CRITICAL, 0 HIGH.
