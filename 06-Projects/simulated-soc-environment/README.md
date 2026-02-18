#Simulated Soc Environment 

##Objective 
Build a Soc environment Operations Centre lab to praactice detecting, analyzing and documenting cyber attacks

____

## Lab Architecture 
-Kali Linux [Attacker Machine]
-Windows 10 [Victim Machine]
-Ubuntu Server [SSH Traget]
-VirtualBox
-Wireshark
-Sysmon 
-SIEM [Log Aggregation and Alerting]

----

## Attack Simulations 
-Nmap port scanning 
-Multiple Failed SSH Login attempts [Brute Force simulation]
-Syn-based port scanning
-EICAR Malware Test File execution 
-Suspicipous process execution

----

## Detection Methods
-Monitored authentication logs 
-Identified repeated failed logins attempts from single source IP 
-Correlated timestamps and source address

## 2 Port Scan Detection [SYN Pattern Analysis]
-Detected  abnormal SYN packet spikes using wiresharks
-Identified scanning behaviour accross multiple ports
-Flagged hgh frequency connection attempts 

## 3 EICAR Malware Detection 
-Uploaded EICAR test file 
-Observed antivirus detection logs 
-Verified alert generation within SIEM dashboard

## 4 File Integrity Monitoring
-Monitored critical system files 
-Triggered alerts when test file modified systemdirectory 
-Logged file hash changes

## 5 Suspicious Process Monitoring 
-Identified abnormal processes via Sysymon logs
-Correlated parent-child process relationships
-Investigated unusual command-line arguments

## 6 SIEM Alerts Dashboard
-Centralized log collection
-Generated alerts for: 
-Brute force attempts 
-Port scans 
-Malware detection 
-File modification 
-Reviewed event timeline and severity levels

## Evidence 
Screnshots located in the /screenshots folder: 

-Nmap scan results 
-Wireshark SYN Scan Traffic 
-SSH Failed login logs
-EICAR Detection Alerts
-SIEM Dashboards Alerts
-FIM Log 
-Suspicious Process Activity 

## Key Skills Demonstrated 
-Log Analysis 
-Network Traffic analysis 
-Threat Detection 
-Incident Investigation
-Alert Triage 
-SOC Documentation 
