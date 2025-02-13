# **DNS (Domain Name System) в деталях** 🌍🚀

## **1. Что такое DNS?**
**DNS (Domain Name System)** — это система, которая переводит **доменные имена** (например, `google.com`) в **IP-адреса** (например, `142.250.74.238`), необходимые для установления соединения между клиентом и сервером.

🔹 **Зачем нужен DNS?**  
Вместо того, чтобы запоминать IP-адреса серверов, пользователи вводят удобные **человеческие** имена, а DNS сам выполняет перевод (резолвинг).

---

## **2. Как работает DNS-запрос?** 🔄
Когда вы вводите `google.com` в браузере, DNS проходит **4 этапа поиска**:

### **1. Проверка локального кэша**
Браузер и операционная система сначала проверяют, нет ли уже кешированного IP-адреса для `google.com`.

### **2. Запрос к рекурсивному DNS-резолверу**
Если IP не найден в кэше, запрос отправляется на **рекурсивный DNS-сервер** (обычно от провайдера, Google DNS `8.8.8.8`, Cloudflare `1.1.1.1`).

### **3. Иерархический поиск по DNS-серверам**
DNS-сервер выполняет последовательные запросы:
1. **Root-серверы** (`.`) → Направляют запрос на сервер `.com`.
2. **TLD-серверы** (`.com`) → Направляют запрос на сервер `google.com`.
3. **Authoritative-сервер `google.com`** → Отдает конечный IP-адрес.

### **4. Возвращение IP-адреса**
Рекурсивный резолвер получает IP, кеширует его и передает браузеру, который затем устанавливает соединение с сервером.

---

## **3. Виды DNS-запросов** 🕵️‍♂️
- **Рекурсивный** – сервер делает запросы дальше по цепочке, пока не найдет IP.
- **Итеративный** – сервер просто отвечает ближайшими возможными данными, но не делает запрос дальше.

---

## **4. Основные типы DNS-записей** 📌
DNS хранит не только IP-адреса, но и другие важные записи.

| **Тип**  | **Описание** | **Пример** |
|----------|-------------|------------|
| `A` | IPv4-адрес домена | `google.com → 142.250.74.238` |
| `AAAA` | IPv6-адрес домена | `google.com → 2607:f8b0::200e` |
| `CNAME` | Алиас (перенаправление) | `www.example.com → example.com` |
| `MX` | Почтовые серверы | `mail.example.com → 10.20.30.40` |
| `TXT` | Текстовые данные (SPF, DKIM, проверка домена) | `example.com → "v=spf1 include:_spf.google.com ~all"` |
| `NS` | Указывает на авторитетные DNS-серверы | `example.com → ns1.dnsprovider.com` |
| `SRV` | Определяет службы (например, SIP, XMPP) | `_sip._tcp.example.com` |
| `PTR` | Обратная запись (IP → Домен) | `40.30.20.10 → mail.example.com` |

---

## **5. DNS-кэширование и TTL (Time To Live)**
DNS-записи не запрашиваются каждый раз заново — они кешируются на разных уровнях:

- **Браузер** (Chrome, Firefox) — хранит кэш **на несколько минут**.
- **ОС (Windows, macOS, Linux)** — хранит кэш **до нескольких часов**.
- **Промежуточные DNS-серверы** (провайдеры) — кешируют **по TTL**.

### **Как проверить DNS-кеш?**
```sh
# Windows
ipconfig /displaydns

# macOS
sudo killall -HUP mDNSResponder && sudo dscacheutil -flushcache

# Linux
systemd-resolve --statistics
```

### **Как проверить TTL домена?**
```sh
nslookup -type=a google.com
```
ИЛИ
```sh
dig google.com +noall +answer
```
👉 **TTL = 300** означает, что запись будет храниться в кэше 5 минут.

---

## **6. DNS-проблемы и их решения**
### **1. "DNS_PROBE_FINISHED_NXDOMAIN"**
❌ Ошибка: Доменное имя не найдено.  
✅ **Решение:**  
- Проверить правильность ввода домена.
- Сбросить DNS-кэш (`ipconfig /flushdns`).
- Использовать **другой DNS-сервер** (`8.8.8.8`, `1.1.1.1`).

---

### **2. DNS-запись не обновляется**
❌ Ошибка: Изменения DNS не применились.  
✅ **Решение:**  
- Подождать **до 48 часов** (из-за кэширования).
- Принудительно очистить DNS-кэш (`flushdns`).
- Использовать `nslookup` или `dig` для проверки новых значений.

---

### **3. Медленное разрешение домена**
❌ Ошибка: Долгий ответ от DNS.  
✅ **Решение:**  
- Использовать **быстрые DNS-серверы** (Google, Cloudflare).
- Проверить `TTL` и уменьшить его перед обновлением записей.
- Проверить, нет ли проблем с **ISP (провайдером)**.

