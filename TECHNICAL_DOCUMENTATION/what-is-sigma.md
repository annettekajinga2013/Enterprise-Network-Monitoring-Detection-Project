# Sigma rule
is a generic, open standard format for writing detection rules that describe suspicious or malicious activities in log data. Think of it as the “YARA for SIEMs”—but instead of files, it focuses on log events.

# What Makes Sigma Useful?

It is platform-independent: You write one Sigma rule, and then convert it to the specific query language of your SIEM or log analysis tool (e.g., Splunk SPL, ElasticSearch KQL, Microsoft Sentinel KQL, QRadar AQL).

It provides a standardized structure: Title, description, log source, detection logic, level, tags, and timeframe.

# Key Components of a Sigma Rule

Title & Description
Brief summary of what the rule detects.

ID & Metadata
Unique identifier, author, creation date, status (experimental, stable, etc.).

Logsource
Defines where the data comes from (OS, service, category).

Detection

selection: What to look for (e.g., EventID, message, process).

condition: How to combine selections (selection and another_selection).

timeframe: Optional, defines time window.

Level
Severity (low, medium, high, critical).

