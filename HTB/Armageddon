
# starting up

So first thing i do after connecting to the IP is a quick nmap to check for ports.

    nmap -n -vv --open -T4 -p- -oN AllPorts.nmap 10.10.10.233
With it i discover 2 open ports. 22 and 80. 

From that i check the website.
![image](https://user-images.githubusercontent.com/84482765/123559337-691d5500-d769-11eb-999e-3e7f04c9990a.png)

Not much to go on from that. Just a login page.

I do another quick nmap scan to check more info about these ports.

    nmap -sV -sC -oN DetailPorts.nmap -p 22,80 10.10.10.233 
![image](https://user-images.githubusercontent.com/84482765/123559360-90742200-d769-11eb-9429-8bb79d830ba0.png)

So from there i can see a fre directories in this website. But just in case i run dirb and gobuster also to check for more directories.

![image](https://user-images.githubusercontent.com/84482765/123559401-d7621780-d769-11eb-9df3-4328f48b1f35.png)

I also try nikto to check for info

    nikto -host http://10.10.10.233/ -output nikto-armageddon.txt  

But i get basically the same results.
So i go digging those directories i found from nmap, dirb and nikto. 

There is a lot in there. But all just scripts to be used. Nothing that i see immediately interesting for me. I spent a long time looking through these files just to make sure.

After some time i check again the nmap scan and i see this "**drupal**" 
![image](https://user-images.githubusercontent.com/84482765/123559576-dda4c380-d76a-11eb-8ac9-c0db10af999f.png)

This is something that is running the website. As i belive it is what it does. And from those directories i see many times this word coming up in the files i check.
Quick google search to check
![image](https://user-images.githubusercontent.com/84482765/123559626-2c525d80-d76b-11eb-86f8-9072be6252cc.png)

So i check exploit-db to see if there is something for this. And there is!

https://www.exploit-db.com/exploits/35150
https://www.exploit-db.com/exploits/44557

So metasplot can be used. I boot up my msfconsole and search for drupal

![image](https://user-images.githubusercontent.com/84482765/123559680-789d9d80-d76b-11eb-8552-b1cd0a28b6b2.png)

And i use option number 4. After setting up the options i'm in!
![image](https://user-images.githubusercontent.com/84482765/123559705-9d921080-d76b-11eb-8491-c49806aa36dc.png)