---

## **7. Быстрые публичные DNS-серверы (замена провайдерским)**
| **Провайдер** | **IPv4** | **IPv6** | **Фишки** |
|--------------|---------|---------|------------|
| **Google** | `8.8.8.8`, `8.8.4.4` | `2001:4860:4860::8888` | Быстро, стабильно |
| **Cloudflare** | `1.1.1.1`, `1.0.0.1` | `2606:4700:4700::1111` | Самый быстрый (1ms) |
| **OpenDNS** | `208.67.222.222` | `2620:0:ccc::2` | Родительский контроль |
| **Quad9** | `9.9.9.9` | `2620:fe::fe` | Безопасность, защита от фишинга |

📌 **Как сменить DNS в системе?**
1. **Windows**: Панель управления → Сетевые подключения → Свойства IPv4.
2. **MacOS**: Системные настройки → Сеть → Дополнительно → DNS.
3. **Linux**: Изменить `/etc/resolv.conf`.

---

## **8. DNS over HTTPS (DoH) и DNS over TLS (DoT)**
🔒 Обычный DNS-запрос передается **без шифрования**, что позволяет провайдерам его отслеживать.

✅ **Решение**:
- **DNS over HTTPS (DoH)** – шифрует DNS-запросы через HTTPS.
- **DNS over TLS (DoT)** – шифрует на уровне TCP.

📌 **Как включить DoH?**
- **Google Chrome**: Включить "Secure DNS" в настройках.
- **Firefox**: Включить DoH в настройках → **Cloudflare DNS**.
- **Android**: Использовать **1.1.1.1 App** от Cloudflare.

---

## **9. DNS vs. CDN (Cloudflare, Akamai, AWS Route 53)**
Многие компании используют **CDN (Content Delivery Network)** для ускорения загрузки сайтов.

✅ **CDN используют Anycast DNS**, что позволяет запросам направляться к **ближайшему серверу** вместо одного фиксированного IP.

**Пример с `dig`:**
```sh
dig google.com
```
👉 Google выдаст **разные IP-адреса в зависимости от вашего местоположения**.

---

## **10. Вывод**
✅ **DNS — это "адресная книга интернета", переводящая домены в IP-адреса.**  
✅ **Event Loop в браузере может зависеть от DNS-запросов, если кэш не используется.**  
✅ **Cloudflare `1.1.1.1` и Google `8.8.8.8` — лучшие альтернативы DNS-провайдеров.**  
✅ **Используйте DNS over HTTPS (DoH) для защиты от перехвата запросов.**  


### English version 



# **DNS (Domain Name System) in Detail** 🌍🚀  

## **1. What is DNS?**  
**DNS (Domain Name System)** is a system that translates **domain names** (e.g., `google.com`) into **IP addresses** (e.g., `142.250.74.238`) needed to establish a connection between a client and a server.  

🔹 **Why is DNS needed?**  
Instead of memorizing IP addresses, users enter easy-to-remember **human-friendly** names, and DNS automatically performs the translation (resolution).  

---  

## **2. How Does a DNS Query Work?** 🔄  
When you enter `google.com` in your browser, DNS goes through **four lookup stages**:  

### **1. Checking Local Cache**  
The browser and operating system first check if the IP address for `google.com` is already cached.  

### **2. Querying the Recursive DNS Resolver**  
If the IP is not found in the cache, the request is sent to a **recursive DNS server** (usually provided by the ISP, or services like Google DNS `8.8.8.8`, Cloudflare `1.1.1.1`).  

### **3. Hierarchical DNS Lookup**  
The DNS server performs sequential queries:  
1. **Root servers** (`.`) → Directs the query to the `.com` server.  
2. **TLD servers** (`.com`) → Directs the query to `google.com`'s name server.  
3. **Authoritative server for `google.com`** → Returns the final IP address.  

### **4. Returning the IP Address**  
The recursive resolver receives the IP, caches it, and sends it to the browser, which then establishes a connection with the server.  

---  

## **3. Types of DNS Queries** 🕵️‍♂️  
- **Recursive** – The server continues querying until it finds the IP.  
- **Iterative** – The server returns the closest available data but does not query further.  

---  

## **4. Main Types of DNS Records** 📌  
DNS stores not only IP addresses but also other important records.  

