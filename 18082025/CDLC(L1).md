## 1. Before (Original Version)

* Returned only a short verdict string such as *“Safe”*, *“Suspicious”*, or *“Invalid URL”*.
* Relied on very basic pattern matching with a regular expression.
* Checked only for a few risky keywords in the URL text.
* Did not normalize input (for example, missing schemes like `http://` were not handled).
* No detailed breakdown of the URL (scheme, host, path, query, etc.).
* No attempt to resolve the domain to an IP address.
* No integration with free public APIs for enrichment.
* Did not generate a risk score or severity levels.
* Very limited reasons or explanations were provided to the user.
* Minimal error handling; invalid input simply returned “Invalid URL format.”

---

## 2. After (New Version)

* Produces a structured report with multiple fields (URL parts, resolved IP, geolocation, reasons, score, timestamp).
* Always returns a result, even for invalid or random input, instead of failing silently.
* Normalizes input and extracts candidate URLs from any text, automatically adding `http://` if missing.
* Applies a much broader set of heuristics, including:

  * HTTP vs HTTPS detection
  * Suspicious or uncommon top-level domains
  * Punycode detection (possible homograph attacks)
  * IP address literals used as hostnames
  * Excessive subdomains
  * Expanded risky keyword detection (account, billing, refund, wallet, etc.)
* Resolves hostname to an IP address (if possible).
* Enriches results with free IP reputation data (country, city, ISP, ASN) via ip-api.com.
* Introduces a numerical risk score and clear severity levels: *Likely Safe, Potentially Suspicious, High Risk, Very High Risk*.
* Provides a list of reasons supporting the verdict for transparency.
* Adds metadata such as timestamp and analysis length for traceability.
* Implements network timeouts and safe exception handling to prevent failures.
* Overall, the function has shifted from a simple yes/no checker to a comprehensive phishing risk analyzer.

---

## 3. Safe / Likely Safe Examples

These are normal, well-known, properly formatted URLs:

* `https://www.google.com`
* `https://www.wikipedia.org`
* `https://www.mit.edu`
* `https://openai.com/research`
* `https://www.gov.uk`
* `https://developer.mozilla.org/en-US/`
* `https://www.nasa.gov/missions`
* `https://www.python.org/downloads/`
* `https://www.un.org/en/about-us`

---

## 4. Unsafe / Suspicious Examples

These are crafted to trigger phishing heuristics:

Suspicious Keywords

* `http://secure-login.example.com`
* `http://paypal-verification.com/reset`
* `http://faceb00k-login.net/account`
* `http://update-appleid.com`

Suspicious TLDs

* `http://banking-support.xyz`
* `http://secure-login.zip`
* `http://verify-account.work`

IP Literal Domains

* `http://192.168.1.100/login`
* `http://45.67.89.123/paypal`

Punycode / Homograph

* `http://xn--pple-43d.com` (looks like “apple.com”)
* `http://xn--paypl-4ve.com` (looks like “paypal.com”)

Too Many Subdomains

* `http://login.secure.verify.update.paypal.com.fake-site.ru`

---
