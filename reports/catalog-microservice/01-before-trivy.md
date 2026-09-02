# Trivy report — CatalogMicroservice .NET (BEFORE / baseline)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Baseline (antes de remediación) |
| **Fecha del escaneo** | 2026-08-31 |
| **Herramienta** | Trivy 0.74.0 |
| **Comando** | `trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice` |
| **TFM** | net8.0 |

## Resumen (únicos en lockfile NuGet)

| Severidad | Cantidad |
| --------- | -------- |
| CRITICAL | **0** |
| HIGH | **4** (paquetes/CVEs únicos en `packages.lock.json`) |
| **Total (gate)** | **4 HIGH** |

Trivy también reportó hallazgos en `.deps.json` (mismo `Snappier`); la fuente principal para el análisis es el lockfile NuGet.

## Hallazgos HIGH (únicos)

| Paquete | CVE | Tipo | Versión instalada | Versión con fix | Título (resumen) |
| ------- | --- | ---- | ----------------- | --------------- | ---------------- |
| `Microsoft.Extensions.Caching.Memory` | CVE-2024-43483 | Transitivo | 8.0.0 | **8.0.1** | Hash flooding en componentes .NET |
| `Snappier` | CVE-2026-44302 | Transitivo | 1.0.0 | **1.3.1** | Loop infinito en descompresión Snappy malformada |
| `System.Text.Json` | CVE-2024-30105 | Transitivo | 8.0.0 | 8.0.4 | DoS en System.Text.Json |
| `System.Text.Json` | CVE-2024-43485 | Transitivo | 8.0.0 | **8.0.5** | DoS en System.Text.Json |

## Notas de análisis

- Todos son **transitivos** (no estaban declarados al inicio en el `.csproj`).
- Apoyo diagnóstico: `dotnet list package --vulnerable --include-transitive`.
- Trivy **requiere** `packages.lock.json` (`RestorePackagesWithLockFile=true`).

## Cómo reproducir (en tag `baseline`)

```powershell
git checkout baseline
dotnet restore .\catalog-microservice\CatalogMicroservice\CatalogMicroservice.csproj
dotnet restore .\catalog-microservice\Middleware\Middleware.csproj
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice
```
