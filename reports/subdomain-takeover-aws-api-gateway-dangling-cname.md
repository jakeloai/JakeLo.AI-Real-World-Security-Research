# [REDACTED] — Subdomain Takeover via Dangling CNAME (AWS API Gateway)

**Vulnerability Type:** Subdomain Takeover
**Severity:** High
**Environment:** DNS / Cloud Infrastructure (AWS API Gateway)
**Status:** Reported

---

## 🔍 Executive Summary

A subdomain takeover vulnerability was identified due to a dangling CNAME record pointing to a decommissioned AWS API Gateway endpoint.

Because the referenced cloud resource no longer exists, an attacker can re-register the same API Gateway identifier and serve arbitrary content under the affected subdomain.

This enables full control over the subdomain, allowing phishing, malware distribution, or brand impersonation under a trusted domain.

---

## 🎯 Attack Scenario

An attacker identifies a subdomain configured with a CNAME record pointing to an AWS API Gateway endpoint.

The endpoint returns a “Not Found” response, indicating that the resource has been deleted but the DNS record remains active.

The attacker then:

1. Creates a new AWS API Gateway instance using the same identifier
2. Deploys a malicious application (e.g., phishing page)
3. The subdomain automatically resolves to the attacker-controlled resource

As a result, users visiting the legitimate subdomain are served attacker-controlled content.

---

## 🧪 Technical Details

### Root Cause

* Dangling CNAME record pointing to a deleted cloud resource
* Lack of DNS hygiene and resource lifecycle management

---

### Verification

Querying the DNS record confirms the CNAME configuration:

```bash id="p1"
dig [REDACTED_SUBDOMAIN] CNAME +short
```

**Observed Result:**

```id="p2"
[REDACTED].execute-api.[region].amazonaws.com.
```

---

Accessing the subdomain returns a response indicating a missing resource:

```bash id="p3"
curl -v https://[REDACTED_SUBDOMAIN]
```

**Observed Response:**

```id="p4"
HTTP/2 404
{"message":"Not Found"}
```

This confirms the backend resource is no longer active.

---

### Exploitation Feasibility

* AWS API Gateway identifiers can be re-registered
* No ownership validation tied to DNS record
* Attack complexity: Low

An attacker can reliably claim the resource and gain control of the subdomain.

---

## 💥 Impact Assessment

Successful exploitation allows:

* **Full control over the affected subdomain**
* Hosting of phishing pages under a trusted domain
* Distribution of malicious content
* Credential harvesting from unsuspecting users
* Brand reputation damage

If the subdomain is linked from primary domains or user workflows, the impact increases significantly.

---

## 🛠️ Remediation Recommendations

* Remove unused or stale DNS records immediately
* Ensure decommissioned cloud resources are reflected in DNS updates
* Implement regular DNS audits
* Monitor for orphaned infrastructure references
* Use automated asset inventory tracking

---

## ⚠️ Notes

* No malicious takeover was performed
* Exploitation path validated without claiming the resource
* All sensitive identifiers have been redacted

---

## 📌 Conclusion

Dangling DNS records represent a critical but often overlooked attack vector.

When combined with reusable cloud resource identifiers, they create a direct path for attackers to gain control over trusted subdomains.

Proper asset lifecycle management is essential to prevent this class of vulnerability.
