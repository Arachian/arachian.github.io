## My First Writeup

### Background
I recently attended a conference where Hack The Box was a sponsor. They also decided to host a CTF for attendees. I had participated in CTFs before (shout out to shellontheborder.com!) but had not done very well for myself. One thing that always happens though is that I end up learning a ton. This CTF was no different.

### The Challenge
This was a challenge in the "Full Pwn" category where you had to find the User and Root flag. Each flag is worth a certain amount of points. I spawned the challenge and got to work.

I started by performing a nmap scan.  
![nmap scan results]({{site.baseurl}}/_posts/nmap_results_storage.png)

Looking at the results I noticed that 22-SSH and 8080-HTTP were both open. I then entered the IP address with the port number at the end. In this case it was 10.129.244.218:8080 which took me to a GitLab Sign-in page. I was able to register for an account, but was unable to log in because an administrator had to approve the account.
![Gitlab Login Page]({{site.baseurl}}/_posts/gitlab_login_storage.png)

The scan also found a robots.txt file with 54 disallowed entries. I got hung up on the robots.txt file as I've never really done these types of challenges before. While I knew what a robots.txt file was, I didn't know that it could contain useful information. After learning this, I became hyper-focused on the information contained in the robots.txt file.  At this point I decided to take a loog at the page source to see what information was available there. When looking at the pae soure, line 3 seemed to stand out to me. I could not recall ever seeing any HTML tags that contained "og:". So I decided to visit the URL (http://ogp.me) and found out it was something called the Open Graph Protocol.
![Gitlab Page Source]({{site.baseurl}}/_posts/page_source_storage.png)

I don't know why I thought to do this, but I decided to fire up Metasploit. I initially searched for "open" which contained too many results to go through so I backed out and searched for "graph". This was pure luck. I found a scanner that would enumerate users in the GitLab GraphQL API. 
![Metasploit scanner]({{site.baseurl}}/_posts/graphql_enumeration_storage.png)

I ran the scanner and navigated the text file that the results were saved to. At this point, I knew there was a root user and the user I created. I probably could've assumed that there was a root account since I have to find the root flag, but I wasn't sure if there was some other user already registered for whic I had to get the flag from...anyway.
![Metasploit scan results]({{site.baseurl}}/_posts/graphql_enumeration_results_storage.png)
![Metasploit scan results]({{site.baseurl}}/_posts/graphql_enumeration_results2_storage.png)

I had an idea once I saw there was a root user. I went back to the address bar, entered the URL for the GitLab log in page and put a __/root__ at the end of it. This took me to the "Admin" profile, but I wasn't logged in. Again, never seen anything like this before so I started to poke around to see what I could find.  I clicked on Projects, Groups, Snippets, and Help...nothing useful there. I saw a question mark icon with a drop down arrow so I clicked that then clicked on the first item, "What's New". It listed some version numbers and it says my version is 13.9. Ok, cool.  

Now, I had been asking for advice in the Arkansas Hackers Discord server. When I mentioned that I knew the version, a user there mentioned that there was a CVE for version 13.9 ([CVE-2021-22205](https://github.com/inspiringz/CVE-2021-22205 "CVE-2021-22205"). So I clicked the link they provided and tried to run it. I got a result saying the payload was sent successfully but it didn't do anything. I was doing something wrong or not taking an additional step...hmm. 

Again, I'm new at this. I mostly play on the Blue Team side of the house. All of my points up to this were from OSINT, Forensics, and Reversing challenges. At this point, I gave up on the exploit and started focusing on the robots.txt. I was convinced there was a misconfiguration or some other method I could use to log in as root. I don't have any screenshots from me trying Burpsuite and attempting header manipulation, but it didn't work, so it's no big deal. After about an hour of playing around with Burpsuite and researching HTTP methods, I decided to go back to the previously mentioned CVE. 

I had to stop everything and start doing some research. I went to the documentation for the CVE and took the time to look at the screenshots of the exploit being used. __Note to self...slow down and actually try to learn what I'm using...don't be a Joey and just throw commands at somethng to see what sticks!__

After taking the time to research the exploit and see how it worked, I figured out I need to run this 'nc' command that was shown in one of the screenshots on the respository page...but what the heck is 'nc'?! Back to google I went and figured out what netcat was.

Man, at this point I've been working on this challenge for like 3 days. I've learned how to use NMap, Metasploit, and now I learned what netcat was...I'm learning a ton!

I felt confident enough to attempt the exploit again...
![Exploit execution]({{site.baseurl}}/_posts/cve_execution_storage.png)

**HOLY SHIT! IT WORKED!**
![Reverse Shell]({{site.baseurl}}/_posts/reverse_shell_storage.png)

Now to start poking around...I gave it a 'ls -a' command and got a long list of directory and files. I saw a file named 'security.txt' but it didn't contain anything useful. If I hadn't said it already, I don't really know what I'm doing, so I just gave it another "cd .." command then another "ls -a".
![Directory and file list]({{site.baseurl}}/_posts/poking_around_storage.png)

****user.txt****...__COULD IT BE?!__
![contents of user.txt file]({{site.baseurl}}/_posts/flag_storage.png)

W00t! Got the user flag!

I have been beating my head against the wall to try and solve this challenge. This is only the __user__ flag. I still need to find the __root__ flag. At the time of this writing, there is about 34 hours left in the CTF and I'm in 5th place out of 25 teams. Not too shabby for someone at my level. I have been trying to find the root flag, but haven't been able to crack it. 

Again, a big shoutout and thank you to everyone on the Arkansas Hackers Discord server that helped me out along the way! 

Happy hacking!
-__Aaron__ 








Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

