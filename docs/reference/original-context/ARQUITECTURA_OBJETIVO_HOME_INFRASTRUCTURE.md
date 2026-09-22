# Arquitectura objetivo — home-infrastructure

**Estado:** propuesta de Fase 0; no equivale a infraestructura ya desplegada.

## Resumen

`home-infrastructure` evoluciona los patrones de `Project-DevOps` para un
homelab real. Reutiliza GitHub Actions, GitOps con Argo CD, Kubernetes,
Helm/Kustomize, observabilidad, seguridad y recuperación. No reutiliza
OpenPanel como aplicación: desplegará software comunitario mantenido, con
versiones fijadas y configuración propia declarativa.

La red crítica permanece fuera de Kubernetes. Un fallo del clúster no debe
impedir que el enrutador/cortafuegos, DHCP o la conectividad básica de la casa sigan
funcionando.

## Esquema de alto nivel

```text
                                      INTERNET
                                         |
                                  +------+------+
                                  |  Nokia ONT  |
                                  +------+------+
                                         |
                           +-------------v--------------+
                           |   ENRUTADOR / CORTAFUEGOS   |
                           | NAT, enrutamiento, DHCP,    |
                           | VLAN, cortafuegos y WireGuard |
                           | (crítico; fuera de K8s)     |
                           +-------------+--------------+
                                         |
                 +-----------------------+------------------------+
                 |                       |                        |
          VLAN 10 HOGAR            VLAN 20 SERVIDORES        VLAN 30 IoT
          móviles, PC, TV          Ubuntu / nodos K8s        cámaras, luces,
          SSID HOGAR               NVR y almacenamiento      enchufes, sensores
                 |                       |                        |
          puntos de acceso Wi-Fi -- enlace troncal Ethernet / SSID por VLAN --+
                                         |
                         +---------------v----------------+
                         |   Kubernetes (uno o más nodos)  |
                         |---------------------------------|
                         | Argo CD, entrada, TLS, políticas |
                         | almacenamiento persistente: PVC |
                         |                                 |
                         | PLATAFORMA: Argo CD / Rollouts  |
                         | OBSERVABILIDAD: Prometheus,     |
                         | Grafana, Loki, Alertmanager     |
                         | HOGAR: Home Assistant + MQTT    |
                         | RED: AdGuard Home*              |
                         | APLICACIONES: Jellyfin,          |
                         | Syncthing, etc.                 |
                         +---------------+-----------------+
                                         |
                    datos persistentes + copias + copia externa
                                         |
                           restauración funcional probada
```

AdGuard puede ejecutarse en Kubernetes, pero DNS debe tener un mecanismo
alternativo de recuperación que no convierta la caída del clúster en una caída
total de DNS doméstico.

```text
Repositorio GitHub            GitHub Actions                 Argo CD
------------------            --------------                 -------
IaC + manifiestos      --->   valida seguridad,         ---> reconcilia
valores Helm/Kustomize        sintaxis y cambios             Git con clúster
documentación/ADR             (no despliega directo)         de forma continua
```

## Segmentación y ubicación de componentes

| Zona | Contenido previsto | Regla principal |
|---|---|---|
| VLAN 10 — HOGAR | Equipos personales, TV y Wi-Fi doméstico | Puede acceder a los servicios domésticos estrictamente necesarios |
| VLAN 20 — SERVIDORES | Ubuntu, nodos Kubernetes, NVR y almacenamiento | Zona de gestión y servicios; sin exposición pública directa |
| VLAN 30 — IoT/SEGURIDAD | Cámaras, luces, enchufes, sensores y calefacción | No accede a HOGAR/SERVIDORES por defecto; solo excepciones necesarias |
| VLAN 40 — INVITADOS | Invitados, si se habilita | Solo Internet |
| VLAN 50 — VPN | Clientes WireGuard, si se habilita | Acceso por política individual, no una red plana |

Los puntos de acceso deberán soportar el diseño elegido: preferentemente un
enlace troncal Ethernet hacia el enrutador/conmutador y SSIDs asignados a VLANs. El acceso de Home
Assistant a IoT, y la transmisión/descubrimiento entre VLANs, se habilitarán solo
mediante reglas explícitas. mDNS/SSDP no se asume funcional entre VLANs.

WireGuard pertenece al perímetro de red (enrutador/cortafuegos o un equipo dedicado
estable), no al clúster Kubernetes. Así el acceso remoto sigue siendo posible
para diagnosticar o recuperar el clúster si este falla.

## Modelo inicial de hardware

Esta es la dirección de diseño; no significa que el hardware ya exista o esté
comprado. El PC de trabajo no será un componente permanente de la plataforma.

