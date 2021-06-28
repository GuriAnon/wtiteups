
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

I cat the passwd file to get some more info 

![image](https://user-images.githubusercontent.com/84482765/123560383-c4eadc80-d76f-11eb-8dbf-670fc07fc9d4.png)

And with that i see a "brucetherealadmin" user, but no password, so i check /etc/shadow but i have no permission to see that.

While i do other tests and look through the files in the server i run hydra to try to find they key to connect via ssh.

        hydra -l brucetherealadmin -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.233 -t 4

My other tests don't go anywhere and i can't do much. But after some time i get the password for the connection!

And there goes the user flag

![image](https://user-images.githubusercontent.com/84482765/123560658-a554b380-d771-11eb-80a3-7ed42b4bac4f.png)


## Root flag

Now we neet some way to get root privileges.

I had my head stuck in the rabbit hole of this priv esc. But i did it...

So I first did a few tests once inside to see what was the path to do this priv esc.
One of the firs was

        sudo -l
        
to see what commands i can run with sudo. Because this might lead me to the right path.

![image](https://user-images.githubusercontent.com/84482765/123563479-d093ce80-d782-11eb-9c4c-39205e331243.png)


So there is this SNAP command i can use. Ok... now what is it and what it does i have to find out.

one google search later and i check GTFObin: https://gtfobins.github.io/gtfobins/snap/
This show me that i have to make the payload and load it into the server machine and execute the snap command from there. 

After a painful time trying to get this to work, i couldn't. I installed FPM to compile the script. I copied it to the server over SSH and tried to run but no use.
I also found this github:
https://github.com/initstring/dirty_sock/
That is basicaly a made script just for this. But i was also having some problems runing it. Downloaded both python scripts and neither worked...
But now i knew the name of this way of priv esc is "DIRTY SOCKS" so i googled it and came across this website: https://0xdf.gitlab.io/2019/02/13/playing-with-dirty-sock.html
And over this website there was another way of doing it. On this site there is a long line that i can echo into a file in the server itself and save that as .snap and run the same end code i from the GTFObin site.

![image](https://user-images.githubusercontent.com/84482765/123563660-ed7cd180-d783-11eb-8759-894eb9aec8fd.png)

now i just need to

        sudo /usr/bin/snap install --devmode script.snap

And BAM!!!! it installed.
This script creates another user. And this user is not as limited as the other one. Username and password being "dirty_sock"

        su dirty_sock

to become that new user. Password being the same. But i'm not root yet. I instantly tried geting inside the root folder to get the flag because i was so exited.
But i needed one more code

        sudo -i
        
With that i become root and now i'm able to get the final flag!!!!

![image](https://user-images.githubusercontent.com/84482765/123563822-d7234580-d784-11eb-8dd8-137d0d3bd55f.png)


## Done!!!

All and all it took me a few hours of head scratching to figure all this. I try to avoid spoilers as much as possible, but i roamed around the HTB foruns for a bit trying to get some tips on what direction i shoud go.

What i liked about this box was more the trouble of finding how to priv esc. I think i cheesed a little using hydra to get the SSH password. I saw other people using diferent methods. They found the hashes first somehow (idk how they did that because i did not do that) and had to decode the hashes to get the password. And some other got it from the files in those directories somehow.
But the priv esc was fun. Trying diferent versions of the same method to get where i needed.

