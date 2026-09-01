# Reto SCA: remediación de vulnerabilidades — Angular + .NET

**Rol:** Analista Desarrollador Fullstack (énfasis DevSecOps)  
**Enfoque:** Fase A (fix mínimo / gate CRITICAL+HIGH = 0) → Fase B (modernización)  
**Fecha:** YYYY-MM-DD  
**Autor:** Tu Nombre

---

## 1. Contexto

Análisis y remediación SCA (Software Composition Analysis) sobre dos proyectos open source con dependencias vulnerables:

| Proyecto | Origen | Carpeta en este repo |
| -------- | ------ | -------------------- |
| Frontend Angular | [cidaluna/ecommerce-angular-14](https://github.com/cidaluna/ecommerce-angular-14) | `angular-app/` |
| Backend .NET | [aelassas/microservices](https://github.com/aelassas/microservices) — `CatalogMicroservice` | `catalog-microservice/` |

**Criterio de aceptación:**  
`trivy fs --scanners vuln --severity CRITICAL,HIGH` → **0 hallazgos** en Angular y .NET al final de la Fase A (y se mantiene en Fase B).  
MEDIUM no remediados: justificación individual (sección 7).

---

## 2. Estrategia (híbrido)

| Fase | Objetivo | Resultado esperado |
| ---- | -------- | ------------------ |
| **A — Gate** | Remediación mínima (bumps, overrides, PackageReference) | 0 CRITICAL, 0 HIGH; proyectos compilando |
| **B — Modernización** | Subir Angular (ruta de majors) y .NET a LTS razonable | Menos deuda; mismo gate en verde |

Evidencia Trivy en tres momentos: baseline → post Fase A → post Fase B.

| Tag Git (recomendado) | Momento |
| --------------------- | ------- |
| `baseline` | Antes de cambios |
| `phase-a-gate-green` | Tras Fase A |
| `phase-b-modernized` | Tras Fase B |

---

## 3. Prerrequisitos

| Herramienta | Uso | Instalación (Windows, ejemplo) |
| ----------- | --- | ------------------------------ |
| [Trivy](https://aquasecurity.github.io/trivy/) | SCA filesystem | `scoop install trivy` / `choco install trivy` |
| Node.js + npm | Angular | LTS alineada al proyecto (ver `engines` / README upstream) |
| .NET SDK | CatalogMicroservice | Versión compatible con el `TargetFramework` |
| Git | Versionado y tags | — |

Verificar:

```powershell
trivy --version
node -v
npm -v
dotnet --version
```

Referencias:

- Angular Update Guide: https://update.angular.io  
- `dotnet list package --vulnerable`: https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-list-package  
- OSV (CVEs): https://osv.dev  

---

## 4. Cómo reproducir los escaneos

Desde la raíz del repositorio:

```powershell
# --- Baseline (01) ---
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app | Tee-Object -FilePath reports/angular/01-before-trivy.txt
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice | Tee-Object -FilePath reports/catalog-microservice/01-before-trivy.txt

# --- Tras Fase A (02) ---
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app | Tee-Object -FilePath reports/angular/02-after-min-fix-trivy.txt
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice | Tee-Object -FilePath reports/catalog-microservice/02-after-min-fix-trivy.txt

# --- Tras Fase B (03) ---
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app | Tee-Object -FilePath reports/angular/03-after-modernize-trivy.txt
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice | Tee-Object -FilePath reports/catalog-microservice/03-after-modernize-trivy.txt
```

Opcional (JSON):

```powershell
trivy fs --scanners vuln --severity CRITICAL,HIGH --format json -o reports/angular/01-before-trivy.json ./angular-app
```

Complementarios (diagnóstico, no sustituyen a Trivy para el gate):

```powershell
# Angular
cd angular-app
npm audit

# .NET
cd catalog-microservice
dotnet restore
dotnet list package --vulnerable --include-transitive
```

---

## 5. Resumen de resultados

### Conteos Trivy (CRITICAL + HIGH)

| Momento | Angular CRITICAL | Angular HIGH | .NET CRITICAL | .NET HIGH | Reportes |
| ------- | ---------------- | ------------ | ------------- | --------- | -------- |
| Before | _n_ | _m_ | _n_ | _m_ | `reports/*/01-before-trivy.txt` |
| Fase A | **0** | **0** | **0** | **0** | `reports/*/02-after-min-fix-trivy.txt` |
| Fase B | **0** | **0** | **0** | **0** | `reports/*/03-after-modernize-trivy.txt` |

### Versiones de plataforma

| Momento | Angular | Node (build) | .NET `TargetFramework` |
| ------- | ------- | ------------ | ---------------------- |
| Before | 14.x | _x_ | netX.0 |
| Fase A | 14.x (+ deps/overrides) | _x_ | netX.0 (+ package bumps) |
| Fase B | _15/16/17/18.x_ | _y_ | net8.0 (o el elegido) |

---

## 6. Análisis de hallazgos (CRITICAL / HIGH)

Completar una fila por CVE relevante del baseline. Usar OSV + árbol de dependencias (`npm ls <pkg>`, `dotnet list package --include-transitive`).

### 6.1 Angular

| CVE / ID | Paquete (versión instalada) | Directo / Transitivo | Severidad | Impacto en esta app | Acción Fase A | Acción Fase B |
| -------- | --------------------------- | -------------------- | --------- | ------------------- | ------------- | ------------- |
| CVE-… | `pkg@1.2.3` | Transitivo vía `foo` | HIGH | _ej. XSS en … / no expuesto pero gate exige fix_ | Override / bump a `1.2.4` | Resuelto al subir Angular / deps |
| | | | | | | |

### 6.2 CatalogMicroservice (.NET)

| CVE / ID | Paquete (versión instalada) | Directo / Transitivo | Severidad | Impacto en esta app | Acción Fase A | Acción Fase B |
| -------- | --------------------------- | -------------------- | --------- | ------------------- | ------------- | ------------- |
| CVE-… | `Package.Name 1.0.0` | Directo | CRITICAL | _ej. RCE en deserialización si…_ | `PackageReference` → `1.0.1` | Incluido en upgrade TFM/LTS |
| | | | | | | |

### Decisiones técnicas (narrativa corta)

**Fase A**

- _Ej.: se priorizó bump de X porque Fixed Version en Trivy/OSV es Y; override de Z porque es transitivo sin padre actualizable sin romper Angular 14._

**Fase B**

- _Ej.: Angular 14 → 15 → 16 según update.angular.io; .NET net6.0 → net8.0; paquetes alineados al SDK._

---

## 7. MEDIUM no remediados (justificación individual)

| Proyecto | CVE / paquete | Por qué se acepta |
| -------- | ------------- | ----------------- |
| Angular | CVE-… / `pkg` | _Sin fix publicado / falso positivo / riesgo no explotable en este contexto_ |
| .NET | CVE-… / `Package` | _…_ |

Si no quedan MEDIUM sin remediar: indicar **“N/A — todos remediados o no reportados en el alcance del gate.”**

---

## 8. Cambios realizados

### Fase A — remediación mínima

**Angular (`angular-app/`)**

- Archivos tocados: `package.json`, `package-lock.json`, _otros_
- Overrides npm (si aplica):

```json
"overrides": {
  "paquete-vulnerable": "version-segura"
}
```

**CatalogMicroservice (`catalog-microservice/`)**

- Archivos tocados: `*.csproj`, _Directory.Packages.props si existe_, etc.
- Paquetes subidos: `Package.A 1.0 → 1.2`, …

### Fase B — modernización

**Angular**

- Ruta de upgrade: 14 → _N_ (comandos `ng update …` usados).
- Breaking changes atendidos: _lista breve_.

**.NET**

- `TargetFramework`: `netX.0` → `netY.0`
- Paquetes / SDK alineados: _lista breve_.

---

## 9. Verificación funcional

| Proyecto | Comando build | Comando run | Resultado |
| -------- | ------------- | ----------- | --------- |
| Angular | `npm run build` | `npm start` / `ng serve` | OK / notas |
| .NET | `dotnet build` | `dotnet run --project …` | OK / notas |

---

## 10. Estructura del repositorio

```text
.
├── README.md
├── reports/
│   ├── angular/
│   │   ├── 01-before-trivy.txt
│   │   ├── 02-after-min-fix-trivy.txt
│   │   └── 03-after-modernize-trivy.txt
│   └── catalog-microservice/
│       ├── 01-before-trivy.txt
│       ├── 02-after-min-fix-trivy.txt
│       └── 03-after-modernize-trivy.txt
├── angular-app/
└── catalog-microservice/
```

- **Pre-análisis (baseline):** `reports/*/01-before-trivy.txt`  
- **Post-análisis Fase A (gate):** `reports/*/02-after-min-fix-trivy.txt`  
- **Post-análisis Fase B (modernizado):** `reports/*/03-after-modernize-trivy.txt`  

---

## 11. Extra (opcional — puntos adicionales)

### GitHub Actions (gate SCA)

Workflow ejemplo (`.github/workflows/sca.yml`): escaneo con Trivy en PRs/push; fallar si hay CRITICAL/HIGH.

### Dependabot / Renovate

- Ecosistemas: `npm` (`angular-app/`) y `nuget` (`catalog-microservice/`).

### SBOM

```powershell
trivy fs --format cyclonedx -o reports/angular/sbom-cyclonedx.json ./angular-app
trivy fs --format cyclonedx -o reports/catalog-microservice/sbom-cyclonedx.json ./catalog-microservice
```

---

## 12. Limitaciones y notas

- _Ej.: modernización Angular detuvo en versión X por tiempo; el gate de aceptación ya estaba cumplido en Fase A._
- _Ej.: el monorepo upstream se recortó a CatalogMicroservice para acotar el alcance._

---

## 13. Checklist de entrega

- [ ] Código Angular y .NET modificados y compilando  
- [ ] Reportes Trivy 01 (before), 02 (Fase A), 03 (Fase B) commiteados  
- [ ] 0 CRITICAL y 0 HIGH en Fase A y Fase B (o justificación documentada)  
- [ ] MEDIUM restantes justificados uno a uno  
- [ ] Este README completo (tablas, decisiones, comandos exactos)  
- [ ] Repo Git público (fork o propio)  
- [ ] (Opcional) CI + Dependabot + SBOM  
