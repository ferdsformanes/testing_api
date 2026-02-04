
# REST Client + Cisco Catalyst SD-WAN (vManage) — Short Guide

## 1) Install
- VS Code → **Extensions** → search **REST Client** (by Huachao Mao) → **Install**.

## 2) (Optional) Settings for Sandbox SSL
- VS Code Settings JSON: add
```json
"rest-client.ignoreSSL": true,
"rest-client.environmentVariables": {
  "sdwan": {
    "host": "https://sandbox-sdwan-2.cisco.com",
    "username": "devnetuser",
    "password": "RG!_Yw919_83"
  }
}
```

## 3) Create `cisco_sdwan.http`
Paste the requests below. Use the **Send Request** links in order.

```http
### 1) Login (sets JSESSIONID cookie automatically)
POST {{host}}/j_security_check
Content-Type: application/x-www-form-urlencoded

j_username={{username}}&j_password={{password}}

### 2) (Optional) Get CSRF Token (some POST/PUT calls require it)
GET {{host}}/dataservice/client/token

### 3) Get Device List
GET {{host}}/dataservice/device
```

> Notes
> - After step **1**, REST Client stores the `JSESSIONID` cookie for this host; step **3** then works using that session.  
> - Step **2** returns a token (if issued). For write operations, include header: `X-XSRF-TOKEN: {{token}}`.

## 4) Run
1. Open `cisco_sdwan.http`.
2. Click **Send Request** above each block (login → token → devices).
3. Inspect response pane (headers/body). Save if needed via the **Save** action.

---
### Bonus: Prompt for credentials (no settings needed)
You can use prompt variables so REST Client asks at run time:
```http
@host = https://sandbox-sdwan-2.cisco.com
@username = :{{user}}
@password = :{{pass}}

### Login
POST {{host}}/j_security_check
Content-Type: application/x-www-form-urlencoded

j_username={{username}}&j_password={{password}}

### Devices
GET {{host}}/dataservice/device
```
