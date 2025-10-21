## What are Certificate Transparency Logs?

- `Certificate Transparency` (`CT`) logs are public
- الCT Logs زي دفتر رسمي بيسجل كل شهادات الأمان (SSL/TLS Certificates)
## Searching CT Logs

There are two popular options for searching CT logs:

| Tool                                | Key Features                                                                                                     | Use Cases                                                                                                 | Pros                                              | Cons                                         |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | ------------------------------------------------- | -------------------------------------------- |
| [crt.sh](https://crt.sh/)           | User-friendly web interface, simple search by domain, displays certificate details, SAN entries.                 | Quick and easy searches, identifying subdomains, checking certificate issuance history.                   | Free, easy to use, no registration required.      | Limited filtering and analysis options.      |
| [Censys](https://search.censys.io/) | Powerful search engine for internet-connected devices, advanced filtering by domain, IP, certificate attributes. | In-depth analysis of certificates, identifying misconfigurations, finding related certificates and hosts. | Extensive data and filtering options, API access. | Requires registration (free tier available). |
```shell-session
0xTroj6n@htb[/htb]$ curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
 
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com`