| **Type**  | **Description** | **Example** |  
|----------|-------------|------------|  
| `A` | IPv4 address of a domain | `google.com → 142.250.74.238` |  
| `AAAA` | IPv6 address of a domain | `google.com → 2607:f8b0::200e` |  
| `CNAME` | Alias (redirection) | `www.example.com → example.com` |  
| `MX` | Mail servers | `mail.example.com → 10.20.30.40` |  
| `TXT` | Text data (SPF, DKIM, domain verification) | `example.com → "v=spf1 include:_spf.google.com ~all"` |  
| `NS` | Specifies authoritative DNS servers | `example.com → ns1.dnsprovider.com` |  
| `SRV` | Defines services (e.g., SIP, XMPP) | `_sip._tcp.example.com` |  
| `PTR` | Reverse lookup (IP → Domain) | `40.30.20.10 → mail.example.com` |  

---  

## **5. DNS Caching and TTL (Time To Live)**  
DNS records are not queried every time; they are cached at different levels:  

- **Browser** (Chrome, Firefox) – stores cache **for a few minutes**.  
- **OS (Windows, macOS, Linux)** – stores cache **for several hours**.  
- **Intermediate DNS servers** (ISPs) – cache records **according to TTL**.  

### **How to Check DNS Cache?**  
```sh
# Windows
ipconfig /displaydns

# macOS
sudo killall -HUP mDNSResponder && sudo dscacheutil -flushcache

# Linux
systemd-resolve --statistics
```  

### **How to Check a Domain’s TTL?**  
```sh
nslookup -type=a google.com
```
OR  
```sh
dig google.com +noall +answer
```  
👉 **TTL = 300** means the record will be cached for 5 minutes.  

---  

## **6. DNS Issues and Their Solutions**  

### **1. "DNS_PROBE_FINISHED_NXDOMAIN"**  
❌ **Error:** Domain name not found.  
✅ **Solution:**  
- Check if the domain name is entered correctly.  
- Flush the DNS cache (`ipconfig /flushdns`).  
- Use a **different DNS server** (`8.8.8.8`, `1.1.1.1`).  

---  

### **2. DNS Record Not Updating**  
❌ **Error:** DNS changes are not taking effect.  
✅ **Solution:**  
- Wait **up to 48 hours** (due to caching).  
- Force clear the DNS cache (`flushdns`).  
- Use `nslookup` or `dig` to check new values.  

---  

### **3. Slow Domain Resolution**  
❌ **Error:** Slow DNS response time.  
✅ **Solution:**  
- Use **faster DNS servers** (Google, Cloudflare).  
- Check `TTL` and lower it before updating records.  
- Check for **ISP (provider) issues**.  

---  

## **7. Fast Public DNS Servers (Alternatives to ISP DNS)**  

| **Provider** | **IPv4** | **IPv6** | **Features** |  
|--------------|---------|---------|------------|  
| **Google** | `8.8.8.8`, `8.8.4.4` | `2001:4860:4860::8888` | Fast, stable |  
| **Cloudflare** | `1.1.1.1`, `1.0.0.1` | `2606:4700:4700::1111` | Fastest (1ms response) |  
| **OpenDNS** | `208.67.222.222` | `2620:0:ccc::2` | Parental control |  
| **Quad9** | `9.9.9.9` | `2620:fe::fe` | Security, phishing protection |  

📌 **How to Change DNS in the System?**  
1. **Windows**: Control Panel → Network Connections → IPv4 Properties.  
2. **macOS**: System Preferences → Network → Advanced → DNS.  
3. **Linux**: Edit `/etc/resolv.conf`.  

---  

## **8. DNS over HTTPS (DoH) and DNS over TLS (DoT)**  
🔒 Regular DNS queries are **unencrypted**, allowing ISPs to track them.  

✅ **Solution**:  
- **DNS over HTTPS (DoH)** – Encrypts DNS queries via HTTPS.  
- **DNS over TLS (DoT)** – Encrypts DNS at the TCP level.  

📌 **How to Enable DoH?**  
- **Google Chrome**: Enable "Secure DNS" in settings.  
- **Firefox**: Enable DoH in settings → **Cloudflare DNS**.  
- **Android**: Use **1.1.1.1 App** by Cloudflare.  

---  

## **9. DNS vs. CDN (Cloudflare, Akamai, AWS Route 53)**  
Many companies use **CDNs (Content Delivery Networks)** to speed up website loading.  

✅ **CDNs use Anycast DNS**, allowing queries to be directed to **the nearest server** instead of a single fixed IP.  

**Example with `dig`:**  
```sh
dig google.com
```
👉 Google will return **different IP addresses depending on your location**.  

---  

## **10. Conclusion**  
✅ **DNS is the "address book of the internet," translating domains into IP addresses.**  
✅ **The browser's Event Loop can be affected by DNS queries if caching is not used.**  
✅ **Cloudflare `1.1.1.1` and Google `8.8.8.8` are the best ISP DNS alternatives.**  
✅ **Use DNS over HTTPS (DoH) to protect against query interception.**  
