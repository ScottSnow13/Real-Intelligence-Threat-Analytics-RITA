# Real-Intelligence-Threat-Analytics-RITA

This walkthrough demonstrates how to use Real Intelligence Threat Analytics (RITA) from Black Hills Information Security. RITA is designed to ingest Zeek logs and analyze data to identify anomalous activities, which could indicate system compromise. In this guide, we will focus on detecting Command and Control (C2) traffic using RITA.

## Requirements:

**`Linux VM`**: Ensure your Linux VM is running.

## Setup and Execution:

1. Starting MongoDB

   RITA uses MongoDB to store analysis results. Start the MongoDB service with the following command:

   ```
   sudo service mongod start
   ```
   
2. Importing VSAgent Logs

   We will analyze network capture data where an attacker uses the VSAgent C2 framework.

   ```
   rita import ~/home/RITA/VSAgent_Logs/ vsagent
   ```

   Example output:

   ```
   [+] Importing [/home/RITA/VSAgent_Logs/]:
   [-] Verifying log files have not been previously parsed into the target dataset ...
   [-] Processing batch 1 of 1
   [-] Parsing logs to: vsagent ...
   [-] Parsing /home/RITA/VSAgent_Logs/dns_001_11:08:14-12:00:00.log.gz -> vsagent
   [-] Parsing /home/RITA/VSAgent_Logs/conn_001_11:08:18-12:00:00.log.gz -> vsagent
   ...
   [-] Updating blacklisted peers ...
   [-] Indexing log entries ...
   [-] Updating metadatabase ...
   [-] Done!
   ```

3. Importing DNSCat2 Logs

   Next, we analyze network capture data where an attacker uses the DNSCat2 C2 framework.

   ```
   rita import ~/home/RITA/DNSCat_Logs/ dnscat2
   ```

   Example output:

   ```
   [+] Importing [/home/RITA/DNSCat_Logs/]:
   [-] Verifying log files have not been previously parsed into the target dataset ...
   [-] Processing batch 1 of 1
   [-] Parsing logs to: dnscat2 ...
   [-] Parsing /home/RITA/DNSCat_Logs/dns_001_10:15:23-11:00:00.log.gz -> dnscat2
   [-] Parsing /home/RITA/DNSCat_Logs/conn_001_00:00:00-01:00:00.log.gz -> dnscat2
   ...
   [-] Updating blacklisted peers ...
   [-] Indexing log entries ...
   [-] Updating metadatabase ...
   [-] Done!
   ```

4. Generating Reports

   Generate HTML reports for the analyzed data:

   ```
   rita html-report
   ```

   Example output:

   ```
   [-] Writing: /home/rita-html-report/vsagent
   [-] Writing: /home/rita-html-report/dnscat2
   [-] Wrote outputs, check /home/rita-html-report for files
   ```

5. Reviewing Reports

   Open the generated HTML report in a web browser. The report includes analysis for both VSAgent and DNSCat2.

   VSAgent Analysis

   - Beacons: Look for consistent intervals (strong heartbeat) in connections between the victim and the attacker.

   - User Agents: Check for repetitive and unusual user agent strings.

   DNSCat2 Analysis

   - DNS Analysis: Focus on domains with a high number of subdomains or requests.

6. Analyzing Zeek Logs with zgrep

   Search the Zeek logs for specific patterns to further investigate anomalies:

   ```
   cd ~/home/RITA/DNSCat_Logs/
   zgrep nanobotninjas dns*
   ```

   Example output:

   ```
   dns_001_10:15:23-11:00:00.log.gz:1490022923.690245  CbEtxF16rCk0utMKw2  10.234.234.105  56122   8.8.8.853   udp 18453   03b001050c0d05fbab.cat.nanobotninjas.com    1   C_INTERNET  16  TXT 0NOERROR    F   F       T   T   0   TXT 18 acd601050cfbab0d05   59.000000   F
   ...
   ```

## Cleanup:

After completing this walkthrough, clean up by deleting the imported databases and stopping the MongoDB service.

### Delete Databases

```
for db in $(rita list); do rita delete -f $db; done
```

### Delete HTML Report Directory

```
rm -fr ~/rita-html
```

### Stop MongoDB Service

```
sudo service mongod stop
```

## Conclusion:

This walkthrough demonstrated the use of RITA for analyzing Zeek logs to detect advanced threats. By focusing on beaconing and DNS analysis, we can identify anomalies indicative of C2 activities. This approach highlights the importance of continuous network monitoring and advanced analysis techniques in cybersecurity.






   
