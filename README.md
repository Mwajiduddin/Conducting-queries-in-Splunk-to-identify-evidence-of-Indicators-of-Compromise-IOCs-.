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


<h3 align="center">What action did Suricata take for these two events?</h3> 
Click on “Show as raw text” under both of these events and you’ll see what Suricata did:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20172309.png" />

<h3 align="center">What is the signature shared by both of these events?</h3> 
Click on the dropdown arrow under the i column to expand the details to see the signature value:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20172618.png" />

<h3 align="center">For these two logs, combine the two given fields to produce the full website addresses that’s being accessed by the attacker.</h3> 
Make sure you’re viewing the events as raw text and not syntax highlighted. Under the event details we can see the hostname of the website and also the url, we just need to combine these two values to get the full website. 
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20173159.png" />
(note: in some logs, a “/” value needs to be escaped by putting a “\” value in front of it so just don’t add \ when combining the two values).

So for the first event: imreallynotbatman.com/phpinfo.php5  and the second event: imreallynotbatman.com/phpinfo.php 

<h3 align="center">What is the HTTP status code that was returned to both of these requests and does the status tell us?</h3> 

From the raw text we see it’s 404 which means that the server couldn’t find the specific webpage because it doesn’t exist anymore.
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20173730.png" />

<h3 align="center">Going back to the dashboard, click on the alert named “A Network Trojan was detected.” Query up a search so that it brings back a numerical count of every unique signature field within this alert.</h3> 

When clicking on this alert, we already see that the search bar is already prefilled so we just need to add to the search query to have it  show the different number of signatures present in this specific alert and we do this by adding “ | stats count by signature” which shows 12 different signatures:

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20174732.png" />


<h3 align="center">Search through the Suricata logs where the HTTP status code is 200 and then perform a count of each signature field to find two signatures that yield a vulnerability CVE identifier. Do a search on the specified CVE value on the National Vulnerability Database, what is the CVSS version 3 score?</h3> 

To get a signature count for events that have a HTTP status code of 200, we need to edit our search query to reflect so now it’s:

index=* sourcetype=suricata status=200 event_type=alert | stats count by signature
and we see two signature values yielding a CVE value:

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20175918.png" />

If we search up this CVE value on the National Vulnerability Database, we find the CVSS version 3 score to be 9.8 CRITICAL.

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20180148.png" />

<h3 align="center">Going back to the dashboard, click on “MS.Windows.CMD.Reverse.Shell” under the Fortigate Security Alerts. What is the internal hostname address for this event?</h3> 

Clicking on this only brings back one event and there’s no internal hostname value found within this event. The only thing we can find that might help is an internal IP address:

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20180901.png" />

And it makes sense since this log is from Fortigate, which is a firewall, it wouldn’t provide a value of hostname, but we know this alert is about exploiting Windows Command Prompt (CMD) so instead of the source type being Fortigate, we can change it to be Sysmon instead since Sysmon is log source that comes from Windows devices. So we need to change our query to reflect that: 

index=* sourcetype=“xmlwineventlog” 192.168.250.70
and from this we find that internal hostname is we5201srv.waynecorpinc.local: 

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20180901.png" />

<h3 align="center">Click on the “Acunetix Web Vulnerability Scanner” alert under Fortigate and find out the specific version of the scanning tool used.</h3> 

When clicking on this alert, we see multiple events stating that the name of the scanning tool is Acunetix Web Vulnerability Scanner but none of them seem to specify which version is being used. So we change our query, search in a different source type like Suricata instead and we see what results it provides and it shows that version is Version 6 Free Edition:

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20182539.png" />

<h3 align="center">Create a search query that yields a count of events based on severity rating. Once you have this query, save it to the dashboard and figure out what is the count% for the severity rating for High.</h3> 

First we create a query to get the count of events based on severity:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20183217.png" />

Save it to the dashboard by going to Save As > Existing Dashboard > Splunk Investigations > Save to Dashboard. Then go to our dashboard and we see the query we made:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20183443.png" />

Edit > Select Visualization > Pie chart and from here we can see the High count% to be 28.824%:

<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20183541.png" />

<h3 align="center">Now do the same thing with the Fortigate logs and figure out what is the count% for Critical alerts.</h3> 

Similar like the previous answer, we need a search query that will provide us the count of severity:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20184039.png" />

Save it to our dashboard > Edit > Select Visualization > Pie chart and from here we can see the Critical count% to be 0.401%:
<img src="https://github.com/Mwajiduddin/Conducting-queries-in-Splunk-to-identify-evidence-of-Indicators-of-Compromise-IOCs-./blob/main/Screenshot%202026-06-09%20184411.png" />
