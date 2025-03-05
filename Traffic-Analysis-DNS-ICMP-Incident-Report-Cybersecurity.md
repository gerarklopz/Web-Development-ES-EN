# EN ver.🇺🇸🇬🇧
# **DNS and ICMP Traffic Analysis - Cybersecurity Incident Report**

## **1️⃣ Problem Summary**

A cybersecurity incident was detected when several customers reported that they were unable to access the website `www.yummyrecipesforme.com`.

When attempting to load the page, users received the error: **"destination port unreachable"**.

To analyze the cause of the problem, the `tcpdump` tool was used to capture network traffic. The results indicated a problem in the DNS resolution of the domain.

---

## **2️⃣ Step-by-Step Investigation Process**

### **Step 1: Capturing traffic with `tcpdump`**

The following command was executed to capture DNS traffic:
```bash
sudo tcpdump -i wlp1s0 port 53 -nn
```
**Results:**
- The client sent DNS requests to the server `80.58.61.254:53`.
- The response was `NXDOMAIN`, indicating that the domain could not be resolved.

---

### **Step 2: Checking the existence of the domain with `whois`**

The domain was verified to be still registered:
```bash
whois yummyrecipesforme.com
```
**Results:**
- The domain **is still registered** and belongs to **Google LLC**.
- It has not expired or been deleted.

---

### **Step 3: Query different DNS servers with `dig`**

To confirm whether the problem was local or global, different DNS servers were queried.

📌 **Query Cloudflare (1.1.1.1):**
```bash
dig www.yummyrecipesforme.com @1.1.1.1
```
**Result:** `NXDOMAIN` → Cloudflare was also unable to resolve the domain.

📌 **Query Google Domains (official domain server):**
```bash
dig A yummyrecipesforme.com @ns1.googledomains.com
```
**Result:**
- `NOERROR`, but **no A records**.
- `WARNING: recursion requested but not available` → The server does not respond with a valid IP.

---

### **Step 4: DNS Record Verification**

📌 **Name Servers (NS) Lookup:**
```bash
dig NS yummyrecipesforme.com
```
**Result:**
- DNS servers confirmed for Google Domains (`ns1.googledomains.com`, etc.).

📌 **A (IPv4) and CNAME (alias) records lookup:**
```bash
dig A yummyrecipesforme.com
dig CNAME yummyrecipesforme.com
```
**Result:**
- **There are no A or CNAME records**, which means that **the domain is not assigned an IP address.**

---

## **3️⃣ Final Conclusion**

📌 **It's not a network problem, nor a firewall, nor a DDoS attack.**
📌 **The domain is registered, but lacks valid DNS configuration.**
📌 **Google Domains has not assigned an IP address to the domain.**

### **Possible causes:**
1️⃣ **The domain administrator did not configure the DNS records correctly.**
2️⃣ **Google Domains might have temporarily suspended the domain.**
3️⃣ **The domain is in the process of DNS propagation and changes are not yet visible.**

---

## **4️⃣ Recommendations and Next Steps**

🔹 **Contact the domain administrator** to verify the DNS settings.
🔹 **Add A or CNAME records in Google Domains** to assign an IP address to the domain.
🔹 **Wait up to 48 hours** if recent changes were made to the DNS settings.

---

📌 **This report documents an analysis of DNS and ICMP traffic in a real-world environment, applying cybersecurity methodologies for the detection and diagnosis of network failures.** 🚀
