- ال.well-known ده زي صندوق بريد مركزي لأي موقع ويب
- وظيفته انه يخلي البرامج والخدمات الخارجيه عارفه مكان ثابت وموحود تقدر تلاقي فيه ملفات مهمه عن الموقع ده بدل ما كل خدمه تعمل فولدر مختلف وتتلغبط
- المجلد ده بيخزن **"البيانات الوصفية الهامة" (Critical Metadata)** للموقع، زي:
1. **ملفات الأمان:** (زي ملفات التحقق من شهادات SSL/TLS).
2. **ملفات الإعدادات:** معلومات خاصة بطريقة عمل خدمات معينة أو بروتوكولات الأمان.
3. **معلومات الخدمات:** تفاصيل عن الخدمات اللي بيقدمها الموقع ده

|URI Suffix|Description|Status|Reference|
|---|---|---|---|
|`security.txt`|Contains contact information for security researchers to report vulnerabilities.|Permanent|RFC 9116|
|`/.well-known/change-password`|Provides a standard URL for directing users to a password change page.|Provisional|https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri|
|`openid-configuration`|Defines configuration details for OpenID Connect, an identity layer on top of the OAuth 2.0 protocol.|Permanent|http://openid.net/specs/openid-connect-discovery-1_0.html|
|`assetlinks.json`|Used for verifying ownership of digital assets (e.g., apps) associated with a domain.|Permanent|https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md|
|`mta-sts.txt`|Specifies the policy for SMTP MTA Strict Transport Security (MTA-STS) to enhance email security.|Permanent|RFC 8461|

---
## Web Recon and .well-known
- ال.well-known مهم جدا ف الrecon عشان ممكن من خلال تكتشف endpoints مهمه او configuratuion details مهمه برضو من اشهر ملفات الconfig اللي بتكون فيه هو  `openid-configuration` وده خاص بالOAuth 2.0 protocol
```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```