<h1 align="center">Conducting queries in Splunk to identify evidence of Indicators of Compromise IOCs</h1>

Starting off from the dashboard, we see that there are multiple types of alerts displayed coming from both Suricata (an IDS and IPS platform) and Fortigate (a next-gen firewall (NGFW)).
<p align="center">
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20170455.png" />
</p>

<h3 align="center">Under the Suricata alerts, click on “Information Leak” to see the events for this alert. What are the source and destination IP addresses listed under this alert?</h3> 
Luckily there’s only two events under this alert so there won’t be any querying to find the source and destination addresses. In fact once you click on this alert, it’s staring right in front of your face:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20171716.png" />

The IPs can also be found on the left-side panel under Interesting Fields, just click on dest_ip and src_ip:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20171954.png" />
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20172015.png" />