```text
enrutador/cortafuegos dedicado  !=  servidor de aplicaciones  !=  NAS/copias

Enrutador/cortafuegos        Torre x86-64                   NAS dos bahías
  - Internet / DHCP            - Ubuntu + K3s inicial          - copias
  - VLANs / cortafuegos        - GitOps y observabilidad        - archivos
  - WireGuard                  - Home Assistant / MQTT          - contenido multimedia futuro
  - fuera de Kubernetes        - almacenamiento persistente      - no K8s inicial
```

| Equipo | Base de planificación | Función y límite |
|---|---|---|
| Enrutador/cortafuegos | x86-64 dedicado, tarjetas de red fiables, ~8 GB RAM y SSD; modelo pendiente de validar con ISP | Mantiene Internet, VLANs, DHCP, cortafuegos y WireGuard aunque Kubernetes falle |
| Servidor | Torre empresarial reacondicionada y ampliable, 32 GB RAM y NVMe de 1 TB como punto de partida | Ejecuta el clúster inicial de un nodo y sus cargas persistentes; no es HA |
| NAS | Dos bahías y dos discos CMR iguales en espejo | Recibe copias, comparte archivos y puede almacenar contenido multimedia; no ejecuta el clúster inicial |
| Conmutador | Gestionable, 802.1Q VLAN, puertos suficientes; PoE solo si se necesita | Conecta y segmenta enrutador, servidor, NAS y puntos de acceso |
| SAI | Dimensionado para enrutador, conmutador, servidor y NAS | Evita apagados bruscos y permite apagado controlado |

Los datos activos de las aplicaciones no dependerán inicialmente de un montaje
de red hacia el NAS: vivirán en almacenamiento persistente deliberado del
servidor y se copiarán automáticamente al NAS y a una ubicación externa.
Así, la caída del NAS no detiene todos los servicios; la pérdida del servidor
se recupera desde las copias. Un espejo de discos reduce el impacto de perder
un disco, pero no sustituye backups.

Orden previsto de compra: validar ISP/ONT y cortafuegos, conmutador gestionable,
servidor, NAS con discos, SAI y finalmente puntos de acceso/IoT/cámaras. No se comprarán
GPU, Coral, almacenamiento masivo de grabaciones, varios nodos ni alta
disponibilidad hasta medir necesidades de cámaras, retención, energía, ruido,
presupuesto y cableado Ethernet.

## Opciones de inversión de la primera fase

Los precios son orientativos en España a septiembre de 2026 y no incluyen
IoT, cámaras, puntos de acceso nuevos ni una suscripción de copia externa.
La ONT, el Sagemcom y los repetidores actuales se reutilizan temporalmente
hasta validar el diseño definitivo de red.

### Opción recomendada — base completa desde el inicio

Esta opción construye todos los dominios físicos necesarios: cómputo, red,
copias y alimentación protegida. Es la recomendada si se van a almacenar
datos domésticos importantes o se quiere avanzar sin sustituir hardware a los
pocos meses.

| Elemento | Configuración de referencia | Coste aproximado |
|---|---|---:|
| Servidor | Torre empresarial reacondicionada, Intel i5-9500 o equivalente, 32 GB RAM y NVMe de 1 TB | 520 € |
| Enrutador/cortafuegos | Dispositivo x86-64 N100/N150, 8 GB RAM, SSD 128 GB y 4 tarjetas de red Intel i226-V de 2.5 GbE | 260 € |
| Conmutador | Gestionable, 8 puertos Gigabit, VLAN 802.1Q, ACL e IGMP; por ejemplo TP-Link TL-SG2008 | 92 € |
| NAS | NAS de dos bahías; por ejemplo Synology DS225+ | 413 € |
| Discos NAS | 2 × discos duros CMR NAS de 4 TB en espejo: 4 TB útiles | 400 € |
| SAI | 900 VA con USB para monitorización y apagado controlado | 181 € |
| Copia independiente | Disco USB externo de 4 TB; se mantiene desconectado excepto durante copias | 171 € |
| Material menor | Cables Cat6, regleta protegida, adaptadores y etiquetado | 50 € |
| **Total estimado** |  | **2.087 €** |

Resultado: el servidor ejecuta las aplicaciones con almacenamiento persistente
local; el NAS recibe copias automáticas; el disco externo proporciona una
segunda copia local separada. Falta añadir una copia externa cifrada para
cumplir una estrategia 3-2-1 completa.

```text
ONT/Sagemcom temporal -> cortafuegos -> conmutador VLAN
                                         |        |
                                  torre servidor  NAS
                                         |
                              copias -> NAS -> disco USB -> copia externa
```

