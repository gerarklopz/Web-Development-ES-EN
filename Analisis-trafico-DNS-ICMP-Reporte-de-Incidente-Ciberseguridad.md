# ES ver.🇪🇸
# **Análisis de Tráfico DNS e ICMP - Reporte de Incidente de Ciberseguridad**

## **1️⃣ Resumen del Problema**

Se detectó un incidente de ciberseguridad cuando varios clientes informaron que no podían acceder al sitio web `www.yummyrecipesforme.com`.

Al intentar cargar la página, los usuarios recibieron el error: **"destination port unreachable"**.

Para analizar la causa del problema, se utilizó la herramienta `tcpdump` para capturar el tráfico de red. Los resultados indicaron un problema en la resolución DNS del dominio.

---

## **2️⃣ Proceso de Investigación Paso a Paso**

### **Paso 1: Captura de tráfico con ****`tcpdump`**

Se ejecutó el siguiente comando para capturar tráfico DNS:

```bash
sudo tcpdump -i wlp1s0 port 53 -nn
```

**Resultados:**

- El cliente envió solicitudes DNS al servidor `80.58.61.254:53`.
- La respuesta fue `NXDOMAIN`, indicando que el dominio no pudo resolverse.

---

### **Paso 2: Comprobación de la existencia del dominio con ****`whois`**

Se verificó si el dominio seguía registrado:

```bash
whois yummyrecipesforme.com
```

**Resultados:**

- El dominio **sigue registrado** y pertenece a **Google LLC**.
- No ha caducado ni ha sido eliminado.

---

### **Paso 3: Consultar diferentes servidores DNS con ****`dig`**

Para confirmar si el problema era local o global, se consultaron diferentes servidores DNS.

📌 **Consulta a Cloudflare (1.1.1.1):**

```bash
dig www.yummyrecipesforme.com @1.1.1.1
```

**Resultado:** `NXDOMAIN` → Cloudflare tampoco pudo resolver el dominio.

📌 **Consulta a Google Domains (servidor oficial del dominio):**

```bash
dig A yummyrecipesforme.com @ns1.googledomains.com
```

**Resultado:**

- `NOERROR`, pero **sin registros A**.
- `WARNING: recursion requested but not available` → El servidor no responde con una IP válida.

---

### **Paso 4: Verificación de registros DNS**

📌 **Consulta de servidores de nombres (NS):**

```bash
dig NS yummyrecipesforme.com
```

**Resultado:**

- Se confirmaron los servidores DNS en Google Domains (`ns1.googledomains.com`, etc.).

📌 **Consulta de registros A (IPv4) y CNAME (alias):**

```bash
dig A yummyrecipesforme.com
dig CNAME yummyrecipesforme.com
```

**Resultado:**

- **No hay registros A ni CNAME**, lo que significa que **el dominio no tiene asignada una dirección IP.**

---

## **3️⃣ Conclusión Final**

📌 **No es un problema de red, ni un firewall, ni un ataque DDoS.**
📌 **El dominio está registrado, pero carece de configuración DNS válida.**
📌 **Google Domains no ha asignado una dirección IP al dominio.**

### **Posibles causas:**

1️⃣ **El administrador del dominio no configuró los registros DNS correctamente.**
2️⃣ **Google Domains podría haber suspendido el dominio temporalmente.**
3️⃣ **El dominio está en proceso de propagación DNS y los cambios aún no son visibles.**

---

## **4️⃣ Recomendaciones y Próximos Pasos**

🔹 **Contactar al administrador del dominio** para verificar la configuración DNS.
🔹 **Agregar registros A o CNAME en Google Domains** para asignar una dirección IP al dominio.
🔹 **Esperar hasta 48 horas** si se realizaron cambios recientes en la configuración DNS.

---

📌 **Este informe documenta un análisis de tráfico DNS e ICMP en un entorno real, aplicando metodologías de ciberseguridad para la detección y diagnóstico de fallos en la red.** 🚀


