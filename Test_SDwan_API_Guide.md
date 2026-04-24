# Test Cisco SD‑WAN API Using Python

This guide shows how to **test the Cisco SD‑WAN (vManage) API using a simple Python script**.
It covers logging in and preparing the script **up to running it**.

---

## What this script will do

- Log in to Cisco SD‑WAN Manager using session-based authentication (`/j_security_check`)
- Store the session cookie (**JSESSIONID**) automatically
- Prepare for an API call to `/dataservice/device`

---

## Prerequisites

- **Python 3.8 or later** installed
- Internet access
- Visual Studio Code (recommended)

---

## Step 1 — Install required Python libraries

Open a terminal (or VS Code terminal) and run:

```bash
pip install requests urllib3
```

---

## Step 2 — Create the Python script

1. Open **VS Code**
2. Create a new file named:

```text
sdwan_api_test.py
```

---

## Step 3 — Add the API test script

Paste the following code into `sdwan_api_test.py`:

```python
import requests
import urllib3

# Disable SSL warnings (sandbox uses self-signed certs)
urllib3.disable_warnings()

HOST = "https://sandbox-sdwan-2.cisco.com"
USERNAME = "devnetuser"
PASSWORD = "RG!_Yw919_83"

# Create session (stores cookies like JSESSIONID)
session = requests.Session()

# Login request
login_url = f"{HOST}/j_security_check"
payload = {
    "j_username": USERNAME,
    "j_password": PASSWORD
}

response = session.post(login_url, data=payload, verify=False)

# Simple API test result
if "JSESSIONID" in session.cookies:
    print("✅ API login successful")
else:
    print("❌ API login failed")

```

---

## Step 4 — Run the script

In the VS Code terminal (from the same folder):

```bash
python sdwan_api_test.py
```

If the login is successful, the script will print confirmation and the session ID.