La torre no incluye GPU dedicada ni componentes de juegos: esos costes no
aportan valor a la plataforma inicial. Debe tener capacidad para añadir discos
SATA y tarjetas PCIe en el futuro.

### Opción contenida — empezar y crecer sin duplicar compras

Esta ruta permite construir y aprender la plataforma, pero no ofrece aún la
misma independencia de red ni el mismo nivel de recuperación local. Es válida
para la Fase 0–5, mientras no se almacenen datos domésticos irreemplazables.

| Elemento | Configuración de referencia | Coste aproximado |
|---|---|---:|
| Servidor | La misma torre empresarial reacondicionada, 32 GB RAM y NVMe de 1 TB | 520 € |
| SAI | 900 VA de entrada con USB y apagado controlado | 87 € |
| Copia independiente | Disco USB externo de 4 TB | 171 € |
| Material menor | Cables, regleta protegida y etiquetado | 50 € |
| **Total inicial estimado** |  | **828 €** |

En esta opción se mantiene el Sagemcom como enrutador y la red actual sin
VLANs. El disco USB recibe las copias y debe guardarse desconectado fuera del
servidor; también se configura una copia externa cifrada antes de incorporar
datos importantes. No se presenta como alta disponibilidad ni como sustituto
del NAS.

La ruta posterior no desperdicia componentes: se añade, en este orden,
enrutador/cortafuegos (260 €), conmutador VLAN (92 €), NAS (413 €) y dos discos
CMR (400 €). Tras estas ampliaciones, la opción contenida converge con la
opción recomendada.

### Criterio de elección

- Elegir la **opción recomendada** si el presupuesto de unos 2.100 € está
  disponible y se quiere una base estable desde el primer servicio doméstico.
- Elegir la **opción contenida** si se quiere aprender Kubernetes, GitOps y
  automatización primero, aceptando que todavía no habrá segmentación de red
  ni NAS hasta completar la ampliación.
- No incorporar Home Assistant con dispositivos reales, secretos importantes
  o información familiar sensible hasta disponer de copias local y externa
  verificadas.

## Separación de responsabilidades

| Capa | Responsabilidad | Fuente de verdad / recuperación |
|---|---|---|
| Red física | ONT, enrutador/cortafuegos, DHCP, WireGuard, puntos de acceso, VLANs y reglas | Configuración/exportación segura del equipo y guías operativas; no Kubernetes |
| Equipo anfitrión | Ubuntu, discos, acceso SSH, entorno de ejecución y base del clúster | Ansible/IaC, inventario y copia del equipo cuando aplique |
| Kubernetes | Ejecución de aplicaciones, red de pods, ingress y almacenamiento | Manifiestos GitOps; datos en almacenamiento persistente |
| Aplicaciones de origen | Home Assistant, AdGuard, Grafana, MQTT, etc. | Charts/operadores oficiales fijados a versión; valores propios en Git |
| Configuración propia | Alertas, paneles, políticas, DNS local y automatizaciones declarables | Git; secretos cifrados cuando corresponda |
| Datos/identidad | BBDD, configuraciones activas, pares VPN, tokens, emparejamientos y grabaciones | PVCs + copias cifradas + copia externa + restauración probada |

## Patrón de despliegue de servicios

```text
Proyecto de origen mantenido
  + chart de Helm / operador / imagen oficial con versión fijada
                         |
                         v
home-infrastructure
  + aplicación de Argo CD
  + values.yaml / Kustomize propios
  + recursos, límites, seguridad y persistencia
  + monitorización, alertas y copia definida
                         |
                         v
Argo CD aplica y reconcilia el estado deseado en Kubernetes
                         |
                         v
PVC / almacenamiento duradero (nunca solo sistema de archivos del contenedor)
                         |
                         v
copias automáticas + copia externa + prueba de restauración
```

No se copia ni se hace un fork de aplicaciones externas por defecto. Solo se
creará un fork si una modificación concreta de código o chart es inevitable y
se puede mantener de forma responsable.

## Recuperación ante pérdida total del equipo anfitrión

```text
Equipo anfitrión de reemplazo
  -> arranque base/endurecimiento automatizado
  -> Kubernetes + almacenamiento + Argo CD
  -> Argo CD redepliega desde Git
  -> restaurar secretos y datos persistentes desde copias
  -> pruebas funcionales: DNS, VPN, Home Assistant, MQTT y servicios críticos
```

El criterio de éxito no es que arranquen contenedores nuevos: es que la casa
recupere configuraciones, automatizaciones e integraciones sin tener que
reconfigurar manualmente los dispositivos soportados.

