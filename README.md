# Home Infrastructure

`home-infrastructure` es un proyecto personal de Home Lab diseñado como una
plataforma real de infraestructura doméstica, construida con prácticas de
DevOps y Platform Engineering adaptadas a escala doméstica.

El objetivo es crear una plataforma útil, reproducible y operable para servicios
de casa: red, acceso privado, automatización, observabilidad, copias,
recuperación y servicios personales. El proyecto también funciona como
laboratorio práctico para aprender operando sistemas reales, no solo
desplegando ejemplos.

El repositorio, la instalación base del sistema operativo, la base de storage
en `/srv` y una primera plataforma k3s single-node ya existen. El proyecto aún
no tiene servicios domésticos de producción, GitOps, observabilidad completa ni
hardware dedicado de red/storage; esos pasos siguen siendo trabajo futuro.

## Objetivo Técnico

La plataforma debe permitir construir y operar un entorno doméstico con estas
propiedades:

- Git como fuente de verdad.
- Cambios reproducibles y revisables.
- Configuración versionada cuando aporte valor operativo.
- Automatización progresiva.
- Servicios observables.
- Acceso privado y seguro.
- Secretos gestionados de forma cifrada.
- Datos persistentes fuera del sistema efímero de contenedores.
- Backups con restauración probada.
- Separación entre red crítica y plataforma de aplicaciones.
- Documentación técnica suficiente para reconstruir, operar y recuperar.

La intención no es copiar complejidad empresarial sin necesidad. La intención es
usar prácticas profesionales donde mejoren fiabilidad, seguridad, recuperación o
aprendizaje.

## Plataforma Que Se Quiere Construir

La dirección técnica es una infraestructura doméstica con varias capas:

```text
Internet / ISP / ONT
        |
router / firewall / red domestica
        |
host inicial Home Lab
        |
plataforma de aplicaciones
        |
servicios domesticos y personales
        |
observabilidad + backups + restauracion
```

La red crítica debe seguir funcionando aunque falle la plataforma de
aplicaciones. Por eso routing, DHCP, firewall y acceso de recuperación no deben
depender de Kubernetes por defecto.

A largo plazo, la arquitectura puede separar dominios físicos:

```text
router/firewall != servidor de aplicaciones != backup/storage
```

Esa separación es dirección objetivo. No está desplegada todavía.

## Estado Actual

Estado verificado:

- Ruta local: `/home/kiyana/Desktop/project-home`.
- El proyecto ya es un repositorio Git válido.
- Rama principal: `main`.
- Remote: `origin` -> `git@github.com:RubenLopSol/home-infrastructure.git`.
- Baseline inicial: `01595d3` (`Initial project baseline`).
- Ubuntu Server 26.04.1 LTS está instalado en `homelab-server-01`.
- La Fase 1 de instalación e inventario inicial del host está completada.
- La Fase 3 de storage, backup y recuperación inicial está completada.
- La Fase 4 de plataforma de aplicaciones está en curso con k3s single-node ya
  instalado en `homelab-server-01`.
- Rancher Local Path Provisioner está instalado como componente Kustomize en
  `kubernetes/platform/storage/local-path-provisioner/`.
- La StorageClass `local-path-srv` existe, es la predeterminada y provisiona en
  `/srv/k3s/storage` con política `Retain`.
- Los smoke tests stateless, persistent `hostPath` temporal y dynamic PVC con
  backup/restore han sido validados y limpiados.
- No hay servicios domésticos o personales de producción desplegados.
- No existen directorios de implementación para Ansible, Terraform,
  monitorización, backups o red.
- La revalidación física detallada de la red queda para la fase de diseño de
  red.

Hardware y sistema operativo decididos para el primer host:

| Elemento | Valor | Estado |
|---|---|---|
| Host inicial | Acer Aspire E5-571G | DECIDED |
| CPU | Intel i3-4005U | DECIDED |
| RAM | 12 GB | DECIDED |
| Disco | Kingston 240 GB SSD | DECIDED |
| Sistema operativo | Ubuntu Server 26.04.1 LTS | INSTALLED |

Instalación base observada:

- Kernel tras primer arranque: `7.0.0-31-generic`.
- Arquitectura: `x86_64`.
- Modo de arranque: UEFI.
- Sin entorno gráfico.
- Usuario administrativo inicial: `ruben` con sudo.
- Red por Ethernet con DHCP y reserva en el router: `192.168.1.181/24`.
- OpenSSH Server instalado; acceso por clave SSH verificado y autenticación SSH
  por contraseña deshabilitada.

## Stack Técnico Previsto

Esta tabla separa compromisos actuales, propuestas y decisiones pendientes. Un
elemento `PROPOSED` forma parte de la dirección técnica, pero todavía no está
implementado ni cerrado como decisión final.

| Área | Tecnología / enfoque | Estado |
|---|---|---|
| Fuente de verdad | Git | DECIDED |
| Repositorio local | Git en rama `main` | DECIDED / CURRENT |
| Remote / hosting | GitHub: `RubenLopSol/home-infrastructure` | DECIDED / CURRENT |
| Host inicial | Acer Aspire E5-571G | DECIDED |
| Sistema operativo | Ubuntu Server 26.04.1 LTS en `homelab-server-01` | DECIDED / CURRENT |
| Gestión de configuración | Herramienta exacta pendiente | TBD |
| Plataforma de aplicaciones | Kubernetes para servicios adecuados | DECIDED / CURRENT |
| Distribución Kubernetes | k3s `v1.36.4+k3s1` single-node en `homelab-server-01` | DECIDED / CURRENT |
| Kubernetes storage inicial | Rancher Local Path Provisioner, StorageClass `local-path-srv`, data root `/srv/k3s/storage` | DECIDED / CURRENT |
| CI / validación | GitHub Actions | PROPOSED |
| GitOps | Argo CD | PROPOSED |
| Progressive delivery | Argo Rollouts donde aporte valor | PROPOSED |
| Empaquetado/configuración apps | Helm/Kustomize sobre artefactos fijados | PROPOSED |
| Secretos | SOPS + age u otro mecanismo cifrado revisado | PROPOSED / TBD |
| DNS local / filtrado | AdGuard Home con fallback independiente | PROPOSED |
| Acceso remoto privado | WireGuard fuera del plano crítico de Kubernetes | PROPOSED |
| Segmentación de red | VLANs para home, servers, IoT/security, guests, VPN | PROPOSED |
| Firewall/router dedicado | Routing, DHCP, VLANs, firewall, WireGuard | PROPOSED |
| Storage / backups | NAS futuro con 2 discos en RAID1; disco externo `BACKUP_2TB` como backup provisional | DECIDED direction / TEMPORARY current |
| Observabilidad | Prometheus, Grafana, Loki, Alertmanager | PROPOSED |
| Recuperación | Matriz por servicio + restauración probada | PROPOSED |
| Host futuro más grande | Torre 32 GB / 1 TB discutida previamente | PROPOSED, no comprada/desplegada |

## Modelo De Operación

El modelo previsto es Git-centered:

```text
engineer
   |
Git
   |
validacion / CI
   |
estado deseado
   |
automatizacion / GitOps
   |
Home Lab
   |
observabilidad
   |
feedback / rollback / recovery
```

Este flujo existe parcialmente: Git y documentación ya gobiernan las decisiones
del repositorio, y k3s/storage inicial tienen manifests versionados. CI, GitOps,
observabilidad completa y despliegue reproducible de servicios siguen
pendientes.

La configuración manual no queda prohibida para investigación, diagnóstico o
recuperación, pero el estado duradero de la plataforma debe acabar representado
en Git, documentación técnica o backups restaurables según corresponda.

## Principios De Diseño

Principio central:

```text
Production-grade practices, adapted to homelab scale.
```

Principios prácticos:

- Diseñar antes de implementar.
- Separar estado actual, arquitectura objetivo, decisiones y referencia
  histórica.
- No tratar documentación histórica como estado actual.
- No crear directorios de implementación antes de necesitarlos.
- Mantener el plano de red crítico independiente de Kubernetes.
- Versionar lo que afecte a reproducibilidad.
- No guardar secretos en plaintext en Git.
- No aceptar un servicio como terminado solo porque arranca.
- Exigir persistencia, backup y restauración para servicios con estado.
- Mantener el coste, complejidad y consumo acordes al entorno doméstico.

