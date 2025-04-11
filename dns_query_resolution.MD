
# 🧪 Real-Time DNS Delay Investigation Exercise

This hands-on exercise is designed to help you identify **DNS-related website delay** using real-world tools like `dig`, `nslookup`, `traceroute`, and `curl`.

---

## 🎯 Objective

Identify whether a slow-loading website is experiencing latency due to DNS resolution issues.

---

## 🔧 Tools Required

- Terminal with Linux/Mac or WSL (for Windows users)
- `dig`, `nslookup`, `traceroute`, `curl`, `time`
- A browser to cross-check results

---

## 🌐 Test Website

Choose a well-known website (example: `www.nytimes.com`, `www.reddit.com`, or one of your own apps).

Replace the website in the commands below as needed.

---

## 🧩 Step-by-Step Instructions

### Step 1: Measure Total Time for HTTP Request

```bash
time curl -o /dev/null -s -w "%{time_namelookup} DNS | %{time_connect} TCP | %{time_total} TOTAL\n" https://www.nytimes.com
```

> This tells you how long it took to resolve DNS, connect via TCP, and the total time taken.

### Step 2: Use `dig` to Check DNS Resolution Time

```bash
dig www.nytimes.com
```

Look for:
- `Query time:` → Should be low (e.g., < 100ms)
- `SERVER:` → Where is the DNS response coming from?

### Step 3: Use `traceroute` to DNS Server

Find your DNS IP:

```bash
cat /etc/resolv.conf
```

Then:

```bash
traceroute <DNS_IP>
```

Check how far (how many hops) and how long it takes to reach your DNS.

### Step 4: Compare with a Public DNS

Try querying using Google DNS:

```bash
dig @8.8.8.8 www.nytimes.com
```

And Cloudflare DNS:

```bash
dig @1.1.1.1 www.nytimes.com
```

Compare the `Query time:` results. Do public DNS servers respond faster?

### Step 5: Check if Site Loads Faster with Faster DNS

Temporarily override DNS resolution using `/etc/hosts` (Linux/macOS):

```bash
# Get IP
dig www.nytimes.com +short

# Add to /etc/hosts
sudo nano /etc/hosts

# Add:
<IP_FROM_DIG> www.nytimes.com
```

Now retry the `curl` command from Step 1 and compare timings.

---

## ✅ What to Observe

| Metric              | Good Value    | Suspicious Value    |
|---------------------|---------------|----------------------|
| DNS lookup time     | < 50 ms       | > 150 ms             |
| TCP connect time    | < 100 ms      | > 300 ms             |
| Total request time  | Varies        | Compare with others  |
| DNS hops            | < 5 hops      | > 8 hops             |
| DNS query server    | Near region   | Far country or slow  |

---

## 🧠 Bonus Questions

1. Does using a different DNS provider improve lookup speed?
2. Do you get different IPs when querying via different DNS servers?
3. Are those IPs located closer to you (use tools like `ipinfo.io`)?
4. What does traceroute to those IPs show?

---

## 📌 Cleanup

Don’t forget to remove your `/etc/hosts` entry if you added it for testing.

---

Happy debugging! 🧑‍💻🌐
