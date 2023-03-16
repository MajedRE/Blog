# Getting admin access to multiple s3 amazon buckets.

Hi everyone, in this write-up, I will talk about a bug I found on a private program, I will name it hidden-bug.com.


When I first I get invited to the program, They mentioned in the scope section everything belong to `hidden-bug` in scope, so my initial step is to identify the top-level domain (com,net,org...) using different approach.


### 1- Google dork

### 2- bing dork

### 2- shodan 

### 3- subdomain enumeration

### 4- web archive



After finding the top-level domain, the next step is to enumerate the root domain(hidden-bug.com,hidden-bug.net,hidden-bug.org...), Next we start  subdomain enumeration on the root domain we found.



![Recon](https://user-images.githubusercontent.com/47279932/225724099-6a09e24c-2ea9-4982-840d-c9f137804f31.png)





---------------------------------------------

# First phase


So I did my recon, and I found an interesting top-level domain it was `cloud`, I start  subdomain enumeration on the root domain `hidden-bug.cloud`,one of the subdomain I found was `dev-123.hidden-bug.cloud`.


when I opened the subdomain it did not resolve, the other way I tried was to open the subdomain using his ip instead of the hostname, actually, this trick worked, and the IP was hosting a static shop website, and they use the asp framework for the application.




Now it's Time to a directory and files brute force, suddenly I found Sitecore CMS Login page, as you think trying to enter the default password and username, but this does not work, after some research, I found that Sitecore has debugger called `Glimpse ` and it's accessible under `Sitecore/Glimpse.axd`





I Access the debugger page and click Turn Glimpse On, Then reload the page, and the Glimpse debugger was opened, 


![g](https://user-images.githubusercontent.com/47279932/225726447-d5cfd32b-52d4-425d-9b4f-96a5d011c8ca.png)






#  Second phase


Glimpse has multiple taps one of these taps is configuration, this tap contains sensitive information on of this information I found was amazon Access Key ID and Secret Access Key ID.




The next step is to configure amazon CLI, then I used an enumerate-iam.py script to enumerate IAM permissions,

After the script finished, it turns out I have admin access to multiple s3 amazon buckets with read, write, delete, and this bucket had configuration file database file log files and many sensitive information.


















































