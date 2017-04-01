# Sedna Vulnhub writeup
Sedna was pretty straight forward with bit of twist and turns. Cheers to Simon for building the VM. 
Once the VM booted it displayed the IP address which saved us from a pointless discovery scan. 
 ![Welcome_page](/img/1.png)
 
Anyhow performing nikto on all http services, running around with nmap can be quite cumbersome. Hence as usual practice I fired up the DeepScan script to do all the basic enumeration for me. The beauty of the script is that it enumerates all common ports and services automatically while you can go for a coffee. You can find the DeepScan script here: https://github.com/tahmed11/DeepScan 
 ![Deepscan_Demo](/img/2.png)
 
At this stage the scan came back with quite few open services and information. I wasted quite a bit of time going after port 8080 as it seemed to be the vulnerable service. After jiggling with PUT method to upload a shell and trying default tomcat credentials it became quite apparent port 8080 wouldn’t work. So I moved on to the next service. 

  ![Deepscan](/img/3.png)
  
Deepscan showed a unix user crackmeforpoints I assume it’s another post exploitation flag maybe after cracking the shadow file. I took a note of it and moved on.  
 ![crackmeforpoints](/img/4.png)
 
 Nothing seemed out of ordinary from the nikto scan:
 
 ![Nikto_again](/img/5.png)
 
But close inspection of the license.txt revealed the BuilderEngine application was installed.

 ![license_file](/img/6.png)
 
Searching the exploit-db database revealed BuilderEngine has an arbitrary file upload vulnerability. 

![searchsploit](/img/7.png)

To confirm if the vulnerable php file exists visited the following link: http://192.168.117.132/themes/dashboard/assets/plugins/jquery-file-upload/server/php/ 

![check_file](/img/8.png)

Now to put the exploit into action. If the exploit works the shell would be uploaded under the files directory. Before the exploit files directory:

![before_exploit](/img/9.png)

Uploaded a reverse shell through the exploit.

 ![shell_upload](/img/10.png)
 
Files directory after exploitation. The shell was uploaded so it means the exploit worked.

  ![after_exploit](/img/11.png)
  
Now all is left to get the initial shell.
# Finally Shell

  ![shell_finally](/img/12.png)
  
Time to get the first flag.

   ![flag_1](/img/13.png)
 
## Privilege Escalation:
Looking at the kernel version: 3.13.0 it was quite apparent that it is vulnerable to the new kernel exploits like the dirty cow. But I tried to look for any vector through common misconfigurations. Hence ran the usual linux enumeration scripts. Ran out of patience soon and went straight for kernel exploits.  As expected dirty cow worked like a charm and got the second flag.

 ![dirty_cow](/img/14.png)
  
  ![privesc](/img/15.png)
  
  Overall a fun VM. Good weekend challenge. 
   