## Servicios Y Capacidades Previstas

La plataforma está pensada para soportar, de forma progresiva:

- DNS local y filtrado.
- Acceso remoto privado.
- Home Assistant y automatización doméstica.
- MQTT y servicios de integración.
- Observabilidad de host, red, servicios y plataforma.
- Backups locales y externos.
- Servicios personales como sincronización, multimedia o ficheros.
- Cámaras/NVR si se justifica por requisitos reales de retención, red y
  almacenamiento.

Cada servicio deberá evaluarse por seguridad, persistencia, backup,
monitorización, restauración y adecuación a Kubernetes antes de incorporarse.

## Roadmap Técnico

La evolución prevista está separada en fases para poder construir el proyecto
paso a paso sin confundir preparación con infraestructura ya desplegada.

Resumen de fases:

| Fase | Objetivo |
|---|---|
| 0 | Contexto, baseline del repositorio y Git como fuente de verdad completados |
| 1 | Instalación e inventario inicial del host completados |
| 2 | Red física/firewall: baseline actual completado; diseño futuro pendiente |
| 3 | Host, storage y backup foundation completados |
| 4 | Plataforma k3s inicial en curso con storage dinámico validado |
| 5 | GitOps y ciclo de entrega reproducible si procede |
| 6 | Observabilidad, seguridad, secretos y recuperación |
| Later | Servicios domésticos y fiabilidad continua |

El siguiente paso inmediato del proyecto es cerrar o ampliar la Fase 4:
definir el primer workload reproducible desde Git y decidir si se introduce
validación/GitOps antes de servicios domésticos reales.

El roadmap detallado está en `docs/architecture/project-roadmap.md`.

## Organización Del Repositorio

Estructura actual:

```text
README.md
AGENTS.md
.ai/
docs/
kubernetes/
```

`README.md` es la entrada humana al proyecto.

`AGENTS.md` es la entrada para agentes de IA y contiene reglas operativas para
trabajar en el repositorio.

`.ai/` contiene contexto y prompts específicos para trabajo asistido por IA. No
es implementación del Home Lab.

`docs/` contiene documentación técnica del proyecto. Debe ser útil de forma
independiente de Codex/ChatGPT.

Directorios futuros de implementación, como Ansible, Terraform, scripts,
monitorización o backups, se crearán solo cuando haya trabajo real que lo
justifique. `kubernetes/` ya existe porque contiene la plataforma k3s inicial y
smoke tests versionados.

## Documentación Técnica

Documentos actuales:

- `docs/architecture/home-infrastructure-target.md`: arquitectura objetivo.
- `docs/architecture/initial-host-inventory.md`: inventario/checkpoint inicial
  del host tras la instalación de Ubuntu Server.
- `docs/architecture/project-roadmap.md`: fases de creación del proyecto,
  tareas y criterios de salida.
- `docs/architecture/current-network-observations.md`: observaciones de red
  históricas pendientes de revalidación.
- `docs/architecture/current-network-inventory.md`: inventario de red actual
  verificado desde el servidor.
- `docs/architecture/state-and-recovery-model.md`: modelo de estado, backup y
  recuperación.
- `docs/architecture/definition-of-done.md`: criterios de finalización para
  componentes.
- `docs/architecture/initial-storage-foundation.md`: layout inicial de LVM y
  `/srv`.
- `docs/architecture/initial-backup-and-restore.md`: modelo temporal de backup
  y restore usando `BACKUP_2TB`.
- `docs/architecture/application-platform-phase-4.md`: decisión y evidencia de
  la plataforma k3s inicial.
- `docs/architecture/initial-k3s-storage.md`: StorageClass `local-path-srv`,
  Local Path Provisioner y smoke test dinámico.
- `docs/reference/openpanel-project-devops.md`: referencia histórica del
  proyecto OpenPanel / Project-DevOps.
- `docs/reference/original-context/`: documentos originales archivados como
  fuente histórica.

Futuros ADRs y runbooks se crearán cuando existan decisiones o procedimientos
que lo justifiquen.
