# Trivy report — Angular (AFTER Fase A / gate)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Tras remediación mínima (Fase A) |
| **Fecha del escaneo** | 2026-08-31 |
| **Herramienta** | Trivy 0.74.0 |
| **Comando** | `trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app` |
| **Target** | `package-lock.json` (npm) |
| **Versión Angular** | **20.3.27** |

## Resumen

| Severidad | Cantidad |
| --------- | -------- |
| CRITICAL | **0** |
| HIGH | **0** |
| **Total (gate)** | **0** |

```text
Report Summary
Target              Type   Vulnerabilities
package-lock.json   npm    0
```

## Remediación aplicada

- Subida de `@angular/*` a **20.3.27** (mínimo con fixes publicados).
- `@angular/material` / `@angular/cdk` → **20.2.14**.
- `standalone: false` en componentes NgModule (compatibilidad con Angular 20).

## Cómo reproducir (en tag `phase-a-gate-green` o `main`)

```powershell
git checkout phase-a-gate-green
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app
```

**Resultado esperado:** 0 CRITICAL, 0 HIGH.
