Getting Admin Access to Multiple Amazon S3 Buckets

Hi everyone, in this write-up, I will share a bug I discovered in a private program. For privacy reasons, I will refer to it as hidden-bug.com.

Initial Steps

When I first joined the program, I reviewed the scope, which explicitly stated that everything belonging to hidden-bug was in scope. My initial task was to identify the top-level domain (e.g., .com, .net, .org) using various techniques:

Methods Used:

-1 Google Dorking

-2 Bing Dorking

-3 Shodan Searches

-4 Subdomain Enumeration

-5 Web Archive Exploration

After identifying the top-level domain, I proceeded to enumerate the root domains (e.g., hidden-bug.com, hidden-bug.net, hidden-bug.org). Once the root domains were mapped, I performed subdomain enumeration.

![Recon](https://user-images.githubusercontent.com/47279932/225724099-6a09e24c-2ea9-4982-840d-c9f137804f31.png)

First Phase: Discovery and Reconnaissance

During the reconnaissance phase, I discovered a top-level domain, .cloud. I initiated subdomain enumeration for hidden-bug.cloud. One of the subdomains identified was dev-123.hidden-bug.cloud.

When I tried accessing the subdomain, it didn’t resolve. I then attempted to access it using the IP address instead of the hostname. This approach worked, revealing a static shop website running on the ASP framework.

Directory and File Brute-Forcing

Using brute-forcing tools, I discovered a Sitecore CMS login page. Naturally, I attempted default credentials, but they didn’t work. Further research revealed that Sitecore includes a debugger called Glimpse, accessible at Sitecore/Glimpse.axd.

Upon accessing the debugger page, I enabled it by clicking "Turn Glimpse On" and reloaded the page. The Glimpse debugger opened successfully.

![Glimpse Debugger](https://user-images.githubusercontent.com/47279932/225726447-d5cfd32b-52d4-425d-9b4f-96a5d011c8ca.png)

Second Phase: Exploitation

Glimpse provides various tabs, including one labeled Configuration. This tab contained sensitive information, including an Amazon Access Key ID and a Secret Access Key ID.

Using Amazon CLI and Enumerating Permissions

With the keys, I configured the Amazon CLI and used the enumerate-iam.py script to list IAM permissions. The results were alarming: I had admin access to multiple S3 buckets with full permissions (read, write, and delete).

Sensitive Data Discovered

The S3 buckets contained:

Configuration files

Database files

Log files

Other sensitive information

Conclusion

This vulnerability demonstrated the importance of securing sensitive data in configuration files and debugging tools. Always ensure that unnecessary access points like Glimpse are disabled in production environments.

By responsibly reporting this issue, I helped the program secure their resources and prevent potential exploitation. Always practice ethical disclosure when encountering vulnerabilities.
