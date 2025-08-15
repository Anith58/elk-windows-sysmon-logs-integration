# Integrating Windows Sysmon & Windows Defender Logs into ELK via Fleet

## **Pre-requisites**
- ELK Stack installed and running (Elasticsearch, Kibana, Fleet Server).
- Fleet Server enrolled with at least one Elastic Agent.
- Windows endpoint (VM or physical machine).
- Administrative access to the Windows machine.
- Sysmon installer and configuration file.

---

## **1. Install Sysmon on the Windows Endpoint**

1. **Download Sysmon**  
   [Sysmon Download](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

2. **Download a Sysmon Config File**  
   Recommended: [ Sysmon config](https://github.com/Anith58/Splunk-Active-Directory-Sysmon-Attack-Simulation/blob/main/splunk/sysmonconfig.xml)

3. **Install Sysmon**  
   ```powershell
   sysmon64.exe -i sysmonconfig-export.xml -accepteula
   ```
---

## **2. Enroll the Windows Endpoint into Fleet**

1. **Get Enrollment Token**  
   In **Kibana → Fleet → Agents → Add agent**, choose the Windows policy and copy the enrollment command.

2. **Download Elastic Agent (Windows)** from the Fleet setup page.

3. **Install & Enroll Elastic Agent**  

---

## **3. Add Sysmon Integration in Fleet**

1. In **Kibana → Fleet → Agent Policies**, select the Windows agent policy.
2. Click **Add integration → Search → Windows event**.
3. Enable:
   - **Windows Event Logs**
   - Add Sysmon channel:  
     ```
     Microsoft-Windows-Sysmon/Operational
     ```
4. Save & deploy changes.

---

## **4. Add Windows Defender Integration**

1. In the same policy, **Add integration → Microsoft Defender**.
2. Enable:
   ```bash
   - `Microsoft-Windows-Windows Defender/Operational`
4. Save & deploy.

---

## **5. Verify Data Ingestion**

- **Kibana → Discover**
- Sysmon index:  
  ```
  logs-windows.sysmon_operational-*
  ```
- Defender index:  
  ```
  logs-windows.defender_operational-*
  ```

---

## **6. Troubleshooting**
- If no logs are appearing:
  1. Ensure the Elastic Agent is running:  
     ```powershell
     Get-Service elastic-agent
     ```
  2. Check Sysmon/Defender event logs in Windows Event Viewer.
  3. **Allow Elasticsearch port 9200** in your firewall:  
     ```bash
     sudo ufw allow 9200
     sudo ufw allow 9200/tcp
     
     ```

---

## **7. Optional: Dashboards**
- In **Kibana → Dashboards**, search for:
  - `Windows Sysmon Events`
  - `Windows Defender Alerts`
- Import sample dashboards or create custom visualizations.
