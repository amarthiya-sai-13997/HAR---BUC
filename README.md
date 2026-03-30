# BUC Complete Test Plan — JMeter Performance Test

## What This Test Plan Does

This JMeter test plan automates the **Bulk User Creation (BUC)** flow in ADManager Plus. It simulates one or more technicians logging in and creating users in bulk by uploading a CSV file through the web interface.

The test covers the full end-to-end flow:
1. Load the login page and extract CSRF tokens
2. Technician login
3. Navigate to the Bulk User Creation page
4. Upload the user CSV file
5. Validate CSV contents and OU structure
6. Submit the bulk user creation request

## Prerequisites

- **JMeter 5.6.3+** installed and available on the path
- **`TechnicianLogins.csv`** — placed in the same directory as the JMX file
- **`sample.csv`** — placed in the same directory as the JMX file
- The ADManager Plus server must be accessible at the configured `BASE_URL` and `PORT`

## CSV Files

### `sample.csv` — Bulk User Creation Data
Contains the users to be created in bulk. Headers must be `givenName` and `password`.

```
givenName,password
Jmetertest001,Test@123
...
Jmetertest010,Test@123
```

### `TechnicianLogins.csv` — Technician Login Credentials
Fed to each JMeter thread via the CSV Data Set Config. Must have at least as many rows as the configured thread count.

```
TECH_USER,TECH_PASS
jmeter001,Test@123
...
jmeter100,Test@123
```

> **Note:** `TechnicianLogins.csv` must have at least as many entries as the thread count (`-Jthreads`). The included file has 100 entries.

## How to Run

### Default settings (10 threads, 10s ramp-up)

```bash
jmeter -n -t BUC_Complete_TestPlan.jmx
```

### Scale to 100 technicians

```bash
jmeter -n -t BUC_Complete_TestPlan.jmx -Jthreads=100 -Jrampup=30
```

### Scale to 150 technicians

```bash
jmeter -n -t BUC_Complete_TestPlan.jmx -Jthreads=150 -Jrampup=60
```

> If scaling beyond 100 threads, update `TechnicianLogins.csv` to include sufficient entries.

## Variable Reference

| Variable | Default Value | Description |
|---|---|---|
| `BASE_URL` | `perfclient-2` | Hostname or IP of the ADManager Plus server |
| `PORT` | `8443` | Port number of the ADManager Plus server |
| `PROTOCOL` | `https` | Protocol (`https` or `http`) |
| `CONTAINER` | `OU=JMeterUsers,DC=report1,DC=com` | LDAP container/OU where users will be created |
| `DOMAIN_NAME` | `report1.com` | Active Directory domain name |
| `CSV_FILE` | `sample.csv` | Path to the bulk user creation CSV file |

Variables can be overridden at the command line with `-J<variable>=<value>`, for example:

```bash
jmeter -n -t BUC_Complete_TestPlan.jmx -JBASE_URL=myserver -JPORT=8080
```
