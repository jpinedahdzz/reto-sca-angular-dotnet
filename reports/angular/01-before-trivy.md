# Trivy report — Angular (BEFORE / baseline)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Baseline (antes de remediación) |
| **Fecha del escaneo** | 2026-08-31 |
| **Herramienta** | Trivy 0.74.0 |
| **Comando** | `trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app` |
| **Target** | `package-lock.json` (npm) |
| **Versión Angular** | 14.3.0 |

## Resumen

| Severidad | Cantidad |
| --------- | -------- |
| CRITICAL | **0** |
| HIGH | **13** |
| **Total (gate)** | **13** |

## Hallazgos HIGH

| Paquete | CVE | Versión instalada | Versión con fix (Trivy) | Título (resumen) |
| ------- | --- | ----------------- | ----------------------- | ---------------- |
| `@angular/common` | CVE-2025-66035 | 14.3.0 | 21.0.1 / 20.3.14 / 19.2.16 | Fuga de token XSRF vía URLs protocol-relative |
| `@angular/common` | CVE-2026-50170 | 14.3.0 | 20.3.22 / 19.2.23 / 21.2.15 | Filtrado de info vía HttpTransferCache |
| `@angular/common` | CVE-2026-50171 | 14.3.0 | (mismas líneas 19/20/21) | DoS vía parámetro `digitsInfo` malformado |
| `@angular/common` | CVE-2026-54266 | 14.3.0 | 22.0.1 / 21.2.17 / 20.3.25 | Hash débil 32-bit en HttpTransferCache |
| `@angular/common` | CVE-2026-54268 | 14.3.0 | 22.0.1 / 21.2.17 / 20.3.25 | DoS vía formato de fecha |
| `@angular/common` | CVE-2026-68945 | 14.3.0 | 22.0.2 / 21.2.19 / **20.3.27** | Reuso cross-request / state poisoning |
| `@angular/compiler` | CVE-2025-66412 | 14.3.0 | 21.0.2 / 20.3.15 / 19.2.17 | XSS almacenado (SVG / MathML) |
| `@angular/compiler` | CVE-2026-22610 | 14.3.0 | 21.0.7 / 20.3.16 / 19.2.18 | XSS en Template Compiler |
| `@angular/compiler` | CVE-2026-69151 | 14.3.0 | 22.0.1 / 21.2.19 / **20.3.27** | XSS vía handlers i18n |
| `@angular/core` | CVE-2026-22610 | 14.3.0 | 21.0.7 / 20.3.16 / 19.2.18 | XSS en Template Compiler |
| `@angular/core` | CVE-2026-27970 | 14.3.0 | 21.2.0 / 20.3.17 / 19.2.19 | XSS vía archivos de traducción |
| `@angular/core` | CVE-2026-54267 | 14.3.0 | 22.0.1 / 21.2.17 / 20.3.25 | DOM clobbering / cache poisoning (hydration) |
| `@angular/core` | CVE-2026-69151 | 14.3.0 | 22.0.1 / 21.2.19 / **20.3.27** | XSS vía handlers i18n |

## Notas de análisis

- Todos los hallazgos son en paquetes **directos** (`@angular/common`, `@angular/compiler`, `@angular/core`).
- **No hay versión fija en la rama 14.x** para estos CVEs.
- El mínimo con fixes publicados que cubre el conjunto (incluido CVE-2026-68945 / CVE-2026-69151) es **Angular 20.3.27**.

## Cómo reproducir (en tag `baseline`)

```powershell
git checkout baseline
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app
```
