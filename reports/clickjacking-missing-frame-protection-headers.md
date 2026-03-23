# [REDACTED] — Clickjacking via Missing Frame Protection Headers

**Vulnerability Type:** Clickjacking (UI Redressing)
**Severity:** Medium
**Environment:** Web Application
**Status:** Reported

---

## 🔍 Executive Summary

A clickjacking vulnerability was identified due to missing frame protection headers on a web application.

The application does not implement `X-Frame-Options` or `Content-Security-Policy` (`frame-ancestors`), allowing it to be embedded within an attacker-controlled iframe.

This enables attackers to overlay deceptive user interface elements and trick users into performing unintended actions, potentially leading to unauthorized operations or phishing attacks.

---

## 🎯 Attack Scenario

An attacker hosts a malicious webpage designed to appear legitimate.

The vulnerable application is embedded invisibly within an iframe, while fake interface elements (e.g., buttons or prompts) are placed on top.

When a user interacts with the visible elements, they are unknowingly interacting with the underlying legitimate application.

This can result in:

* Clicking sensitive actions (e.g., account setup, authorization flows)
* Submitting unintended requests
* Being redirected into attacker-controlled flows

---

## 🧪 Technical Details

### Root Cause

* Missing `X-Frame-Options` header
* Missing `Content-Security-Policy: frame-ancestors` directive

These protections are standard mechanisms to prevent unauthorized framing of web applications.

---

### Verification

The application response headers were inspected:

```bash id="c1"
curl -I https://[REDACTED_TARGET]
```

**Observed Result:**

```id="c2"
(no X-Frame-Options header)
(no Content-Security-Policy frame-ancestors directive)
```

---

### Proof-of-Concept (PoC)

A simple clickjacking page was created to demonstrate exploitation:

```html id="c3"
<html>
<head><title>Clickjacking Demo</title></head>
<body>
  <button style="position:absolute; top:300px; left:150px; z-index:2;">
    Click Here
  </button>

  <iframe src="https://[REDACTED_TARGET]"
          width="1000" height="800"
          style="opacity:0.2; position:absolute; top:0; left:0; z-index:1;">
  </iframe>
</body>
</html>
```

When loaded in a browser, user interaction with the visible button triggers actions on the embedded application.

---

### Exploitation Feasibility

* No authentication required to load target page
* No frame restrictions in place
* Attack complexity: Low

The vulnerability can be exploited using basic HTML and does not require advanced tooling.

---

## 💥 Impact Assessment

Successful exploitation may lead to:

* **User interaction hijacking**
* Execution of unintended actions within the application
* Phishing via trusted interface embedding
* Potential abuse of onboarding or authorization flows

The severity depends on the sensitivity of actions exposed within the framed application.

---

## 🛠️ Remediation Recommendations

* Implement `X-Frame-Options: DENY` or `SAMEORIGIN`
* Add `Content-Security-Policy: frame-ancestors 'none'` or restricted domains
* Review sensitive user flows for clickjacking exposure
* Apply defense-in-depth measures for critical actions

---

## ⚠️ Notes

* No real user interaction was targeted or impacted
* Testing was conducted in a controlled environment
* Sensitive identifiers have been redacted

---

## 📌 Conclusion

Clickjacking remains a low-complexity but effective attack vector when standard browser protections are not enforced.

Proper implementation of frame protection headers is a simple yet critical step in preventing user interface manipulation attacks.
