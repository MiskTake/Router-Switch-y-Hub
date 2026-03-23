# 🌐 Redes para Developers

> Conceptos esenciales de networking explicados desde la perspectiva del desarrollo de software.

---

## 📦 Dispositivos de Red

### 🔀 Router
Conecta **diferentes redes entre sí** (por ejemplo, tu red doméstica con internet).

- Asigna direcciones IP
- Dirige el tráfico entre redes
- Toma decisiones inteligentes sobre a dónde enviar los datos

### 🔌 Switch
Conecta **dispositivos dentro de la misma red local (LAN)**.

- Envía los datos **solo al dispositivo destino específico**
- Usa direcciones MAC para identificar destinatarios
- Eficiente y seguro

### 📡 Hub *(obsoleto)*
También conecta dispositivos en una red local, pero de forma "tonta".

- Envía los datos **a todos los dispositivos conectados**, sin importar el destinatario
- Hoy está prácticamente reemplazado por el switch

---

## ❓ Preguntas Clave

### ¿Qué hace el router con los paquetes de datos?

Al hacer una petición, los datos viajan en **paquetes con una IP de destino**. El router:

1. Lee la IP de destino
2. Consulta su tabla de rutas
3. Decide si el paquete va a la red local o hacia internet

> **Si es externo:** lo pasa al siguiente router en la cadena hasta llegar al servidor. Además realiza **NAT**: traduce tu IP privada a la IP pública.

---

### ¿Cómo decide un switch a dónde enviar la información?

Cada dispositivo tiene un número de identificación único llamado **dirección MAC**.

- La primera vez que un dispositivo se conecta, el switch lo registra
- Cuando alguien le envía datos, el switch los dirige **solo a esa dirección MAC**, sin molestar a nadie más

---

### ¿Por qué el hub es obsoleto?

Cada vez que recibe datos, hace un **broadcast**: los reenvía por todos sus puertos sin importar el destino. Esto causa:

| Problema | Descripción |
|---|---|
| **Colisiones** | Si dos dispositivos transmiten al mismo tiempo, los paquetes chocan y se corrompen |
| **Seguridad cero** | Cualquier equipo conectado puede escuchar el tráfico de todos los demás |

El switch resuelve ambos problemas. Por eso reemplazó al hub completamente.

---

## 🗂️ Glosario

| Término | Definición |
|---|---|
| **IP** | Dirección de tu dispositivo en la red. Como el domicilio de tu casa. Puede cambiar. |
| **MAC** | Número de serie del hardware de red de tu dispositivo. Como el DNI. No cambia. |
| **Broadcast** | Mensaje enviado a todos los dispositivos de la red a la vez, sin importar el destinatario. |
| **API** | Ventana que expone un servicio para que otras apps puedan usarlo. |

---

## 💻 Networking aplicado al desarrollo

### ¿Qué rol cumple el router cuando hacés un `fetch`?

```js
fetch("https://api.miapp.com")
```

Tu paquete llega al router → detecta que la IP destino **no es local** → lo manda hacia internet → varios routers lo van pasando hasta llegar al servidor.

---

### ¿Por qué un switch importa en un backend o data center?

Tu base de datos y tu caché se comunican constantemente. Ese tráfico **pasa por el switch**. Si falla, esos servicios no pueden hablar entre sí, aunque tu código esté perfecto.

---

### ¿Qué errores de red pueden romper tu app aunque tu código sea correcto?

| Error | Causa de red |
|---|---|
| `timeout` | Un router descartó el paquete en el camino |
| `ERR_NAME_NOT_RESOLVED` | El DNS no convirtió el dominio en IP |
| `Firewall` | El puerto está bloqueado, la conexión nunca se abre |
| Latencia rara | Los paquetes tardan demasiado entre saltos |

---

## 🚨 Escenario: *"Tu app está en producción pero los usuarios no pueden acceder"*

### ¿Podría ser problema de router?
**Sí.** El router puede tener una ruta caída, un firewall bloqueando el puerto, o el DNS sin responder. Los paquetes de los usuarios simplemente **no llegan al servidor**.

### ¿Podría ser problema de switch?
**Sí.** Si el switch interno falla, tu API no puede hablar con la base de datos ni con otros servicios. El servidor "existe" y responde, pero internamente está **incomunicado**.

### ¿Cómo distinguir si es problema de red o de código?

```bash
# ¿El servidor responde?
ping api.miapp.com

# ¿El puerto está abierto?
telnet api.miapp.com 443

# ¿El DNS resuelve?
nslookup api.miapp.com

# ¿Dónde se cae el paquete?
traceroute api.miapp.com
```

- Si `ping` falla → problema de red o DNS
- Si `ping` pasa pero el endpoint devuelve error → problema de código o configuración del servidor

---

## 🏙️ Analogía

# 🐄 Router vs Switch vs Hub (Explicado con Vacas)