## Relación con Project-DevOps/OpenPanel

| Patrón previo | Adaptación en home-infrastructure |
|---|---|
| Repositorio de infraestructura separado de la aplicación | Un repositorio de plataforma; el proyecto de origen se consume como dependencia fijada |
| GitHub Actions valida cambios | CI valida Terraform/Ansible/manifiestos, seguridad y políticas |
| Argo CD como CD declarativo | Igual: reconciliación continua desde Git |
| Kustomize y Helm | Configuración propia sobre software comunitario mantenido |
| Argo Rollouts azul/verde | Solo para servicios donde aporte seguridad/valor operativo |
| Prometheus, Grafana, Loki, Alertmanager | Base de observabilidad de plataforma y hogar |
| Velero, MinIO y scripts de copias | Evolucionan a una política por servicio, copia externa y restauraciones probadas |
| Sealed Secrets | Evoluciona a SOPS + age, con recuperación explícita de las claves de descifrado |

## Decisiones de arquitectura de Fase 0

### Decisiones resueltas

| ID | Decisión | Resolución | Consecuencia / revisión |
|---|---|---|---|
| D-01 | Modelo físico inicial | Se adopta la **opción recomendada**: torre empresarial, enrutador/cortafuegos dedicado, conmutador VLAN, NAS, SAI y disco de copia independiente. | La inversión de referencia es 2.087 €. La lista de modelos concretos se valida antes de comprar. |
| D-02 | Plataforma de cómputo | Ubuntu y K3s en una torre empresarial de un nodo, con 32 GB RAM y NVMe de 1 TB. | Es un clúster funcional, pero no alta disponibilidad. Se revisará al existir carga medida o un segundo nodo. |
| D-03 | Estado y almacenamiento | Los servicios usan almacenamiento persistente local del servidor; el NAS no será inicialmente su volumen de trabajo. | Un fallo del NAS no detiene las aplicaciones. Un fallo del servidor se recupera desde copias. |
| D-04 | Estrategia de copias | 3-2-1: datos activos en servidor, copia automatizada al NAS en espejo, copia USB independiente y copia externa cifrada. | Se exige restauración documentada antes de declarar un servicio doméstico crítico como terminado. El proveedor externo y la retención exacta se elegirán antes de activarlo. |
| D-05 | Perímetro de red | Un enrutador/cortafuegos dedicado gobernará Internet, DHCP, VLANs, reglas y WireGuard. Kubernetes no será un componente de red crítico. | El Sagemcom se mantiene temporalmente hasta validar ISP, ONT, modo puente/DMZ y telefonía. No se cambia la red actual sin plan de reversión. |
| D-06 | Gestión de secretos | SOPS + age cifra secretos en Git; Argo CD los procesa mediante una integración de configuración aprobada y endurecida. | La clave privada age nunca entra en Git. Tendrá dos copias cifradas fuera del clúster y un procedimiento de restauración probado. Se revisará para External Secrets cuando exista un gestor externo justificado. |
| D-07 | Flujo inicial de servicios | Host y copias → Kubernetes → GitOps → observabilidad → seguridad/TLS → DNS y VPN → Home Assistant/MQTT. | No se incorporan dispositivos domésticos hasta que las copias y la restauración estén verificadas. |
| D-08 | DNS | AdGuard Home es la opción prevista para filtrado y DNS local, desplegado solo cuando exista un mecanismo alternativo de recuperación independiente del clúster. | El DNS del router/ISP permanece como ruta de recuperación hasta comprobar fallos y restauración. |
| D-09 | IoT, NVR y aceleración | Fuera de alcance inicial. | Se decidirán cámaras, días de retención, NVR, coordinadores Zigbee/Z-Wave/Thread y aceleradores tras medir necesidades reales. |

### Información que aún debe verificarse, no decidirse por suposición

- La configuración y restricciones reales del ISP, la ONT y el Sagemcom:
  modo puente/DMZ, VLAN WAN, telefonía, velocidad y configuración recuperable.
- Espacio, ruido, consumo eléctrico, presupuesto final y disponibilidad de
  cableado Ethernet para torre, NAS, conmutador y futuros puntos de acceso.
- Modelos concretos de torre, enrutador/cortafuegos, NAS, discos y SAI, tras
  comparar disponibilidad, garantía, compatibilidad y consumo.
- Proveedor de copia externa, región, coste, cifrado, frecuencia y retención.
- Carga real futura: número de servicios, cámaras, resolución, FPS y días de
  retención; solo entonces se decidirá ampliación de discos, GPU/Coral o más
  nodos.
