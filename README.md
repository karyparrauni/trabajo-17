# TP16 — Escaneo de Seguridad de Contenedores, Dependencias (SCA) e IaC con Trivy

[![CI/CD Pipeline - Trivy Security](https://github.com/karyparrauni/trabajo-16/actions/workflows/cicd.yml/badge.svg)](https://github.com/karyparrauni/trabajo-16/actions)

## 📌 Visión General del Práctico
El **TP16** consolida la integración de **Trivy** como un portón de seguridad (*security gate*) integral dentro del pipeline de CI/CD para la aplicación contenerizada. A través de este práctico se aborda la auditoría en tres dominios clave:
1. **SCA (Software Composition Analysis):** Detección de vulnerabilidades en las librerías y paquetes del backend (Python/Flask).
2. **Seguridad en Contenedores:** Inspección de vulnerabilidades (CVEs) en las capas de la imagen Docker final.
3. **Seguridad en Infraestructura como Código (IaC):** Análisis de desconfiguraciones en los manifiestos de Kubernetes mediante el patrón **"Render First, Validate Second" (TP10B)**.

---

## 🏗️ Arquitectura del Pipeline CI/CD (3 Fases)

El pipeline implementa el principio de **"Build Once, Test Everywhere"**, garantizando que la misma imagen compilada e inmutable en la primera fase sea la auditada y desplegada en producción.

[Fase 1: Build & Package] ➔ [Fase 2A: Trivy Andon Cord] ➔ [Fase 3: Release & Deploy] (Imagen inmutable .tar)       (HIGH, CRITICAL -> exit 1)     (Push a Docker Hub & Helm) │ [Fase 2B: Reporte Informativo] (LOW, MEDIUM -> exit 0)

### 📋 Matriz de Control e Integración de Fases

| Fase | Job | Dominio Auditado | Severidades | Exit Code | Acción ante Hallazgos | Evidencias / Artefactos |
|---|---|---|---|---|---|---|
| **Fase 1** | `build-and-package` | Compilación Docker | — | 0 | Genera paquete inmutable `app-image.tar` | Artefacto de imagen efímera |
| **Fase 2A** | `trivy-andon-cord` | Contenedor, SCA e IaC Renderizado | `HIGH, CRITICAL` | **1** | **Andon Cord Activo**: Cancela el flujo y detiene el despliegue | Resumen en `$GITHUB_STEP_SUMMARY` |
| **Fase 2B** | `trivy-audit-report` | Contenedor y Dependencias | `LOW, MEDIUM` | **0** | **Informativo**: Registra observaciones menores | Artefacto `.txt` descargable |
| **Fase 3** | `deploy-k8s-helm` | Publicación y Despliegue | — | 0 | Promueve imágenes a Docker Hub y ejecuta Helm | Release activo en Kubernetes |

---

## ⚙️ Patrón TP10B: "Render First, Validate Second"

Para prevenir **falsos positivos** generados por analizadores estáticos al evaluar código de plantillas (como las sintaxis de Go en Helm `{{ .Values... }}`), el pipeline aplica el patrón **TP10B**:

1. Antes del escaneo de IaC, se ejecuta `helm template` para procesar todas las variables y condicionales del chart (`./devops-tp12/chart`).
2. Se genera el manifiesto plano `manifests-rendered-prod.yaml`.
3. Trivy ejecuta la instrucción `trivy config manifests-rendered-prod.yaml` sobre el YAML final, logrando una inspección precisa sin errores de sintaxis.

---

## 🚨 Política de Control: Andon Cord

En cumplimiento con los estándares DevSecOps, la **Fase 2A** actúa como un freno de mano automático (*Andon Cord*):
* Si Trivy detecta cualquier vulnerabilidad clasificada como **`HIGH`** o **`CRITICAL`** en las dependencias, en la imagen Docker o en los manifiestos renderizados de Kubernetes, el paso finaliza con código de salida `1`.
* Esto cancela inmediatamente la ejecución del pipeline, impidiendo que la **Fase 3 (`deploy-k8s-helm`)** publique o despliegue artefactos vulnerables en el clúster.

---

## 💻 Ejecución y Verificación en Entorno Local

Para validar la seguridad antes de enviar los cambios al repositorio:

```bash
# 1. Renderizar el Helm Chart (Patrón TP10B)
helm template mi-app ./devops-tp12/chart > manifests-rendered-prod.yaml

# 2. Escaneo de dependencias backend (SCA)
trivy fs ./backend

# 3. Escaneo de la imagen Docker compilada
docker build -t devops-portfolio:latest ./backend
trivy image --severity HIGH,CRITICAL devops-portfolio:latest

# 4. Auditoría de IaC sobre el manifiesto renderizado
trivy config manifests-rendered-prod.yaml
📁 Entregables del TP16
Workflow CI/CD: .github/workflows/cicd.yml (Arquitectura en 3 fases y Andon Cord).
Manifiesto Renderizado: manifests-rendered-prod.yaml (Generado según el patrón TP10B).
Script de Verificación: scripts/verificar-trivy.sh.
Reporte de Auditoría: Artefacto reporte-vulnerabilidades-trivy-low-medium.
Evidencias de Control: Detención del pipeline en la Fase 2A ante las desconfiguraciones HIGH detectadas en la infraestructura renderizada.

---
