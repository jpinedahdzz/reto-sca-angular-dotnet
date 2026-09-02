# Trivy report — Angular (AFTER Fase B / modernización)

| Campo | Valor |
| ----- | ----- |
| **Momento** | Tras Fase B (DevSecOps + mismos paquetes Angular de Fase A) |
| **Fecha del escaneo** | 2026-09-01 |
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

## Notas

- En Fase B Angular ya estaba en 20.3.27 (el salto de major fue necesario en Fase A por falta de fixes en 14.x).
- Fase B añadió CI, Dependabot y SBOM; el gate CRITICAL/HIGH se mantiene en verde.

## Cómo reproducir

```powershell
git checkout phase-b-modernized
# o: git checkout main
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app
```
