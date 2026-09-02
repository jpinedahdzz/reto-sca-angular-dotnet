# Reportes Trivy — evidencia del reto SCA

Estos archivos son la **evidencia before/after** del análisis.

> **Nota:** Los `.txt` antiguos se generaron con PowerShell `Tee-Object` en **UTF-16** y con caracteres de caja Unicode, por eso Notepad++ los mostraba “raros”.  
> Usa los **`.md`** (UTF-8) — son la versión legible para la demo y el README.

## Índice

| Momento | Angular | .NET (CatalogMicroservice) |
| ------- | ------- | -------------------------- |
| **01 BEFORE** (baseline) | [01-before-trivy.md](./angular/01-before-trivy.md) | [01-before-trivy.md](./catalog-microservice/01-before-trivy.md) |
| **02 AFTER Fase A** (gate 0 C/H) | [02-after-min-fix-trivy.md](./angular/02-after-min-fix-trivy.md) | [02-after-min-fix-trivy.md](./catalog-microservice/02-after-min-fix-trivy.md) |
| **03 AFTER Fase B** | [03-after-modernize-trivy.md](./angular/03-after-modernize-trivy.md) | [03-after-modernize-trivy.md](./catalog-microservice/03-after-modernize-trivy.md) |

## Resumen rápido

| Momento | Angular CRITICAL | Angular HIGH | .NET CRITICAL | .NET HIGH |
| ------- | ---------------- | ------------ | ------------- | --------- |
| 01 Before | 0 | **13** | 0 | **4** |
| 02 Fase A | **0** | **0** | **0** | **0** |
| 03 Fase B | **0** | **0** | **0** | **0** |

## Tags Git relacionados

```powershell
git checkout baseline              # código vulnerable + reportes 01
git checkout phase-a-gate-green    # gate verde Fase A
git checkout phase-b-modernized    # Fase B
```

## Comando de aceptación (reproducir)

```powershell
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice
```
