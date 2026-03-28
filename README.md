# 🌐 Fundamentos de Redes — Actividad Grupal

Investigación sobre los componentes básicos de red (router, switch y hub) y su relevancia directa para el trabajo de un desarrollador.

> *"Como desarrolladores, no trabajamos en el vacío. Nuestro código vive en una red: servidores, APIs, bases de datos, internet… Si no entendemos cómo viaja la información, estamos programando a ciegas."*

---

## 🎯 Objetivo

- Comprender qué son y cómo funcionan router, switch y hub
- Entender por qué estos dispositivos impactan directamente en el desarrollo
- Diagnosticar problemas de red como un developer profesional

---

## 1. ¿Qué es cada dispositivo?

- **Router:** Conecta tu red local con otras redes (como internet). Lee la IP de destino de cada paquete y decide por qué camino mandarlo.
- **Switch:** Conecta dispositivos dentro de la misma red local. Usa direcciones MAC para enviar cada paquete solo al destinatario correcto.
- **Hub:** Igual que el switch, pero sin inteligencia: reenvía cada paquete a todos los dispositivos conectados. Hoy está obsoleto.

| Concepto | Definición simple | Nivel técnico breve | Ejemplo real |
|---|---|---|---|
| **Router** | Conecta tu red con internet. Decide el camino de los datos. | Capa 3 del OSI. Enruta paquetes entre redes usando IPs. | El router de tu casa conecta tu red doméstica con la de tu ISP. |
| **Switch** | Conecta dispositivos en la misma red. Envía datos solo al destinatario. | Capa 2 del OSI. Usa tablas MAC para reenviar tramas al puerto correcto. | En un data center conecta servidores entre sí. |
| **Hub** | Conecta dispositivos pero manda todo a todos. | Capa 1 del OSI. Broadcast sin inteligencia ni filtrado. | Obsoleto. Era común en redes domésticas de los años 90. |

---

## 2. ¿Cómo funciona realmente?

### Router
Lee la IP de destino y consulta su tabla de rutas para decidir el camino. Usa NAT para traducir IPs privadas a pública al salir a internet.

### Switch
Mantiene una tabla MAC que asocia cada dirección con un puerto físico. Envía el paquete solo al puerto correcto. Si no conoce la MAC, hace broadcast una sola vez y luego aprende.

### Hub (obsoleto)
Reenvía todo a todos los puertos. Genera colisiones, divide el ancho de banda y expone el tráfico a todos los dispositivos. Sin ninguna inteligencia de enrutamiento.

---

## 3. Conexión directa con desarrollo

### Router y tu `fetch("https://api.miapp.com")`
Cuando tu código ejecuta esa petición, el paquete sale de tu máquina, pasa por el router (gateway) y viaja por internet saltando entre routers hasta llegar al servidor. Si el router falla, tu fetch lanza error de red aunque tu código esté perfecto.

### Switch en un data center
Conecta tu servidor de app con la base de datos y el caché. Un switch saturado genera latencia interna aunque el hardware sea bueno.

### Problemas de red que afectan tu app sin ser tu código

| Problema | Síntoma en tu app |
|---|---|
| Router caído | Timeout, no llega al servidor |
| Switch saturado | Latencia alta en queries a la base de datos |
| Pérdida de paquetes | Errores aleatorios e intermitentes |
| Firewall bloqueando puerto | Conexión rechazada al servicio externo |

---

## 4. Caso práctico — app en producción sin acceso

- **¿Router?** Sí. Si el router del data center falla, ningún usuario llega al servidor aunque la app esté corriendo.
- **¿Switch?** Sí, pero más raro. Si falla, los servicios internos (app, base de datos) no se comunican entre sí.

### ¿Red o código?
- Haz **ping** al servidor: si no responde, es red.
- Usa **traceroute**: muestra en qué salto se pierde la conexión.
- Revisa los **logs**: si el servidor ni recibe la petición, es red. Si la recibe y falla, es código.

---

## 5. Analogías

| Dispositivo | Analogía |
|---|---|
| **Router** | Oficina de correos: recibe el paquete, lee el destino y lo manda al camino correcto. |
| **Switch** | Recepcionista: sabe exactamente a quién entregar cada mensaje sin molestar a nadie más. |
| **Hub** | Persona que grita el mensaje en voz alta para toda la oficina sin importar a quién iba. |

