# First steps

So, after i'm conected to the machine and have the IP adress i do a quick nmap.

    nmap -n -vv --open -T4 -p- -oN AllPorts.nmap 10.10.10.245

![image](https://user-images.githubusercontent.com/84482765/123526740-dff21980-d6a7-11eb-93a6-522478af6647.png)

After use another set of flags just to get some more info from these ports

        nmap -sV -sC -oN DetailPorts.nmap -p 21,22,80 10.10.10.245

But results are not that impressive.

I see there is an open http port so i check the website

![image](https://user-images.githubusercontent.com/84482765/123526817-5b53cb00-d6a8-11eb-9aeb-d7f56783891c.png)

there is not much to see in this website. I test the search box for possible XSS by typing "<script>alert(1)</script>" but i quickly see that they have it sanitized.
I check the source code and i see some parts of the code that don't appear in the website
![image](https://user-images.githubusercontent.com/84482765/123526916-12504680-d6a9-11eb-8672-6498a05bfc63.png)
I spent way to much time trying to figure out what was that. There were some "username looking" text in there and i try to connect via SSH, which i know this server have. But it was a dead end. After more time than i want to admit i move on from that suspicious code.

I go back to the topdown menu in there and i see some pages with network info and one with a "download" button.
![image](https://user-images.githubusercontent.com/84482765/123526977-960a3300-d6a9-11eb-875b-7271f16360e9.png)
It takes a little longer to load than the other pages. The file i download from this page is **number**.pcap. That was new for me. So i search how to open or view .pcap files. After a quick google search i see that it contain packet information that is transmited. 

I see that there is a number in the URL that changes each time i click on this page from de menu so i try using Fuzz from OWASP ZAP to check what i get from other numbers. I see it goes up to the number 14 but not much info to go on from that. Just to make sure i go to each number in this URL and download whatever is in there. This would prove to be another time consuming move that was going to bite me in the *A.S.S*.

After some time trying to figure out what was in those files i discovered that WIRESHARK is one of the tools used to read these files. 

I've played around with wireshark before but i dont know very well how to use it. So this would be a fun task... (not)
I opened wireshark and started listening when i loaded the page. Not much from there. Lots of time wasted. 
After digging around in google for some more time i discovered that wireshark is not only a GUI tool (i bet experienced people are cringing right now, sorry bout that). So i use this

        tshark -r 10.pcap -V > tshark.txt

Now i have some info from this .pcap file.

There is a LOT of stuff in this file. Glancing over the wall of text i noticed the same name from the website (nathan) and it was over ftp.
![image](https://user-images.githubusercontent.com/84482765/123527216-8c81ca80-d6ab-11eb-8d35-1327f0b64f93.png)

It means i can connect with this as username.
So i check for password and i find this

![image](https://user-images.githubusercontent.com/84482765/123527231-b6d38800-d6ab-11eb-820b-b8f643c10f81.png)

So hey! thats is it i think!
I go back to my terminal and try to login via ssh

        ssh nathan@10.10.10.245


i use the password i found before and im in!!!

![image](https://user-images.githubusercontent.com/84482765/123527272-17fb5b80-d6ac-11eb-9a4e-1c391c0831a6.png)

Quick LS and i see the flag "user.txt". One CAT later and i have the flag. 



## now for the root flag

now i need to get root privileges. Just in case i try 

        sudo su
But no use.

I got stuck pretty bad here. So in search for some clues i go to the HTB forums. And after i see MANY people saying how easy it was i felt kinda bad for taking so long to get where i'm at now ahahahaha. But that's how it is in the beginning i guess...
From the foruns i see someone linking this site (sorry i can credit who posted it cause i closed the page, but many thanks for that whoever it was!)
https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/

From this site i see some ways to do priv esc. So i try this

        python -c 'import pty; pty.spawn("/bin/bash")'
        
which dont work for some reason
![image](https://user-images.githubusercontent.com/84482765/123527377-f5b60d80-d6ac-11eb-8dc4-0dd787fbde14.png)

So back to google it was. But now i had an idea on how this code worked. Basically from what i see it uses python to import an instance of a module that runs (/bin/bash). Basically an terminal that is already rooted.
So i google for "python import bin/bash" and other variations on this search (i didn't find on my first try)
I end up on this site: https://steflan-security.com/linux-privilege-escalation-exploiting-capabilities/
From this site i see this
![image](https://user-images.githubusercontent.com/84482765/123527458-c8b62a80-d6ad-11eb-90da-90f26d2a69bf.png)

        /usr/bin/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'

It fixes a few problems that the other code had. It goes direclty to "/usr/bin/python3" to run python. 
And uses another module to be imported this time. 

and it worked
![image](https://user-images.githubusercontent.com/84482765/123527505-44b07280-d6ae-11eb-8b32-73d6f465c8ee.png)



## DONE!!

With that we have both the user flag and root flag. 

After all this time here i can see why people from the foruns were saying it was very easy. It still took me a very long time to figure things out. 
Looking back i see that i should pay more atention in the hints given in these boxes. Of course is not like that in real life, but its ok in CTFs.
Really liked this box. Learned new things. 
