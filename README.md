# aws-work  

## Task 1: Launched EC2 instance, created a key pair, connected to EC2 via SSH (`ssh -i ....`)  
> Had to download key pair (put into a separate folder)  
> I had to be in that directory/ folder, then, `ssh -i ...`, ie. copy the command on AWS  
> A note, to ensure privacy, one may need to `chmod +x 400 <file>` to ensure read-only to the owner/ current user (_as well as execute permissions_)  
### EC2 => Elastic Compute Cloud  
- It basically creates mini VMs, useful if you didn't want to invest in multiple local PCs (can operate multiple PCs in  "the Cloud", ie. someone else's/ AWS' servers)  
> - flexible; pay for what you use -- can scale up/ down, suitable for start-ups, enterprise.  
- An EC2 can store data on a virtual drive, called, Elastic Block Storage (like a USB/ HDD for the EC2/ virtual machine)  
- Multiple EC2 instances can access a shared server (Elastic File System) via the NFSv4 protocol  
- An instance store is hardware-level storage, attached to the host's PC. It's temporary, but, fast (low-latency).
- A bootstrap script, accessible via the User Data section. Useful: automates software installation -- can run scripts, install software/ updates etc... I demonstrate this in Task 2.

<br>

## Task 2: Build a WEB Server, on a EC2 instance  
- Could use NGINX/ HTTPD .. 
- I use t4g.micro over the defaulted t3.micro (cheaper)  
> <img width="auto" height="159" alt="image" src="https://github.com/user-attachments/assets/a07dbaa8-d52a-40a0-917f-1e610dd33372" />  
- Needed to allow HTTP for this project  
> <img width="578" height="55" alt="image" src="https://github.com/user-attachments/assets/0e4acd05-0c7d-45c3-8907-a1dbef4b2c63" />  
- Wrote Bootstrap script; User Data script  
> <img width="auto" height="250" alt="image" src="https://github.com/user-attachments/assets/5d36a156-c606-4bdd-b0e6-ea0f6dc8105c" />  
- We redirect the echo hello message `>` into index.html file
> - technically, echo hello message, it is re-directed, not into stdout (terminal), but, into the index.html file.  
- `/var/www/html/index.html` is the default web root on Linux servers.  
- the `$(hostname -f)` gets the Fully Qualified Domain Name, ie. combines the short host's name with the DNS Domain Name.

<br>

Result:  
- When I clicked on the open address button, it tried to access the web server (on the Public IP address), using the HTTPS (443) protocol, which I hadn't enabled..  
> <img width="auto" height="200" alt="image" src="https://github.com/user-attachments/assets/1dbf549b-f9b1-40ee-9aee-470e85b73251" /> (hence, an error)  
- I had to access the website, using http, though, it still wouldn't work.  

I went over and connected to the EC2 instance.  
I tried to manually re-run each command I entered in User data.  
- The `yum install -y httpd` showed HTTPD installing (I suspected, the **bootstrap script**, in User Data, **didn't run**=> I don't think HTTPD installed..)  
> <img width="auto" height="200" alt="image" src="https://github.com/user-attachments/assets/1ac82ebb-fdc6-4d53-b5ab-6e11cecef8dd" />(me installing HTTPD)  

Once installed, I entered the next commands; to try start/ enable HTTPD.. (the output, reinforced my suspicion of bootstrap script not running.)  
> <img width="auto" height="34" alt="image" src="https://github.com/user-attachments/assets/e680419c-d5bf-426e-93a7-5426860f1cb4" />  
- I went over to the IP address (HTTP) and surprisingly, it works...  
> <img width="510" height="134" alt="image" src="https://github.com/user-attachments/assets/290a1184-a00d-41ae-b3d7-720a4c08f4aa" />  

#### Question marks in my mind... why didn't the bootstrap script execute?
- I tried to run the next part of my script, but, it didn't work..  
> <img width="auto" height="200" alt="image" src="https://github.com/user-attachments/assets/4a44b3c0-71a3-4122-b158-71da23e1422d" />(tried to run the echo command; didn't work)  
  **I discovered here**, no index.html file ?!  
> Maybe this error, caused the bootstrap script to fail.  

#### I tried to reboot the EC2 instance, still wouldn't work.. so I am going to try making a fresh instance..  
- I entered the same bootstrap script in User Data, launched the EC2, went over to the IP Address (HTTP), wouldn't work.  

#### I changed the User Data, tried again  
> <img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/67586cbe-0cca-4d40-be90-3a8f3afc392b" />  
Result: **Still** wouldn't work.  
> <img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/c6678286-3b3a-4dd2-99eb-058a9462082e" />  

#### I wanted to try one thing  
- try launching a x64 x86 EC2 instance, using t3.micro, instead of x64 (Arm), t4g.micro (ie. use the default one, over the cheaper one)  
> <img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/36942438-0a49-4e76-b2c6-4240dfe53467" />  
> <img width="auto" height="204" alt="image" src="https://github.com/user-attachments/assets/3c039608-bb05-49d0-8484-07b8feba7e23" />(Same script)

Result:  
**Still** didn't work  
<img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/9832a262-ba60-4fb9-879a-027f2c55c876" />
- Though now, I was sure, the error was not due to me using the cheaper instance type.

#### One last thing, I wanted to try...  
- I wanted to try starting the script, without the final echo command (containing the apparently non-existent, index.html file)  
Why did I want to do this?  
- I knew that the final echo command was causing an error and I knew that the previous commands were fine (after I connected to the first instance), so I wanted to see, if I can get a working web server, if I cut out the final echo command.  
  <img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/60f9bc69-c052-4543-a87e-d5768e387662" />  
- Even here right, if it doesn't work, I could try separating each command with `&&`s.  

Result:  
Weirdly, it didn't work.. Mhmm  
<img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/3689e570-7728-4307-aea1-e6d590fe9e06" />

#### I'll try to use `&&`s in the Bootstrap script  
<img width="725" height="182" alt="image" src="https://github.com/user-attachments/assets/6ccf5c4e-3d86-40d9-aec2-8e44e09c324b" />  

RESULT:  
**Still** Didn't work  
<img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/33ce07b1-108d-4aec-b48c-53f041a6f59b" />  



