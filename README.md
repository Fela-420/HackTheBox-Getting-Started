# HackTheBox-Getting-Started
![image](https://github.com/user-attachments/assets/3cb31f74-0a32-480d-b933-1aae9b2a32cb)

This blog serves as my first HackTheBox journey :)

learned how to setup VPN and utilize own virtual machine for flexibility
how organization of files and notetaking is very important
intro to penetration testing distros
Basic Tools
shells like bash, reverse shells, bind shells, ssh, socat
intro to Tmux — a terminal that extend its features
Vim — as a text editor tool
Service Scanning
NMAP commands and scripts
engage with SMB shares and CTF
Web Enumeration
In this section I found a new way to enhance my note taking by using checklists, instead of lengthy and over detailed notes step by step to recall any procedures and methodology to perform web enumeration.

This new finding will serve as an update for my notetaking technique. I call this “Checklisting and redirecting to childnote” if ever I needed to dive deeper into details. I find this more effective to have a central page to look first before jumping from one child note to the other. I recently discovered this from Alex notes from TCM Security. Im a fan of.
![image](https://github.com/user-attachments/assets/01b775ff-a461-42cd-bf09-c7501ebb7500)


Public Exploits
performed google search for public exploits after identifying a vulnerable wordpress plugin

In Metasploit I used an auxiliary module suggested by Rapid7 to perform the attack

![image](https://github.com/user-attachments/assets/9f1480e5-9ab0-4c48-8c3d-15330a2f5a7a)

Rapid7 exploit

msfconsole module
After I specified necessary options to set and exploit the target. It took me a couple of minutes to realize that the answer was outputted in the loots folder of my machine and not read the file directly in the client-side browser
![image](https://github.com/user-attachments/assets/daca0c9a-6081-4987-b697-27d62189011a)


Concatenate this directory out
Types of Shells
Reverse Shell
Bind Shell
Web Shell
Privilege Escalation
In this activity I was able to leverage access from user1 to user2 after finding out that sudoers permissions for user2 does not require any password for the bash file. From there I was able to spawn a shell to get the flag thats sitting in user2 desktop.
![image](https://github.com/user-attachments/assets/bcae2665-150c-4a8c-8446-65b655f55042)


Finally for the second tasks after gaining access to user2 my target was to elevate to root privileges from there. I first checked what permissions I have to the user2’s id_rsa file. After knowing I have read permissions to the file, I went to copy the RSA key, then paste it on my linux machine, changed the permission to the duplicated id_rsa file on my machine to chmod 600 to change the file’s permissions to be more restrictive, then attempted to ssh using -i to specfify user2’s RSA key. From there I was able to gain root privileges.
![image](https://github.com/user-attachments/assets/0f532518-2cba-46f7-bcd6-ada5107adaa0)


user2 RSA key
![image](https://github.com/user-attachments/assets/e0c1c13e-66e3-47bd-a339-419d2f44ed75)

Gaining Foothold
At this activity we already had credential at hand. My task was to spawn a shell to establish a remote connection with the server. When we do, we then upgrate to a more interactive TTY.

The attack vector was determined to be in this upload feature found in the admin panel’s plugin section. This appears to be a feature for the nibbleblog which is a PHP blogging engine.
![image](https://github.com/user-attachments/assets/3828fe77-ba77-40ed-ba08-df43a55a3e4b)


In here, I created a single-liner php payload to test if RCE can be achieved using <?php system('id'); ?> we then map out where the uploaded php file could have been stored.
![image](https://github.com/user-attachments/assets/57a656d8-869b-44c2-921e-4c24fb9a90bd)


The location of where the uploaded plugins are stored.
to execute the crafted payload, curl must be invoked to execute it. And it did. From here, in my machine I created a reverse shell payload, then spawned a netcat listener. Delivered it through upload, and again triggered the execution using curl command. Voila! we spawned a shell, time to upgrade it our TTY to have a more interactive and stable shell.
![image](https://github.com/user-attachments/assets/a3e330a5-28f5-4518-8d21-87e0da6811a0)


I first checked out what python version is installed in the server, then ran python3 -c ‘import pty; pty.spawn(“/bin/bash”)’
![image](https://github.com/user-attachments/assets/9a3db8cc-bd0a-4c17-b829-45ad2104ea59)


I was able to locate the flag from here using locate command.
![image](https://github.com/user-attachments/assets/e3daaa2e-6a98-4611-8207-433ed2e4ef17)


Capstone
In this section, I have touched down the final module which is to perform hands-on web penetration testing for the given target web application. I challenged myself not to rely on walkthroughs or any tips or hints during the engagement, and solely rely on my notes which on the other hand helps prove how effective my notetaking has gone this far.

I started off, performing information gathering, scanning using nmap, enumerating the web application. In this scan I found what ports were open and what the site was hosted from.
![image](https://github.com/user-attachments/assets/b9b49e2e-502a-4558-9a32-e39d67214fdf)


Performed a basic nmap scan to the target
Furthering my enumeration, I used nmap script to squeeze all necessary information I can gather from the server and found a couple of what appears to be directories that exist from the server. Other than this, I performed banner grabbing but did not get anything relevant, inspect the source code and noticed GetSimple being a CMS pops out everywhere but from here the version was still unknown.
![image](https://github.com/user-attachments/assets/2da01e29-71c1-4841-9051-f38ce41e43ed)


I checked the site’s robots.txt confirming the existence of admin page.
![image](https://github.com/user-attachments/assets/0e604b61-576e-40dc-a3a0-b204ead6297f)


After accessing the admin portal, I was presented with a login page
![image](https://github.com/user-attachments/assets/019596d8-d20e-42cd-a02c-416fa2e9697e)


admin:admin
As I know the website is using GetSimple CMS, I googled if default credentials are available. This was not changed from the owners perpective thus I was successfully able to login as administrator. Therefore, changing default passwords is a MUST! After logging in, the CMS version was presented to us.
![image](https://github.com/user-attachments/assets/4771544e-6d0f-456b-a417-f196e796ae63)


With multi-tasking I spun up gobuster to list possible directories available and found a couple of interesting folders.
![image](https://github.com/user-attachments/assets/80a23205-0e98-4ff9-9040-a9018137745e)


Which led me to explore even deeper holes over the file directory listing, then redoing reconnaissance and enumeration from the perspective after gaining initial access.
![image](https://github.com/user-attachments/assets/8eff4085-7ff1-42af-a1fc-a20e6e08dc45)


I did a lot of work from here. Searching for public exploits manually and using automated tools such as searchsploit and metasploit and still had no signs of progress. Interesting enough is that, I found an upload tab but it gets me nowhere when clicked. To the point I started to think outside the box and inpected html elements just to see where one button points to. So I had to go back to the drawing board and look at the current position am at so I wont fall into more rabbit holes. I started to reflect if there is something I could have missed from the beginning and during my information gathering phase. So I went to read the site’s main page and found out this…

![image](https://github.com/user-attachments/assets/35ca91e9-1f78-4e3c-9651-aff8ceb64de8)

I knew from the start when I was inspecting the page and looking up to the GetSimple documentation that this was something I could make use of. Due to that my instinct got me after I glanced at it the second time around.
![image](https://github.com/user-attachments/assets/4d4fc1df-579c-405a-b8ad-59aca7ae6dee)


Then tested a one-liner php script -> hit Save -> refresh the page where this could possibly reflected and success!
![image](https://github.com/user-attachments/assets/8cdfd753-08c2-437b-862d-2ed578d27f25)


From there on, I crafter a nifty one-liner script for a reverse shell to gain foothold. I spawned a netcat listener from my machine to fetch the connection and establish a shell.
![image](https://github.com/user-attachments/assets/4596f908-e4d2-44c0-a788-0a83481445a9)


The first flag was found which is the user’s flag. From here I had to look for a way to escalate my privileges and gain root access from their server.
![image](https://github.com/user-attachments/assets/7f8314a5-58df-497a-931c-f0074129e512)


I went to look at my privesc checklist and performed the action one step at a time. From here I was looking for any sudo privilege applications from this user using the command below, and found out php can be ran as sudo.


With this in mind, immediately remembered I could utilize GTFObins from here.
![image](https://github.com/user-attachments/assets/f95c2277-64ce-4203-959d-b46ea10b0e49)


Crossing my fingers that this would work…
![image](https://github.com/user-attachments/assets/ddf19bd9-45ab-4a5e-9c2b-10566e545ccb)


And it did!!!
![Uploading image.png…]()


I was able to gain root access and captured the root flag to wrap up the learning I acquired and completed the module. I am liking the things I am learning so far in this platform! :)
