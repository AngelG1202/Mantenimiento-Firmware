# Orquestación Zero-Downtime y Mantenimiento de Hardware Crítico (SSNF)

## Contexto y Objetivo del Proyecto
Los servidores de producción del entorno SSNF operaban con versiones de microcódigo severamente desactualizadas en sus interfaces de gestión (iLO) y placas base (BIOS). Esta obsolescencia tecnológica representaba un alto riesgo operativo y de ciberseguridad a nivel bare-metal. 

**Objetivo:** Ejecutar un mantenimiento preventivo y actualización profunda de firmware en un clúster de alta disponibilidad, garantizando **cero tiempo de inactividad (Zero-Downtime)** para los servicios corporativos virtualizados del cliente.

---

## Arquitectura y Stack Tecnológico
* **Plataforma de Hardware:** Clúster de 3x Servidores físicos HPE ProLiant DL380 Gen10.
* **Identificadores de Chasis (S/N):** 2M281902GG, 2M281902GJ, 2M281902GH.
* **Entorno de Virtualización:** Clúster VMware vSphere / ESXi con capacidades de migración en caliente.
* **Componentes Intervenidos:** HPE Integrated Lights-Out (iLO) y System ROM (BIOS).

---

## Metodología de Ejecución: Rolling Upgrade
Para evitar ventanas de mantenimiento disruptivas, se diseñó y ejecutó un procedimiento iterativo de evacuación y flasheo nodo por nodo (Ticket #2884):

### Fase 1: Evacuación de Cargas (Host Vacating)
* Se inició el proceso en el primer servidor aislando sus recursos de cómputo.
* Se ejecutó la migración controlada de todas las máquinas virtuales (VMs) alojadas en dicho servidor, distribuyendo sus cargas de manera balanceada hacia los otros 2 servidores activos del clúster.

### Fase 2: Actualización de Firmware OOB y BIOS
Con el hardware liberado de cargas productivas, se procedió a actualizar los subsistemas críticos:
* **Flasheo de HPE iLO:** Actualización de la versión heredada **v1.20** a la versión estable **v2.78**.
* **Flasheo de System ROM:** Actualización del BIOS de la versión **v1.36** a la versión **v2.78**.

### Fase 3: Validación POST y Restablecimiento (Failback)
* Se verificó el correcto arranque del hardware y la telemetría de los sensores desde la nueva consola iLO[cite: 32].
* Una vez estabilizado el host, se restablecieron (migraron de vuelta) sus máquinas virtuales correspondientes[cite: 32].
* Este ciclo (Evacuación -> Flasheo -> Restablecimiento) se repitió secuencialmente en los 3 servidores físicos, dejando la infraestructura operando de forma óptima sin afectar la red del cliente[cite: 32].

---

## Valor Agregado: Consultoría Técnica y Preventa
Como parte del alcance del servicio técnico y con una visión orientada a la arquitectura de soluciones, se realizó una auditoría del licenciamiento del entorno:
* **Hallazgo Crítico:** Se notificó formalmente al cliente que el **End of General Support (EOGS)** de su infraestructura VMware finaliza el **19 de octubre de 2027**.
* **Recomendación Estratégica:** Se planteó un roadmap tecnológico, recomendando presupuestar la actualización de las licencias de VMware o planificar una migración corporativa hacia **Proxmox VE** para optimizar costos de licenciamiento.

---

## Anexo Fotográfico (Evidencias de Ejecución)

1. `[Vista del cliente vSphere validando el estado operativo normal de las máquinas virtuales en el host esxi01.intendencia.local posterior a la actualización de firmware y el restablecimiento de las cargas de trabajo.]`<img width="887" height="494" alt="ESXI 01" src="https://github.com/user-attachments/assets/42a5856a-7ebd-44e2-8d23-7ea58fb24f72" />

2. `[Insertar Imagen 3 y 4 del Anexo: Interfaz de actualización de firmware / validación del sistema]`[cite: 34]
3. `[Insertar Imagen 5 y 6 del Anexo: Proceso de validación final en los nodos del clúster]`[cite: 34]