---

## 6. Bonus — Load Balancer y Cloud

**Load Balancer:** Opera sobre el router/switch como punto de entrada único que distribuye peticiones entre varios servidores. El paquete entra por el router, pasa por el switch interno y antes de llegar a los servidores el load balancer decide a cuál mandarlo.

| Dispositivo físico | Equivalente en cloud |
|---|---|
| Router | Internet Gateway + Route Tables (AWS VPC) |
| Switch | Red interna del VPC entre instancias |
| Hub | No tiene equivalente — obsoleto incluso en cloud |
| Load balancer | ELB (AWS), Azure Load Balancer, GCP Load Balancing |

---

## 7. Glosario de términos

| Término | ¿Qué es? (en simple) | Ejemplo de uso |
|---|---|---|
| **IP** | Dirección de un dispositivo en una red. Como el número de casa, pero para computadores. | Tu laptop tiene IP `192.168.1.5` en tu red local. Un servidor tiene IP pública como `142.250.80.46`. |
| **MAC** | Identificador único de la tarjeta de red de un dispositivo. No cambia aunque cambies de red. | El switch usa la MAC para saber exactamente a qué computador entregar un paquete. |
| **Paquete** | Trozo pequeño de información. Los datos se dividen en paquetes para viajar por la red. | Cuando haces un `fetch`, tu petición se divide en paquetes que viajan y se reensamblan al llegar. |
| **API** | Puerta de entrada que un servidor ofrece para que otras apps le hagan peticiones y obtengan datos. | `fetch("https://api.miapp.com/users")` le pide a la API la lista de usuarios. |
| **Gateway** | Puerta de salida de una red. Todo el tráfico hacia afuera pasa por aquí. Generalmente es el router. | Cuando tu app llama a una API externa, el paquete va primero al gateway antes de salir a internet. |
| **Broadcast** | Enviar un mensaje a todos los dispositivos de la red al mismo tiempo, sin destinatario específico. | El hub hace broadcast de cada paquete. En una red con 20 dispositivos, todos reciben todo el tráfico. |
| **Latencia** | Tiempo que tarda un paquete en ir de un punto a otro y volver. Se mide en milisegundos (ms). | Si tu app tarda en responder aunque el código esté bien, puede ser latencia entre servidor y base de datos. |
| **NAT** | Permite que varios dispositivos con IPs privadas compartan una sola IP pública para salir a internet. | Tu laptop tiene IP `192.168.1.5` (privada). El router la traduce a su IP pública `181.43.10.5`. |
| **Firewall** | Sistema que filtra el tráfico de red según reglas. Bloquea conexiones no autorizadas. | Si tu app no puede conectarse a un servicio externo, puede ser que el firewall esté bloqueando el puerto. |
| **Puerto** | Número que identifica un servicio dentro de un servidor. La IP lleva al servidor, el puerto al servicio. | Puerto `443` = HTTPS. Puerto `3306` = MySQL. Puerto `5432` = PostgreSQL. |
| **DNS** | Sistema que traduce nombres de dominio a IPs. Sin él tendrías que memorizar números. | Escribes `google.com` → DNS lo convierte en `142.250.80.46` → tu navegador se conecta. |
| **VPC** | Red privada virtual dentro de un proveedor cloud. Como tu propia red local, pero en la nube. | En AWS creas un VPC donde viven tu servidor, base de datos y load balancer, todos aislados. |

---

## 💡 TL;DR

| Concepto | En simple |
|---|---|
| **Router** | Conecta redes distintas usando IPs — puente entre tu app e internet |
| **Switch** | Conecta dispositivos en la misma red usando MACs — clave en data centers |
| **Hub** | Igual que el switch pero manda todo a todos — obsoleto |
| **Load Balancer** | Distribuye peticiones entre servidores — opera sobre router y switch |
| **En cloud** | Router = Internet Gateway, Switch = VPC interno, LB = ELB/Azure LB |

---

> *"Un developer que entiende redes no solo programa… entiende por qué las cosas fallan, escala mejor sus soluciones y se vuelve mucho más profesional."*
