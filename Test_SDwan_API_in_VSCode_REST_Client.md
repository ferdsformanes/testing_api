# Option A (Recommended): Test Cisco SD‑WAN (vManage) API in VS Code with the **REST Client** Extension

This guide shows how to test the **same SD‑WAN API flow you shared** (session-based auth using **JSESSIONID**) directly inside **Visual Studio Code** using the **REST Client** extension and a `.http` request file.

---

## What you will achieve

You will run two REST calls inside VS Code:

1. **Login**: `POST /j_security_check` → vManage sets a **JSESSIONID** cookie
2. **API Call**: `GET /dataservice/device` → REST Client automatically reuses the cookie

If everything works, you will receive a JSON response containing a `data` array of devices.

---

## Prerequisites

- **Visual Studio Code** installed
- Internet access to the Cisco DevNet sandbox host
- VS Code extension: **REST Client** (publisher: *Huachao Mao*)

---

## Step-by-step

### Step 1 — Install the REST Client extension

1. Open **VS Code**
2. Go to **Extensions** (`Ctrl+Shift+X`)
3. Search for **REST Client**
4. Install **REST Client** by **Huachao Mao**

---

### Step 2 — Enable cookie jar (required for JSESSIONID reuse)

Because vManage authentication is **session-based**, the login response sets a cookie (**JSESSIONID**) that must be sent on subsequent requests.

Enable REST Client cookie persistence:

1. Open **Settings**
2. Search for: `cookie jar`
3. Turn on: **Rest Client: Cookie Jar Enabled**

Alternatively, add this to your `settings.json`:

```json
{
  "rest-client.cookieJarEnabled": true
}
```

---

### Step 3 — Create a `.http` request file

1. In your VS Code workspace, create a new file named:

```text
sdwan-test.http
```

2. Paste the requests below. This mirrors your Python flow: login first, then call `/dataservice/device`.

```http
@host = https://sandbox-sdwan-2.cisco.com
@username = devnetuser
@password = RG!_Yw919_83

### 1) Login (creates JSESSIONID cookie in REST Client cookie jar)
POST {{host}}/j_security_check
Content-Type: application/x-www-form-urlencoded

j_username={{username}}&j_password={{password}}

### 2) Get device list (cookie auto-attached if cookie jar is enabled)
GET {{host}}/dataservice/device
Accept: application/json
```

---

### Step 4 — Send the login request

1. Hover over the first request (`POST /j_security_check`)
2. Click **Send Request**

**Expected result:**
- Status code typically **200**
- REST Client stores **JSESSIONID** in the cookie jar

> Tip: Even if the response body is empty or HTML-ish, the important part is the cookie being set.

---

### Step 5 — Send the device list request

1. Hover over the second request (`GET /dataservice/device`)
2. Click **Send Request**

**Expected result:**
- Status code **200**
- JSON response with a top-level object containing a `data` list

Example (shape only):

```json
{
  "data": [
    {
      "host-name": "...",
      "deviceId": "..."
    }
  ]
}
```

---

## How to confirm it worked

- The second request returns JSON (not an HTML login page)
- The response includes a `data` array
- You can expand the JSON in the REST Client response panel to inspect device properties

---

## Common issues & quick fixes

### Issue: Second request shows an HTML login page
**Cause:** The cookie was not reused.

**Fix:**
- Make sure **Rest Client: Cookie Jar Enabled** is ON
- Run the **login request first**, then the device request

---

### Issue: 401 / 403 Unauthorized on `/dataservice/device`
**Possible causes:**
- Cookie jar is off
- Session expired (sandbox reset)

**Fix:**
- Re-run the login request
- Ensure cookie jar is enabled

> Note: Some vManage versions require an XSRF token; that is handled in a different flow (Option B). This file intentionally documents Option A only.

---

### Issue: SSL / certificate error
The DevNet sandbox may use self-signed certificates.

**Fix:**
- In VS Code Settings, search for **REST Client SSL** and enable the option that allows **unauthorized/self-signed** certificates (wording varies by version).

---

## Next steps

Once `/dataservice/device` works, try additional endpoints in the same `.http` file, for example:

```http
GET {{host}}/dataservice/device/health
Accept: application/json
```

You can build a repeatable API “test suite” by keeping all SD‑WAN requests in one `.http` file and running them as needed.

---

## Reference

- Cisco DevNet Sandbox: SD‑WAN Always-On
- Cisco SD‑WAN Authentication: Session-based authentication (`/j_security_check`)

