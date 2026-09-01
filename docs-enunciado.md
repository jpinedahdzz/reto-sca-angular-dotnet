#  Reto Técnico: Análisis y Remediación de Vulnerabilidades de Dependencias — Angular + .NET

**Rol:** Analista Desarrollador Fullstack con énfasis en DevSecOps
**Duración estimada:** 6 a 8 horas
**Entregable:** Repositorio Git público (fork o propio) + README

---

## Contexto

Recibes dos proyectos open source con dependencias desactualizadas y vulnerables: uno en Angular y uno en ASP.NET Core. Tu trabajo es analizarlos, remediarlos y demostrar con evidencia que el estado final no tiene vulnerabilidades críticas ni altas.

---

## Proyectos base

Buscarlos en github:

| Proyecto | Repositorio |
|----------|-------------|
| Frontend Angular | [`cidaluna/ecommerce-angular-14`](https://github.com/cidaluna/ecommerce-angular-14) |
| Backend .NET | [`aelassas/microservices`](https://github.com/aelassas/microservices) — microservicio `CatalogMicroservice` |

---

## Lo que se espera

1. **Escanear** ambos proyectos con **Trivy en modo `fs`** (filesystem / SCA) y registrar la línea base de vulnerabilidades.
2. **Analizar** cada hallazgo de severidad CRITICAL y HIGH: qué paquete lo origina, si es directo o transitivo, y cuál es el impacto real en el contexto de la aplicación.
3. **Remediar** las vulnerabilidades encontradas actualizando o reemplazando las dependencias afectadas. Los proyectos deben seguir compilando y funcionando después de los cambios.
4. **Verificar** que el escaneo final con Trivy arroje **0 CRITICAL y 0 HIGH** en ambos proyectos.
5. **Documentar** todo en un README propio: qué se encontró, qué se hizo, por qué, y cómo reproducir el análisis. indicar donde queda el pre-analisis, y el post-analisis solucionado.

---

## Criterio de aceptación

- `trivy fs --scanners vuln --severity CRITICAL,HIGH` → **0 resultados** en Angular y en .NET al final. Si no se puede solucionar, justificar el porqué.
- Las vulnerabilidades MEDIUM no remediadas deben justificarse individualmente.
- Ambos proyectos compilan y corren sin errores tras los cambios.

---

## Entregables mínimos

- Código fuente modificado (Angular y .NET) con los cambios de dependencias.
- Reportes Trivy **antes** y **después** de la remediación, en texto o JSON, commiteados en el repositorio.
- `README.md` con: tabla de hallazgos, decisiones técnicas tomadas, comandos exactos para reproducir el escaneo final.

---

## Criterios de evaluación

| Criterio | Peso |
|---|---|
| Calidad y cobertura del análisis inicial (comprensión de cada CVE) | 25% |
| Efectividad y criterio de las remediaciones aplicadas | 30% |
| Verificación final con 0 CRITICAL y 0 HIGH, evidencia reproducible | 20% |
| Claridad del README y justificación de decisiones | 25% |

**Puntos extra:** pipeline de CI con gate de seguridad en GitHub Actions, configuración de Dependabot o Renovate, generación de SBOM.

---

## Recursos

- Trivy: https://aquasecurity.github.io/trivy/
- Angular Update Guide: https://update.angular.io
- dotnet list package --vulnerable: https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-list-package
- OSV (base CVEs open source): https://osv.dev
