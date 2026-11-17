# AWS Elastic Beanstalk Java Application Deployment (Sydney — `ap-southeast-2`)

This repository documents the end-to-end Platform as a Service (PaaS) deployment of a Java web application using **AWS Elastic Beanstalk** in the **Asia Pacific (Sydney) — `ap-southeast-2`** region. Elastic Beanstalk automates capacity provisioning, reverse proxy routing (Nginx), health monitoring, and runtime configuration on **Amazon Corretto 25 (64-bit Amazon Linux 2023)**.

---

## Environment Overview & Resource Inventory

| Parameter | Configuration & Resource Details |
| :--- | :--- |
| **AWS Service** | AWS Elastic Beanstalk (Managed PaaS) |
| **AWS Region** | Asia Pacific (Sydney) — `ap-southeast-2` |
| **Application Name** | `priya-app` |
| **Environment Name** | `Priya-app-env` |
| **Environment ID** | `e-ypgpqix665` |
| **Platform & Runtime** | Corretto 25 running on 64-bit Amazon Linux 2023 (`v4.14.3`) |
| **Endpoint URL** | `http://elasticbeanstalk.com` |
| **Health Status** | `Ok` (Verified via Elastic Beanstalk Environment Health Monitor) |

---

## Architecture & Managed Provisioning Flow

When creating `Priya-app-env`, AWS Elastic Beanstalk automatically orchestrates the underlying cloud infrastructure:
1. **Compute Layer:** Launches an Amazon Linux 2023 EC2 instance pre-configured with the **Amazon Corretto 25** Java runtime.
2. **Reverse Proxy Layer:** Configures **Nginx** on the instance to accept incoming HTTP (`Port 80`) traffic and forward requests to the backend Java application port (`5000` / `8080`).
3. **Network & Security:** Creates and attaches a dedicated Security Group permitting inbound web traffic (`HTTP:80`) and assigns a public `.elasticbeanstalk.com` DNS endpoint.
4. **Health & Log Management:** Continuously tracks environment health (`Pending` → `Ok`) and provides one-click access to application and Nginx logs.

---

## Step-by-Step Deployment Guide

### Step 1: Create Elastic Beanstalk Application & Environment
1. Open the **AWS Management Console** and switch the region to **Asia Pacific (Sydney) `ap-southeast-2`**.
2. Navigate to **AWS Elastic Beanstalk** → **Create application**.
3. Configure the application and environment details:
   * **Environment tier:** `Web server environment`
   * **Application name:** `priya-app`
   * **Environment name:** `Priya-app-env`
4. **Platform Configuration:**
   * **Platform:** `Java`
   * **Platform branch:** `Corretto 25 running on 64bit Amazon Linux 2023`
   * **Platform version:** `4.14.3` (Recommended)
5. **Application Code:** Select **Sample application** (or upload a custom compiled `.jar` / `.war` bundle).
6. **Presets & Service Access:** Select **Single instance (Free Tier eligible)** for lab/demo environments, configure or attach the default Elastic Beanstalk service role (`aws-elasticbeanstalk-service-role`) and EC2 instance profile (`aws-elasticbeanstalk-ec2-role`), and click **Submit**.

---

### Step 2: Monitor Environment Initialization
* Watch the **Events** log inside the **`Priya-app-env`** dashboard as Elastic Beanstalk creates the Security Group, Elastic IP, and EC2 instance (~2 minutes).
* Wait until the **Health** indicator transitions from `Pending` to **`Ok`**.

---

### Step 3: Verify the Live Application
1. Click the generated domain link in the **Environment overview** card:
   ```text
   http://elasticbeanstalk.com
   ```
2. Confirm the live landing page loads cleanly:
   > *"Welcome to Your Elastic Beanstalk Application — Congratulations! Your Amazon Corretto application is now running on your own dedicated environment in the AWS Cloud."*

---

## Deploying Custom Application Updates

To deploy a new version of a Java `.jar` or `.war` application without manual server configuration:
1. Open **Elastic Beanstalk → Environments → `Priya-app-env`**.
2. Click **Upload and deploy** in the top-right corner.
3. Upload your packaged `.jar` / `.war` file, assign a unique **Version label** (e.g., `v2`), and click **Deploy**.

---

## Key Troubleshooting Lessons & Cleanup

* **Initialization Timing (`ERR_CONNECTION_REFUSED`):** Accessing the `.elasticbeanstalk.com` URL while the environment status is still `Pending` returns `Connection refused` until Nginx and the Corretto runtime finish bootstrapping (~2 minutes) and Health reaches `Ok`.
* **HTTP vs. HTTPS Default Endpoint:** Default `.elasticbeanstalk.com` environments serve traffic over `http://` (`Port 80`). Use `http://` explicitly unless an SSL/TLS certificate is attached to a Load Balancer.
* **Post-Lab Resource Cleanup:** To prevent ongoing hourly EC2 charges after testing, select **`Priya-app-env`** → **Actions** → **Terminate environment** to cleanly remove all Elastic Beanstalk-managed AWS resources.
