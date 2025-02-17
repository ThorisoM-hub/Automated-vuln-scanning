
# Automated Vulnerability Scanning and Reporting System

## Objective:
To develop an automated system for vulnerability scanning and report generation. This system will use **OpenVAS** or **Nessus** to identify vulnerabilities in a system or network and automatically generate reports for further analysis and remediation.

## Skills Demonstrated:
- **Automated Vulnerability Scanning**: Utilize industry-standard tools (OpenVAS or Nessus) to perform regular scans and identify vulnerabilities.
- **Automation**: Automating vulnerability scans to reduce manual effort and ensure regular monitoring of the system.
- **Report Generation**: Create reports in various formats (PDF, HTML, CSV) based on scan results to provide actionable insights for remediation.
- **Remediation Concepts**: Automate basic remediation tasks like closing open ports and patching vulnerable software.

## Step 1: Install OpenVAS or Nessus

### Installing OpenVAS or Nessus
```bash
echo "Installing OpenVAS..."
sudo apt update
sudo apt install -y openvas
sudo openvas-setup
# Screenshot 1: Show OpenVAS installation (e.g., screenshot link: https://example.com/screenshot1)

# Uncomment below to install Nessus
# echo "Installing Nessus..."
# sudo apt update
# sudo apt install -y wget
# wget https://www.tenable.com/downloads/api/v1/public/pages/nessus/downloads/11890/download?i_agree_to_tenable_license_agreement=true -O nessus.deb
# sudo dpkg -i nessus.deb
# sudo systemctl start nessusd
# sudo systemctl enable nessusd
# Screenshot 2: Show Nessus installation (optional, if Nessus is used) (e.g., screenshot link: https://example.com/screenshot2)

echo "OpenVAS (or Nessus) installed successfully!"
# Screenshot 3: Successful installation confirmation (e.g., screenshot link: https://example.com/screenshot3)
```

## Step 2: Automate Vulnerability Scanning

### Create a Script for Automated Scanning:
Use cron jobs to schedule scans automatically at specific intervals (e.g., daily, weekly).

```bash
# Example Cron Job (Run every day at 2:00 AM):
# Edit the cron job using `crontab -e` command:
# crontab -e
# Add the following line to automate vulnerability scanning:
# 0 2 * * * /usr/local/bin/openvas-nvt-sync && /usr/local/bin/openvas-start

# For Nessus (Alternative Method):
# #!/bin/bash
# /opt/nessus/sbin/nessuscli scan --name "scan_name" --target "target_ip"

# Python Alternative (Not used in this project but provided for others who prefer it):
# This Python script automates scanning with OpenVAS.

# TARGET_IP=$1  # Target IP for scanning
# SCAN_NAME=$2  # Scan name for report file
# REPORT_PATH="./reports/$SCAN_NAME.pdf"  # Directory to save reports

# # Function to start OpenVAS scan and generate report
# def run_scan(target_ip, scan_name, report_path):
#     print("Syncing NVT (Network Vulnerability Tests)...")
#     os.system("openvas-nvt-sync")

#     print("Starting OpenVAS service...")
#     os.system("openvas-start")

#     print(f"Running the scan for {target_ip} and generating the report...")
#     scan_id = os.popen(f"openvas-cli -h | grep {target_ip} | awk '{{print $1}}'").read().strip()
#     os.system(f"openvas-report -i {scan_id} --output-format=PDF --output-file {report_path}")

#     print(f"Scan complete. Report saved to {report_path}")

# # Example of running the Python script:
# # run_scan("<TARGET_IP>", "<SCAN_NAME>", "./reports/<SCAN_NAME>.pdf")

# Use cron or another task scheduler to run your script automatically.
```

## Step 3: Report Generation and Automation

### Generate Reports Automatically:
Use the OpenVAS CLI or Nessus CLI to generate reports after each scan.

```bash
# For OpenVAS:
# openvas-report -i <scan_id> --output-format=PDF --output-file /path/to/report.pdf

# For Nessus:
# nessuscli report -i <scan_id> --output-format pdf --output-file /path/to/report.pdf

# Save Reports Locally:
# The generated reports will be saved locally at a specified directory, for later review and analysis.
# Screenshot 4: Example of report generation (e.g., screenshot link: https://example.com/screenshot4)
```

## Step 4: Remediation (Automated)

### Automate Basic Remediation Tasks:
- **Closing open ports**:
  - If a vulnerability scan detects an open port that shouldn't be there, use scripts to block or close those ports.

  **Example: Closing an Open Port (Firewall)**:
  ```bash
  sudo ufw deny <port_number>
  ```

- **Patching Vulnerable Software**:
  - If a vulnerable package or software version is detected, automate the patching process. For example, you can automate system updates with a simple script:

  **Example: Automate System Update**:
  ```bash
  sudo apt-get update && sudo apt-get upgrade -y
  ```

- **Stopping Unnecessary Services**:
  - If unnecessary services are running, automate their shutdown or disable them.

  **Example: Disable a Service**:
  ```bash
  sudo systemctl stop <service_name>
  sudo systemctl disable <service_name>
  ```

### Trigger Remediation Based on Scan Results:
- Set up your vulnerability scanning scripts to check the scan results and automatically trigger remediation actions for any high-severity vulnerabilities found.

- These remediation actions can be linked directly to certain vulnerabilities, so if a specific type of issue is found, it is resolved automatically.

```bash
# Example Remediation Function in Bash:
function perform_remediation {
  echo "Starting automated remediation..."

  # Example of closing open port (port 8080 as example)
  sudo ufw deny 8080
  echo "Closed port 8080."

  # Perform system updates to patch vulnerabilities
  echo "Performing system update..."
  sudo apt-get update && sudo apt-get upgrade -y
  echo "System patched successfully."

  # Disable unnecessary services (example: nginx)
  SERVICE_NAME=$1  # Service to be disabled (e.g., nginx)
  echo "Stopping and disabling service: $SERVICE_NAME"
  sudo systemctl stop $SERVICE_NAME
  sudo systemctl disable $SERVICE_NAME

  echo "Remediation complete."
}

# Example of calling remediation script:
# perform_remediation <service_name>
```

### Alternative Using Python for Remediation:
If you prefer Python, here is an example Python code for performing the remediation tasks:

```python
import subprocess

# Function to close an open port (using ufw)
def close_open_port(port):
    subprocess.run(['sudo', 'ufw', 'deny', port])
    print(f"Closed port {port}.")

# Function to patch the system (run system update)
def patch_system():
    subprocess.run(['sudo', 'apt-get', 'update'])
    subprocess.run(['sudo', 'apt-get', 'upgrade', '-y'])
    print("System patched successfully.")

# Function to disable unnecessary services
def disable_service(service_name):
    subprocess.run(['sudo', 'systemctl', 'stop', service_name])
    subprocess.run(['sudo', 'systemctl', 'disable', service_name])
    print(f"Service {service_name} stopped and disabled.")

# Example usage
close_open_port('8080')
patch_system()
disable_service('nginx')
```

## Final Instructions to Run
To execute the script, use:
```bash
./scan_and_report.sh <TARGET_IP> <SCAN_NAME>
```

### Example usage:
```bash
./scan_and_report.sh 192.168.1.1 MyScanReport
```


