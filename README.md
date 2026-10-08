# 🚀 DevOps & DevSecOps Portfolio: Automated Scripts, Infrastructure & Security Pipelines

<div align="center">

![CI/CD Pipeline](https://github.com/karyparrauni/trabajo-17/actions/workflows/cicd.yml/badge.svg)
![Security Scans](https://img.shields.io/badge/Security-Gitleaks%20%7C%20Trivy%20%7C%20Semgrep%20%7C%20ZAP%20%7C%20Threagile-blue)
![Kubernetes](https://img.shields.io/badge/Orchestration-Kubernetes%20%7C%20Helm%20%7C%20k3d-326CE5)
![IaC](https://img.shields.io/badge/IaC-Terraform-7B42BC)
![Licencia](https://img.shields.io/badge/License-MIT-green.svg)

**Repositorio Central Integrador de la Carrera DevSecOps**
*Un recorrido práctico desde la administración del sistema operativo Linux hasta la automatización de la ciberseguridad en el ciclo de vida de entrega de software.*

</div>

---

## 📋 Tabla de Contenidos
- [Sobre este Portfolio](#-sobre-este-portfolio)
- [Arquitectura Integrada de la Aplicación](#-arquitectura-integrada-de-la-aplicación)
- [Resumen del Recorrido Técnico (TP01 - TP17)](#-resumen-del-recorrido-técnico-tp01---tp17)
- [Fábrica de CI/CD y Matriz de Seguridad](#-fábrica-de-cicd-y-matriz-de-seguridad)
- [Stack Tecnológico](#-stack-tecnológico)
- [Guía de Ejecución y Comandos Útiles](#-guía-de-ejecución-y-comandos-útiles)
- [Fundamentos Metodológicos](#-fundamentos-metodológicos)

---

## 🛠️ Sobre este Portfolio

Este proyecto consolida el aprendizaje práctico de la fábrica de software de una aplicación multinivel (**Notes App**) construida con **Python Flask, NGINX y PostgreSQL**. 

A lo largo de 17 trabajos prácticos integradores, el proyecto evolucionó desde scripts locales de administración hasta convertirse en un sistema distribuido en **Kubernetes**, aprovisionado con **Terraform**, empaquetado con **Helm**, monitoreado mediante **Prometheus & Grafana** y blindado mediante controles de ciberseguridad automatizados (*Shift Left* y *Runtime Security*).

---

## 📐 Arquitectura Integrada de la Aplicación

El ecosistema en producción opera bajo una arquitectura de **7 contenedores** desplegados en clústeres de Kubernetes (k3s/k3d/Minikube):

                   [ Cliente Web / Navegador ]
                                │
                            (HTTP / 80)
                                ▼
                    [ Ingress Controller NGINX ]
                                │
     ┌──────────────────────────┴──────────────────────────┐
     │ (Servicio Estático)                                 │ (Routing API /api)
     ▼                                                     ▼
[ Frontend NGINX ]                                   [ Backend Flask (WSGI Gunicorn) ] │         │ (PostgreSQL Protocol)      │ (Scrape /metrics) v         ▼ [ Database PostgreSQL ]  [ Prometheus ] (Volume: postgres-pvc)      │ ▼ [ Grafana ] (Dashboards / Alerts) ▲       ▲ │       │ [ Node Exporter ] [ cAdvisor ]

---

## 🗂️ Resumen del Recorrido Técnico (TP01 - TP17)

### 🔹 Módulo 1: Fundamentos de Sistemas, Control de Versiones y Redes
* **TP01 — Automatización Bash:** Script `sistema.sh` para backups con timestamp, limpieza de logs con retención y reporte de salud del sistema (CPU, RAM, Disco).
* **TP02 — Gestión de Usuarios y Principio de Menor Privilegio:** Creación del usuario de sistema `devops-deploy` y auditoría estricta de permisos de archivos.
* **TP03 — Flujo Gitflow:** Estrategia de ramas (`main`, `develop`, `feature/*`, `hotfix/*`), merges no-fast-forward (`--no-ff`) y etiquetado de releases (`v1.0`).
* **TP04 — Configuración Multi-Entorno & Redes:** Estructura YAML multi-entorno (`app-config.yml`) y script de diagnóstico de red (`ping`, `dig`, `curl`, `ss`, `ip`).

### 🔹 Módulo 2: Contenedores, CI/CD y Monitoreo
* **TP05 — Containerización con Docker:** Dockerfile optimizado (`python:3.12-slim`), multi-stage builds, ejecución no-root y publicación en Docker Hub.
* **TP06 — Orquestación con Docker Compose:** Stack multi-contenedor de la Notes App (Frontend Nginx, Backend Flask, Database Postgres) con *healthchecks* y volúmenes persistentes.
* **TP07 — Pipeline de CI/CD Automatizado:** Integración de GitHub Actions con triggers para `push` y `pull_request`, ejecución de tests con `pytest` y *linting* de código.
* **TP08 / TP12C — Observabilidad & Monitoreo:** Stack completo con **Prometheus, Grafana, Node Exporter y cAdvisor**. Instrumentación de la app con `prometheus_client` y alertas en tiempo real.

### 🔹 Módulo 3: Orquestación en Kubernetes e Infraestructura como Código (IaC)
* **TP09 — Despliegue Nativo en Kubernetes:** Creación de `Namespace`, `Secrets` Base64, `ConfigMaps`, `PersistentVolumeClaim` (PVC), `Deployments` con probes y `Services`.
* **TP10 / TP10B — Helm Charts & Ingress:** Empaquetado parametrizado con Helm, enrutamiento por rutas en NGINX Ingress, HPA para autoscaling y aplicación del patrón **"Render First, Validate Second" (TP10B)** para eliminar falsos positivos.
* **TP11 — Infraestructura como Código (Terraform):** Aprovisionamiento declarativo mediante módulos HCL (`network`, `storage`, `app`) y gestión segura del `terraform.tfstate`.
* **TP12 / TP12B — Portafolio Central e Integración Total:** Consolidación de repositorios y validación automatizada del estado de salud de todos los proyectos.

### 🔹 Módulo 4: DevSecOps, Auditoría de Seguridad & Secret Scanning
* **TP13A/B/C — Pruebas Dinámicas (DAST con OWASP ZAP):** Automatización de escaneos DAST con ZAP Automation Framework (`zap-plan.yml`), Spider, Active Scan, sanitizador SARIF (`sanitize_zap_sarif.py`) y publicación en GitHub Code Scanning.
* **TP14 — Modelado de Amenazas Declarativo (Threagile):** Definición de la arquitectura en `threagile.yaml` (*Threat Modeling as Code*), evaluación de límites de confianza (*trust boundaries*) y generación automática de reportes PDF y diagramas DFD.
* **TP15 — Análisis Estático de Código Fuente (SAST con Semgrep):** Auditoría multilenguaje sobre Python/Flask, Dockerfiles, Terraform y Kubernetes buscando vulnerabilidades del OWASP Top 10.
* **TP16 — Escaneo Multidominio de Contenedores e IaC (Trivy Security Gate):** Inspección de dependencias (SCA en `requirements.txt`), capas de imágenes Docker e IaC renderizado (`manifests-rendered-prod.yaml`) dentro de una arquitectura de pipeline de 3 fases.
* **TP17 — Secret Scanning & Ciberseguridad en Git (Gitleaks):** Control del historial de Git con **Gitleaks**, implementación de *Pre-Commit Hooks* locales, política bloqueante (**Andon Cord**) en CI/CD con `fetch-depth: 0` y remediación del historial con `git-filter-repo`.

---

## 🛡️ Fábrica de CI/CD y Matriz de Seguridad

El pipeline de **GitHub Actions** (`.github/workflows/cicd.yml`) opera bajo una **arquitectura inmutable de 3 Fases**, garantizando que el artefaco probado sea exactamente el mismo que se despliega en producción (*Build Once, Test Everywhere*):

┌───────────────────────────────┐ │   FASE 1: BUILD & PACKAGE     │  --> Compila la imagen Docker y exporta el tarball 'app-image.tar' └───────────────┬───────────────┘ │ ▼ ┌───────────────────────────────┐ │ FASE 2: AUDITORÍAS PARALELAS  │ │ (Semgrep, Trivy, Gitleaks,    │  --> Ejecuta controles SAST, SCA, DAST, IaC y Secret Scanning. │  Threagile, OWASP ZAP)        │      Aplica la política ANDON CORD ante severidades HIGH/CRITICAL. └───────────────┬───────────────┘ │ ▼ ┌───────────────────────────────┐ │  FASE 3: RELEASE & DEPLOY     │  --> Promueve la imagen inmutable a Docker Hub y ejecuta el └───────────────────────────────┘      despliegue seguro en Kubernetes con Helm.

### 📊 Matriz de Herramientas de Ciberseguridad Integradas

| Herramienta | Tipo de Análisis | Alcance / Objetivos | Política en Pipeline |
| :--- | :--- | :--- | :--- |
| **Gitleaks (TP17)** | Secret Scanning | Historial de Git, Commits, API Keys, Passwords | **Andon Cord Activo** (`exit-code: 1`) |
| **Trivy (TP16)** | SCA / Container / IaC | `requirements.txt`, Imagen Docker, Helm Renderizado | **Andon Cord Activo** (`severity: HIGH,CRITICAL`) |
| **Semgrep (TP15)** | SAST Multilenguaje | Código Python/Flask, Dockerfile, Terraform, K8s | Reporte SARIF + Integración GitHub Security |
| **Threagile (TP14)**| Threat Modeling | Análisis declarativo de arquitectura (`threagile.yaml`) | Generación de artefactos PDF y DFD |
| **OWASP ZAP (TP13)**| DAST Activo/Pasivo | Endpoints HTTP en ejecución, Fuzzing, XSS, SQLi | Reporte HTML + SARIF Sanitizado |

---

## 💻 Stack Tecnológico

| Categoría | Tecnologías Utilizadas |
| :--- | :--- |
| **Sistemas & Scripting** | Linux (Debian/Ubuntu), Bash Scripting, Cron Jobs |
| **Control de Versiones** | Git, GitHub, Gitflow, `git-filter-repo` |
| **Contenedores & IaC** | Docker, Docker Compose, Helm v3, Terraform HCL |
| **Orquestación K8s** | Kubernetes (kubectl, Ingress NGINX, k3d, K3s, Minikube) |
| **CI/CD** | GitHub Actions, Pre-Commit Hooks, Docker Buildx |
| **Observabilidad** | Prometheus, Grafana, Node Exporter, cAdvisor |
| **Seguridad (DevSecOps)**| Gitleaks, Trivy, Semgrep, OWASP ZAP, Threagile |
| **Desarrollo Base** | Python 3.12, Flask, Gunicorn, PostgreSQL, NGINX |

---

## 🚀 Guía de Ejecución y Comandos Útiles

### 1. Pruebas Locales del Stack Completo (Docker Compose)
```bash
# Clonar el repositorio
git clone https://github.com/karyparrauni/trabajo-17.git
cd trabajo-17

# Levantar la aplicación de notas junto con el stack de monitoreo
docker compose up -d --build

# Verificar el estado de los servicios y ejecutar el healthcheck
bash scripts/healthcheck.sh
2. Auditorías Locales de Ciberseguridad
# Escaneo local de secretos con Gitleaks
gitleaks detect --source . -v

# Probar la protección local de staged en Git
gitleaks protect --staged -v

# Escaneo de vulnerabilidades en contenedor e IaC con Trivy
trivy fs ./backend
helm template mi-app ./guia-10/devops-portfolio > manifests-rendered-prod.yaml
trivy config manifests-rendered-prod.yaml

# Ejecución del script de verificación técnica integrador
bash scripts/verificar-gitleaks.sh
📚 Fundamentos Metodológicos
Este desarrollo aplica los principios fundamentales de las metodologías Las Tres Vías y Los 12 Factores:
La Primera Vía (Flujo y Pensamiento Sistémico): Optimización de la cadena de valor mediante imágenes inmunes e infraestructura declarativa que aceleran la entrega de código a producción.
La Segunda Vía (Retroalimentación Continua): Implementación del Andon Cord en CI/CD y telemetría en tiempo real con Prometheus/Grafana para detectar e interceptar fallas de forma inmediata.
La Tercera Vía (Aprendizaje y Experimentación Continua): Prácticas de refactorización continua, simulación de ataques DAST/Fuzzing y purga de deuda técnica para fortalecer la resiliencia del sistema.
Los 12 Factores: Externalización de configuraciones mediante variables de entorno, procesos stateless, paridad entre entornos de desarrollo/producción y tratamiento de logs como flujos de eventos.
