# first steps

As usual nmap kicking in.
![image](https://user-images.githubusercontent.com/84482765/126548036-840afdaa-230b-4718-9b54-4c16487e045b.png)

![image](https://user-images.githubusercontent.com/84482765/126548087-7737a269-5167-4f8f-932e-816c2a40c9c3.png)
![image](https://user-images.githubusercontent.com/84482765/126548115-f40b2478-c68f-4842-80dc-f6bd5c3780f3.png)

There is no port 80, so no website. I've checked just in case i overlooked something but definetly no aparent website.
I see ports 445 and 139 so i think about SMB connection. But thats just it. i see no aparent way to get in.

i see kerberos at port 88. And it reminded me of one of the keywords from this box in hack the box, called "kerberoasting" so i follow it.
After quite a bit of googling and having a better idea of what is kerberos and what is kerberoasting i think this might be a way in.

If you are curious have a read. I strongly suggest reading it first to better understand what i will do.
https://www.qomplx.com/qomplx-knowledge-kerberoasting-attacks-explained/
https://owasp.org/www-pdf-archive/OWASP_Frankfurt_-44_Kerberoasting.pdf

From what i researched there are a few tools for this. The one i first started trying is called impacket (https://github.com/SecureAuthCorp/impacket).


Got stuck for a long time because i was having problem with one module that refused to work. Got stuck installing, reinstalling, and trying diferent versions.
All this because i was using the .py script direclty from the "examples" folder from impacket install. 
But luckly i noticed in my BIN folder those same scripts but with "impacket-" at the start. And using those i had no issue.
While i was stuck trying to fix that module issue i used searchsploit to check for that kerberos thing, and there are a few that i can use. But many of then needed credentials to run.
I also checked the 'PayloadAllTheThings' repo to check what i could use and make sure i used the right syntax.(https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#kerberoasting)

When i managed to use the correct script for impacket i used one of the codes from the repository above and it worked, even though i wasn't expecting it to work because i didn't knew the user

![image](https://user-images.githubusercontent.com/84482765/126550849-409684c2-965c-40a1-be95-ee5716f0e49e.png)

Turns out that "svc-alfresco" is the user. And i double checked it using another impacket script.
![image](https://user-images.githubusercontent.com/84482765/126551042-dcbdca95-b05e-4f49-8ea9-13595612c415.png)

I tried other usernames instead of "svc-alfresco" but they had error messages. 

Now that i have the user i want to get the TGT key for this user.
Also using impacket.

![image](https://user-images.githubusercontent.com/84482765/126551567-ab639262-7a5d-48fa-92e2-ed132f1a5a36.png)


Now that i have a hash i need to crack it. I used john since last time i tried using hashcat it wasn't working properly.

![image](https://user-images.githubusercontent.com/84482765/126552341-49871251-947e-4359-a6ce-9fd9bd67ab4c.png)


NOT YET FINISHED
