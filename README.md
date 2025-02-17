
# **Automated Vulnerability Scanning and Reporting System**

## **Objective:**
To develop an automated system for vulnerability scanning and report generation. This system will use **OpenVAS** or **Nessus** to identify vulnerabilities in a system or network and automatically generate reports for further analysis and remediation.

## **Skills Demonstrated:**
- **Automated Vulnerability Scanning**: Utilize industry-standard tools (OpenVAS or Nessus) to perform regular scans and identify vulnerabilities.
- **Automation**: Automating vulnerability scans to reduce manual effort and ensure regular monitoring of the system.
- **Report Generation**: Create reports in various formats (PDF, HTML, CSV) based on scan results to provide actionable insights for remediation.
- **Remediation Concepts**: Automate basic remediation tasks like closing open ports and patching vulnerable software.

---

### **Step 1: Install OpenVAS**

1. **Update package repositories and install OpenVAS:**
   ```bash
   sudo apt update
   sudo apt install -y openvas
   ```

2. **Perform initial setup of OpenVAS:**
   ```bash
   sudo openvas-setup
   ```

3. **Check the status of OpenVAS services:**
   ```bash
   sudo systemctl status openvas-scanner
   sudo systemctl status openvas-manager
   sudo systemctl status openvas-gsa
   ```

4. **Sync the OpenVAS NVT (Network Vulnerability Tests) feed:**
   ```bash
   sudo openvas-nvt-sync
   ```

5. **Access the OpenVAS web interface:**
   OpenVAS can be accessed through your web browser at:  
   `https://<your-server-ip>:9392/`

   Login with the credentials set up during installation.

---

### **Step 1: Alternative Install Nessus (If Preferred)**

1. **Install Nessus:**
   ```bash
   sudo apt update
   sudo apt install -y wget
   wget https://www.tenable.com/downloads/api/v1/public/pages/nessus/downloads/11890/download?i_agree_to_tenable_license_agreement=true -O nessus.deb
   sudo dpkg -i nessus.deb
   sudo systemctl start nessusd
   sudo systemctl enable nessusd
   ```

2. **Access the Nessus web interface:**
   Nessus can be accessed through your web browser at:  
   `https://<your-server-ip>:8834/`

   Login with the credentials set up during installation.

---

### **Step 2: Automate Vulnerability Scanning**

1. **Edit the crontab to schedule automatic vulnerability scans:**
   ```bash
   crontab -e
   ```

2. **Add the following line to automate vulnerability scanning every day at 2 AM:**
   ```bash
   # Synchronize the NVT feed and start OpenVAS scan at 2 AM every day
   0 2 * * * /usr/local/bin/openvas-nvt-sync && /usr/local/bin/openvas-start
   ```

This cron job will ensure that the NVT feed is updated daily, and OpenVAS starts the scanning process at **2 AM** every day.

---

### **Step 2: Alternative for Nessus Scanning**

For Nessus, you can automate the scanning process with a bash script:

```bash
#!/bin/bash
# Example for Nessus scanning
/opt/nessus/sbin/nessuscli scan --name "scan_name" --target "target_ip"
```

---

### **Step 3: Automate Report Generation**

1. **Create a report generation script (`generate_report.sh`) to automatically generate and save reports:**

   ```bash
   #!/bin/bash
   # Script to generate PDF report from OpenVAS scan

   # Set the scan ID (you may need to retrieve this dynamically based on your scanning process)
   SCAN_ID="your_scan_id_here" 

   # Generate the PDF report and save it in the desired location
   openvas-report -i $SCAN_ID --output-format=PDF --output-file=/path/to/save/report_$(date +\%Y\%m\%d).pdf
   ```

2. **Make the script executable:**
   ```bash
   chmod +x /path/to/generate_report.sh
   ```

3. **Schedule the script to automatically run at **3 AM**, to give some buffer after the scan completes (this ensures the scan is finished before generating the report):**

   ```bash
   crontab -e
   ```

4. **Add the following cron job to generate the report daily at 3 AM:**
   ```bash
   0 3 * * * /path/to/generate_report.sh
   ```

This ensures that a report is automatically generated every day at 3 AM, once the scan completes.

---

### **Step 4: Fully Automated Remediation**

1. **Create the remediation script (`perform_remediation.sh`):**

   ```bash
   #!/bin/bash
   # Automated Remediation Script (Fully Automated)

   echo "Starting automated remediation..."

   # Example: Close open ports (example: port 8080)
   echo "Closing open port 8080..."
   sudo ufw deny 8080

   # Perform system updates
   echo "Performing system update..."
   sudo apt-get update && sudo apt-get upgrade -y

   # Disable unnecessary services (example: nginx)
   echo "Disabling nginx service..."
   sudo systemctl stop nginx
   sudo systemctl disable nginx

   echo "Remediation complete."
   ```

2. **Make the script executable:**
   ```bash
   chmod +x /path/to/perform_remediation.sh
   ```

3. **Schedule the remediation script to run every day at **3:30 AM** (just after the report generation script has run at 3 AM, to ensure that everything is completed):**

   ```bash
   crontab -e
   ```

4. **Add the following line to run the remediation script daily at 3:30 AM:**
   ```bash
   30 3 * * * /path/to/perform_remediation.sh
   ```

---

### **Python Alternative for Automation**

If you prefer a Python script to automate the entire process, including scanning, reporting, and remediation, here's an example:

```python
import subprocess
import os

def run_scan(target_ip, scan_name, report_path):
    # Sync NVT feed
    subprocess.run(["openvas-nvt-sync"])

    # Start OpenVAS
    subprocess.run(["openvas-start"])

    # Start the scan and generate the report
    scan_id = os.popen(f"openvas-cli -h | grep {target_ip} | awk '{{print $1}}'").read().strip()
    subprocess.run(["openvas-report", "-i", scan_id, "--output-format=PDF", "--output-file", report_path])

    print(f"Scan complete. Report saved to {report_path}")

def perform_remediation():
    print("Starting automated remediation...")

    # Close open port (8080 example)
    subprocess.run(["sudo", "ufw", "deny", "8080"])

    # Perform system update
    subprocess.run(["sudo", "apt-get", "update"])
    subprocess.run(["sudo", "apt-get", "upgrade", "-y"])

    # Disable unnecessary services
    subprocess.run(["sudo", "systemctl", "stop", "nginx"])
    subprocess.run(["sudo", "systemctl", "disable", "nginx"])

    print("Remediation complete.")

# Example Usage
run_scan("192.168.1.1", "MyScan", "/path/to/report.pdf")
perform_remediation()
```

This Python alternative can automate the scanning, report generation, and remediation tasks if you prefer working with Python.

---

### **Final Instructions to Run**

To execute the script, use:

```bash
./scan_and_report.sh <TARGET_IP> <SCAN_NAME>
```

#### **Example usage:**
```bash
./scan_and_report.sh 192.168.1.1 MyScanReport
```

---
**Conclusion:**
This automated vulnerability scanning and remediation system eliminates the need for manual intervention once it's set up. By leveraging tools like OpenVAS or Nessus, along with scheduled cron jobs and automated scripts, the entire process—scanning, report generation, and even basic remediation—runs autonomously


