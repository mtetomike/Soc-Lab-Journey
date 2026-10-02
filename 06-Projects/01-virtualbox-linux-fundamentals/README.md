# Project 1 — VirtualBox & Linux Fundamentals

## 1. Project Overview

This project established the foundational Linux and VirtualBox skills required for cybersecurity and Security Operations Centre (SOC) work.

The project focused on understanding the Linux environment, navigating important system directories, identifying security-relevant logs, monitoring authentication activity, and performing a basic investigation of failed authentication events.

A controlled authentication-failure scenario was generated in the Kali Linux virtual machine to simulate an analyst investigating suspicious login activity.

The investigation followed a basic SOC workflow:

**Event → Evidence → Pattern → Investigation → Classification → Conclusion**

---

## 2. Objectives

The objectives of this project were to:

- Become familiar with the Kali Linux environment.
- Understand basic Linux system information.
- Identify important Linux directories used during security investigations.
- Understand where system and security-related logs can be found.
- Use `journalctl` to investigate system events.
- Monitor logs in real time.
- Identify authentication failures.
- Analyse the timing and pattern of authentication failures.
- Investigate the `rhost` field for a possible remote source.
- Distinguish between suspicious activity and known legitimate lab activity.
- Develop an evidence-based SOC investigation methodology.

---

## 3. Lab Environment

### Virtualisation

- Oracle VirtualBox
- Kali Linux virtual machine

### Operating System

- Kali Linux

### Primary Investigation Tool

- `journalctl`

### Supporting Linux Commands

```bash
whoami
hostname
ip addr
uname -a
ls
grep
tail
awk
sort
uniq
wc


4. Initial System Reconnaissance

The first step was to establish basic information about the Linux system.

Commands Used: 
whoami
hostname
ip addr
uname -a

Purpose
Command	Purpose
whoami:	Identifies the currently logged-in user
hostname: Identifies the system hostname
ip addr: Displays network interfaces and IP configuration
uname -a: Displays kernel and system information

Understanding the host before beginning an investigation is important because as a SOC analyst I need to know which system generated the evidence being examined.



## 5. Linux Filesystem Investigation

I investigated several important Linux directories:

```bash
ls /home
ls /var/log
ls /etc

Important directories identified
/home

Contains users' home directories and personal files.

/var/log

Contains logs and other variable system data.

This directory is particularly important during security investigations because many Linux services write logs here.

/etc

Contains system and application configuration files.

Configuration files can be important during investigations because they can show how services, authentication mechanisms, and other components are configured.


6. Investigating System Logs

I used journalctl to examine recent system events:

sudo journalctl -n 30

This displayed recent entries from the system journal.

A security-relevant event showed a privileged session being opened through sudo.

The event demonstrated that Linux logs can provide information about:

Time of activity
Hostname
Process or service involved
Process ID
Authentication mechanism
Account involved
Privilege level
Session activity
SOC Relevance

A privileged session is not automatically malicious.

If the activity was expected—for example, an administrator intentionally using sudo—the event may be considered legitimate.

However, unexpected privileged activity could require further investigation.

This demonstrated an important SOC principle:

An event should be investigated in context rather than automatically classified as malicious.

7. Real-Time Log Monitoring

I then used:

sudo journalctl -f

The -f option follows the journal and displays new events as they occur.

This allowed me to monitor authentication-related activity in real time.

A SOC analyst can use similar log-following concepts during troubleshooting and investigation, although production SOC environments normally centralise telemetry into platforms such as SIEM systems rather than relying solely on a single host's terminal.

Section 3: Authentication Investigation

## 8. Controlled Authentication Failure Test

A controlled authentication test was performed inside the Kali Linux virtual machine.

I intentionally entered an incorrect password while attempting to switch users.

The system generated an authentication-related event similar to:

```text
Failed Su

This confirmed that the failed authentication attempt was being recorded by the system.

Investigation Significance

This demonstrated the relationship between:

User Activity → System Event → Security Log → Analyst Detection

9. Searching for Authentication Failures

I searched the journal for authentication failure events:
sudo journalctl | grep -i "authentication failure"


This allowed the investigation to focus specifically on authentication-related events.

I then counted the matching events:

sudo journalctl | grep -i "authentication failure" | wc -l

The result was 3

Therefore, three matching authentication-failure events were identified in the current lab data.

10. Analysing the Event Pattern

The most recent authentication failures were examined using:

sudo journalctl | grep -i "authentication failure" | tail -n 20


The timestamps showed that the authentication failures were clustered closely together.

This was an important observation.

A number of authentication failures occurring within a short period can be a useful indicator for further investigation because repeated failures may occur during:

Password guessing
Brute-force attempts
Automated authentication attempts
A user repeatedly entering an incorrect password
Misconfigured applications or services

However, the pattern alone does not prove that a brute-force attack occurred.

Further investigation and contextual evidence are required before classifying the activity.

11. Investigating the Remote Host

I investigated the rhost field associated with the authentication events.

rhost represents the remote host associated with an authentication attempt when that information is available.

The investigation showed that the rhost fields were present but did not contain an IP address.

This was consistent with the authentication failures being deliberately generated locally inside the Kali Linux virtual machine.

Therefore, the investigation did not identify a remote attacking IP address.


12. SOC Investigation Assessment
Observed Event

Three authentication-failure events were identified.

Pattern

The events were clustered closely together in time.

Source

The authentication activity was generated locally within the Kali Linux machine.

Remote Source

No remote IP address was recorded in the rhost field.

Known Activity

The failures were deliberately generated as part of a controlled security investigation.

Classification

Benign / Expected Lab Activity

Reason

Although repeated authentication failures can be associated with malicious activity, the investigation established that these particular events were intentionally generated during testing.

There was therefore insufficient evidence to classify them as an actual brute-force attack.


Section 4: Analysis & Lessons Learned

## 13. Detection Gap / Investigation Lesson

During the investigation, a search for a combination of terms initially returned no results even though authentication failures were known to exist.

This demonstrated an important SOC investigation concept:

### A search failure is not automatically a false positive.

A **false positive** occurs when a detection identifies activity as suspicious but investigation determines that the activity is legitimate.

In this case, the issue was related to **searching or filtering the available log data**, rather than the detection incorrectly identifying legitimate activity.

This can be considered a basic example of a potential **detection or search gap**.

---

## 14. Command Pipeline Analysis

I also used a Linux command pipeline to examine authentication events:

```bash
sudo journalctl | grep -i "authentication failure" | awk '{print $1,$2,$3}' | sort | uniq -c


This demonstrates how Linux command-line tools can be combined for security analysis.

journalctl
    ↓
grep
    ↓
awk
    ↓
sort
    ↓
uniq


journalctl

Retrieves journal events.

grep

Filters events containing the authentication-failure text.

awk

Extracts selected fields from each log line.

sort

Sorts the extracted information.

uniq -c

Counts identical extracted entries.

Important Limitation

Because the pipeline extracted only the timestamp fields before using uniq -c, the resulting count represented repeated identical timestamp values rather than necessarily representing the total number of authentication failures.

This is an important lesson in log analysis:

The output of a command depends on which fields you choose to analyse.


15. Evidence-Based Investigation

The investigation followed a basic SOC methodology:

1. Identify

Authentication failures were identified in the system journal.

2. Validate

The events were confirmed by reviewing the actual journal entries.

3. Analyse

The timestamps were examined to determine whether the events were isolated or clustered.

4. Investigate Source

The rhost field was examined for a remote source.

5. Establish Context

The authentication failures were known to have been intentionally generated during testing.

6. Classify

The events were classified as benign lab activity.

7. Document

The investigation methodology and findings were recorded for future reference.


16. What I Learned

This project taught me that cybersecurity monitoring is not simply about finding an event and immediately calling it an attack.

I learned how to:

Navigate a Linux security environment.
Identify important Linux directories.
Understand the purpose of /var/log and /etc.
Use journalctl to investigate system activity.
Follow logs in real time.
Identify authentication failures.
Filter logs using grep.
Count matching events.
Analyse timestamps and event patterns.
Investigate the rhost field.
Understand the difference between local and remote authentication activity.
Distinguish a suspicious pattern from confirmed malicious activity.
Understand false positives versus detection/search gaps.
Combine Linux commands into investigation pipelines.
Make conclusions based on evidence rather than assumptions.


17. Key SOC Analyst Takeaway

The most important lesson from this project was:

A security alert is the beginning of an investigation, not the conclusion.

Three closely grouped authentication failures may warrant investigation, but an analyst must determine:

Who attempted authentication?
Which account was targeted?
When did the attempts occur?
Where did they originate?
Were they local or remote?
How many attempts occurred?
Were the attempts successful?
Was the activity expected?
Is there additional evidence of malicious behaviour?


Only after examining the available evidence should the activity be classified.

18. Future Investigation

The next stage of the SOC lab will introduce network-based authentication activity.

The objective will be to create a controlled environment where authentication attempts originate from another system.

This will allow investigation of additional telemetry such as:

Source IP address
Destination IP address
Authentication protocol
Target username
Number of failed attempts
Time between attempts
Successful authentication following failures
Network traffic
Potential brute-force indicators

This will provide a more realistic transition from local Linux log analysis to network-based SOC investigation.


19. Project Status

Status: Completed — Fundamentals & Authentication Log Investigation

Environment: Kali Linux on Oracle VirtualBox

Primary Investigation Tool: journalctl

Investigation Type: Authentication Event Analysis

Final Classification: Benign Controlled Lab Activity

Next Project: Network Reconnaissance / Network-Based Security Investigation



































