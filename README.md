# Reto SCA: remediación de vulnerabilidades — Angular + .NET

**Rol:** Analista Desarrollador Fullstack (énfasis DevSecOps)  
**Enfoque:** Fase A (gate 0 CRITICAL/HIGH) → Fase B (modernización + DevSecOps)  
**Fecha:** 2026-09-01  
**Autor:** Julio Pineda  
**Repositorio:** https://github.com/jpinedahdzz/reto-sca-angular-dotnet

---

## 1. Contexto

Análisis y remediación SCA (Software Composition Analysis) sobre dos proyectos open source con dependencias vulnerables:

| Proyecto | Origen | Carpeta en este repo |
| -------- | ------ | -------------------- |
| Frontend Angular | [cidaluna/ecommerce-angular-14](https://github.com/cidaluna/ecommerce-angular-14) | `angular-app/` |
| Backend .NET | [aelassas/microservices](https://github.com/aelassas/microservices) — `CatalogMicroservice` | `catalog-microservice/` |

**Criterio de aceptación:**  
`trivy fs --scanners vuln --severity CRITICAL,HIGH` → **0 hallazgos** en Angular y .NET.

---

## 2. Estrategia (híbrido 1 + 2)

| Fase | Objetivo | Resultado |
| ---- | -------- | --------- |
| **A — Gate** | Cerrar CRITICAL/HIGH con el cambio mínimo que compile | 0 CRITICAL, 0 HIGH |
| **B — Modernización** | Actualizar paquetes directos, CI gate, Dependabot, SBOM | Mismo gate en verde + menos deuda |

| Tag Git | Momento |
| ------- | ------- |
| `baseline` | Antes de cambios |
| `phase-a-gate-green` | Tras Fase A |
| `phase-b-modernized` | Tras Fase B |

```powershell
git checkout baseline              # estado vulnerable + reportes 01
git checkout phase-a-gate-green    # gate verde Fase A
git checkout phase-b-modernized    # Fase B (CI, Dependabot, SBOM)
```

---

## 3. Prerrequisitos

| Herramienta | Instalación (Windows) |
| ----------- | --------------------- |
| [Trivy](https://aquasecurity.github.io/trivy/) | `winget install AquaSecurity.Trivy` |
| Node.js 22 + npm | Para Angular |
| .NET SDK 8 | Para CatalogMicroservice |

```powershell
trivy --version    # 0.74.0+
node -v            # v22.x
dotnet --version   # 8.0.x
```

**.NET — requisito para Trivy:** habilitar `packages.lock.json`:

```xml
<RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
```

```powershell
dotnet restore .\catalog-microservice\CatalogMicroservice\CatalogMicroservice.csproj --force-evaluate
dotnet restore .\catalog-microservice\Middleware\Middleware.csproj --force-evaluate
```

---

## 4. Cómo reproducir los escaneos

Desde la raíz del repositorio:

```powershell
# Escaneo en consola (gate)
trivy fs --scanners vuln --severity CRITICAL,HIGH ./angular-app
trivy fs --scanners vuln --severity CRITICAL,HIGH ./catalog-microservice

# Guardar salida limpia en UTF-8 (evitar Tee-Object → UTF-16)
trivy fs --scanners vuln --severity CRITICAL,HIGH --format json -o reports/angular/scan.json ./angular-app
```

Los reportes legibles para la demo están en Markdown: `reports/**/*.md` (ver [`reports/README.md`](reports/README.md)).

Complementarios:

```powershell
cd angular-app && npm audit
cd catalog-microservice
dotnet list package --vulnerable --include-transitive
```

---

## 5. Resumen de resultados

### Conteos Trivy (CRITICAL + HIGH)

| Momento | Angular CRITICAL | Angular HIGH | .NET CRITICAL | .NET HIGH | Reportes (Markdown, UTF-8) |
| ------- | ---------------- | ------------ | ------------- | --------- | -------------------------- |
| Before | 0 | **13** | 0 | **4** | [`reports/angular/01-…`](reports/angular/01-before-trivy.md) / [`…/catalog…/01-…`](reports/catalog-microservice/01-before-trivy.md) |
| Fase A | **0** | **0** | **0** | **0** | [`02-after-min-fix`](reports/angular/02-after-min-fix-trivy.md) |
| Fase B | **0** | **0** | **0** | **0** | [`03-after-modernize`](reports/angular/03-after-modernize-trivy.md) |

Índice de evidencia: [`reports/README.md`](reports/README.md).

### Versiones de plataforma

| Momento | Angular | Node | .NET TFM |
| ------- | ------- | ---- | -------- |
| Before | 14.3.0 | — | net8.0 |
| Fase A | **20.3.27** | 22.x | net8.0 (+ bumps transitivos) |
| Fase B | 20.3.27 | 22.x | net8.0 (+ paquetes directos actualizados) |

---

## 6. Análisis de hallazgos (CRITICAL / HIGH)

### 6.1 Angular (baseline — 13 HIGH, 0 CRITICAL)

Todos en `@angular/common`, `@angular/compiler` y `@angular/core` @ **14.3.0**. No existían parches en la rama 14; Trivy/OSV indican fixed en **19.x / 20.x / 21.x**.

| CVE | Paquete | Tipo | Impacto resumido | Remediación |
| --- | ------- | ---- | ---------------- | ----------- |
| CVE-2025-66035 | @angular/common | Directo | Fuga XSRF token vía URLs protocol-relative | Angular 20.3.27 |
| CVE-2026-50170 | @angular/common | Directo | Filtrado de credenciales en HttpTransferCache | Angular 20.3.27 |
| CVE-2026-50171 | @angular/common | Directo | DoS vía digitsInfo malformado | Angular 20.3.27 |
| CVE-2026-54266 | @angular/common | Directo | Cache poisoning (hash débil 32-bit) | Angular 20.3.27 |
| CVE-2026-54268 | @angular/common | Directo | DoS vía formato de fecha | Angular 20.3.27 |
| CVE-2026-68945 | @angular/common | Directo | Reuso cross-request / state poisoning | Angular 20.3.27 |
| CVE-2025-66412 | @angular/compiler | Directo | XSS almacenado (SVG/MathML) | Angular 20.3.27 |
| CVE-2026-22610 | @angular/compiler, @angular/core | Directo | XSS en Template Compiler | Angular 20.3.27 |
| CVE-2026-69151 | @angular/compiler, @angular/core | Directo | XSS vía handlers i18n | Angular 20.3.27 |
| CVE-2026-27970 | @angular/core | Directo | XSS vía archivos de traducción | Angular 20.3.27 |
| CVE-2026-54267 | @angular/core | Directo | DOM clobbering / cache poisoning (hydration) | Angular 20.3.27 |

**Decisión Fase A:** subir `@angular/*` a **20.3.27** (mínimo con fixes publicados). Añadir `standalone: false` en componentes NgModule (Angular 20 trata componentes como standalone por defecto).

### 6.2 .NET (baseline — 4 HIGH únicos en lockfile)

| CVE | Paquete | Tipo | Versión | Fix | Remediación |
| --- | ------- | ---- | ------- | --- | ----------- |
| CVE-2026-44302 | Snappier | Transitivo | 1.0.0 | 1.3.1 | PackageReference directo 1.3.1 |
| CVE-2024-43483 | Microsoft.Extensions.Caching.Memory | Transitivo | 8.0.0 | 8.0.1 | PackageReference directo 8.0.1 |
| CVE-2024-30105 | System.Text.Json | Transitivo | 8.0.0 | 8.0.4 | PackageReference directo 8.0.5 |
| CVE-2024-43485 | System.Text.Json | Transitivo | 8.0.0 | 8.0.5 | PackageReference directo 8.0.5 |

**Decisión Fase A:** forzar versiones seguras con `PackageReference` directo (patrón NuGet para transitivos vulnerables).

**Decisión Fase B:** actualizar paquetes directos (HealthChecks 8.0.1, Serilog 8.0.3, Swashbuckle 6.9.0, JwtBearer 8.0.11). MongoDB.Driver se mantiene en **2.24.0** por incompatibilidad de ensamblado con HealthChecks UI al subir a 2.30+.

---

## 7. MEDIUM no remediados (justificación)

Fuera del gate CRITICAL/HIGH; detectados con `dotnet list package --vulnerable`:

| Proyecto | Paquete | Severidad | Justificación |
| -------- | ------- | --------- | ------------- |
| .NET | KubernetesClient 12.1.1 | Moderate | Transitivo vía HealthChecks UI; no hay ruta de upgrade sin major de HealthChecks (9.x). Riesgo acotado: no se despliega contra cluster K8s en este reto. |
| .NET | SharpCompress 0.30.1 | Moderate | Transitivo vía cadena HealthChecks/Kubernetes; sin fix en lockfile actual sin breaking change. |

Angular: Trivy con `--severity CRITICAL,HIGH` no reporta MEDIUM en dependencias de producción tras Fase A/B.

---

## 8. Cambios realizados

### Fase A

**Angular:** `package.json`, `package-lock.json`, `standalone: false` en 7 componentes.

**CatalogMicroservice + Middleware:** `Snappier` 1.3.1, `Microsoft.Extensions.Caching.Memory` 8.0.1, `System.Text.Json` 8.0.5, `RestorePackagesWithLockFile`.

### Fase B

**DevSecOps:** `.github/workflows/sca.yml` (Trivy gate en CI), `.github/dependabot.yml` (npm, nuget, actions).

**Paquetes .NET directos:** HealthChecks 8.0.1, Serilog 8.0.3, Swashbuckle 6.9.0, JwtBearer 8.0.11, Containers.Tools 1.21.0.

**SBOM:** `reports/*/sbom-cyclonedx.json` (Trivy CycloneDX).

---

## 9. Verificación funcional

| Proyecto | Build | Resultado |
| -------- | ----- | --------- |
| Angular | `cd angular-app && npm run build` | OK |
| .NET | `dotnet build catalog-microservice/CatalogMicroservice/CatalogMicroservice.csproj` | OK |

---

## 10. Estructura del repositorio

```text
.
├── README.md
├── .github/workflows/sca.yml
├── .github/dependabot.yml
├── reports/
│   ├── README.md         (índice de evidencia)
│   ├── angular/          (01/02/03-*.md legibles + .txt crudos opcionales)
│   └── catalog-microservice/
├── angular-app/
└── catalog-microservice/   (CatalogMicroservice + Middleware)
```

- **Pre-análisis:** `reports/*/01-before-trivy.md`
- **Post Fase A:** `reports/*/02-after-min-fix-trivy.md`
- **Post Fase B:** `reports/*/03-after-modernize-trivy.md`

---

## 11. CI — gate de seguridad

Workflow `.github/workflows/sca.yml`:

- Dispara en push/PR a `main`
- Escanea `angular-app/` y `catalog-microservice/` con Trivy
- **Falla el pipeline** si hay CRITICAL o HIGH (`exit-code: 1`)

---

## 12. Limitaciones

- Angular saltó **14 → 20** en Fase A (no había parches en 14.x); la “modernización por majors” ocurrió ahí por necesidad del gate.
- Fase B no migró a Angular 21 ni a `application` builder para limitar breaking changes.
- `CatalogMicroservice` incluye `Middleware` (ProjectReference) extraído del monorepo upstream.
- MongoDB.Driver no se subió a 2.30+ por conflicto de ensamblado con HealthChecks UI.

---

## 13. Checklist de entrega

- [x] Código Angular y .NET modificados y compilando
- [x] Reportes Trivy 01, 02, 03 commiteados
- [x] 0 CRITICAL y 0 HIGH (Fase A y B)
- [x] MEDIUM justificados (sección 7)
- [x] README con tablas, decisiones y comandos
- [x] Repo Git público
- [x] CI Trivy + Dependabot + SBOM
