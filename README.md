# Active Directory Project

## Objective
![Active Directory Project Diagram1 drawio](https://github.com/user-attachments/assets/cf12b08f-388c-470b-9e4a-29d7a1a7183e)


The Active Directory Project aimed to created a basic understanding of how Active Directory is used in a real world environment. To do this, I created 3 virtual machines on Microsoft Azure. I was able to create two 2022 windows machines and one instance of Ubuntu. One Windows machine was meant to act as the user machine, and the other acted as the Active Directory Domain Controller. This is where I would be able to create new users,reset/change passwords, or unlock users in the Windows Active Directory. I used the UBUNTU machine to download Splunk and used that as a way to monitor traffic between the machines, and see detailed information about successful log-ons. 

### Skills Learned


- How to setup Virtual Machines on Microsoft Azure.
- Basic Understanding of Windows Active Directory.
- Able to successfully create Telemetry between devices on a network.
- Basic understanding of IPs and ports.
- Ability to use RDP to connect to devices. 

### Tools Used


- Security Information and Event Management (SIEM) system for log ingestion and analysis: Splunk
- Hypervisor allowing mulitple Operating systems to run on one computer: Microsoft Azure
- Windows powershell to SSH into the Ubuntu machine 


## Steps



The first step in this assignment was to create a free account with Microsoft Azure. This gave me access to Azure's free services, along with $200 credit to use on the machines. Due to only having access to the free services, I was limited to only 4 virtual CPUs for my project. This meant I had to make sure I had enough memory to power each machine. For both windows machines, I went with the standard B1ms size, meaning they both had 2 gigs of RAM, but only used 1VCPU each. This allowed me to give more RAM to the Ubuntu machine, which was going to be a lot more resource-heavy. I decided to give the UBUNTU machine standard B2ms, meaing it had 8 gigs of RAM to work with, while only using 2 vCPUs. 
![Screenshot 2025-06-16 131637](https://github.com/user-attachments/assets/2e941499-58ab-4eef-994c-ec8444022f02)

After creating all 3 virtual machines, I was able to start them up and access them through rdp files available to download from the Azure portal. 
This gave me full access to both machines.
![Screenshot 2025-06-16 131715](https://github.com/user-attachments/assets/e065ca15-a490-42ce-95d9-215089e8ee03)

To access Ubuntu, I used the Windows Powershell to SSH into the machine. I was able to do this using this command:

ssh -i C:\Users\Tyler\Desktop\UbuntuSplunk_key.pem Splunk@4.227.xxx.xx

This allowed me to use UBUNTU and ping the windows machines, to make sure they are able to communicate with each other. To ping the windows machines, I needed to type in ping along with their private ip addresses. It looked something like this: 
ping 10.0.x.x
When I did this, however, I got no response from the target machines. To fix this, I tried opening up these ports in Azure for all machines on the domain:
![Screenshot 2025-06-16 131840](https://github.com/user-attachments/assets/46cfd823-1c90-4e7a-9550-1a11807c1e6c)
Each port is in charge of different things. Although each port has a different function, I was able to use the same source IP for each one (this being my Public IP). Doing this allowed my machines to be a lot more secure since only inbound traffic within the Public IP was allowed to communicate with the machines. For a better understanding, this is what each port does: 
TCP 443 (HTTPS): used for secure web traffic, encrypts your data. 
TCP 80 (HTTP): used for web traffic, no encryption
TCP 22 (SSH): Secure Shell, securely log into computers or servers. 
TCP 3389 (RDP): Remote Desktop Protocol, used to remotely control a Windows machine. 

After opening these ports for all machines, I still wasn't able to ping them. After searching for an answer, I tried enabling ICMPv4 ports on both Windows machines, along with the "File and printer Sharing (Echo Request)-ICMPv4 and v6.
![Screenshot 2025-06-16 142530](https://github.com/user-attachments/assets/a1aa4c2a-9618-4ffa-820d-b92a4af2dc42)
After doing this, I was finally able to ping both windows machines from Ubuntu. 

Now that telemetry was made between the machines, it was time to create the example user that would be using the test machine. Before creating the user, however, I had to download active directory onto the Domain Controller machine. To start, I click on "add roles and feature".
![IMG_6324](https://github.com/user-attachments/assets/146d3649-108b-4e9b-829c-29b5df5c3dd4)
From here, I was able to select Active Directory/Domain Services to download to the Domain Controller. 
![IMG_6325](https://github.com/user-attachments/assets/1a944c3b-cde4-42b9-be05-0ec1851856ed)

After it was done installing, the machine needed to restart. Once it was restarted, the Active Directory resources were ready to use. I verified this by checking here:
![Screenshot 2025-06-16 132515](https://github.com/user-attachments/assets/702f042d-62c2-417a-851c-320ab10fc344)

I was able to create a new user in the ACtive Directory Users and Computers. From here, I could create users, change/reset passwords, and even unlock/disable users.
![Screenshot 2025-06-16 184808](https://github.com/user-attachments/assets/a0965daa-9219-46d1-87e1-60539c4e0876)

I decided to create a User named James Bond with the username jbond. Bond will be the user that can access the Test Machiine. I was also able to create a temporary password, and enabled it so Bond would have to create a new password after logging on. 
![Screenshot 2025-06-16 132849](https://github.com/user-attachments/assets/8b65c9ec-2d9b-4b90-a618-844d064bb617)

After Creating the user, it was time to sign in as tht user through rdp on the Test Machine. On my first attempt, I wasn't able to sign in as James bond on the the Test Machine. I was given this error: 
![Screenshot 2025-06-16 152330](https://github.com/user-attachments/assets/d060472e-3f74-4ed0-9916-1f1eb35bd45e)

To fix this, I did some research online and found out how to fix it. In the Remote Desktop Connection properties on the Test Machine, I was able to allow jbond as a user in the domain who can remote access the device.
![Screenshot 2025-06-16 152624](https://github.com/user-attachments/assets/c4568bdc-3684-4db8-980f-58d80b4041ca)

I also had to make sure the Test Computer was in the right domain (TJDemo.local). I was able to do this in the "rename this pc(advanced)" setting.
![Screenshot 2025-06-16 133452](https://github.com/user-attachments/assets/5fd5aa09-5f77-4f9f-85b6-86b19ea02202)

Once this was done, I was finally able to sign into the James Bond user on the Test Machine.
![Screenshot 2025-06-16 153332](https://github.com/user-attachments/assets/467e4539-503d-4ee4-815b-aacc8c7d7381)

Now that I had worked in the ACtive Directory on the Domain Controller Machine, and created a new User for the Test Machine, it was time to download Splunk onto Ubuntu and monitor the traffic on our devices. 

To start, I needed to update and upgrade my Ubuntu. 
![Screenshot 2025-06-16 154341](https://github.com/user-attachments/assets/bad81921-d6e7-417e-9e27-84eed0a01ed3)

After this was done downloading, I downloaded the debian version of Splunk off the Splunk website. I chose the Splunk Enterprise version for this project.
![Screenshot 2025-06-16 154838](https://github.com/user-attachments/assets/d57a2660-3da5-432c-8009-d3615cadcc50)

To install splunk on my Ubuntu machine, I used this command:
![Screenshot 2025-06-16 155527](https://github.com/user-attachments/assets/4c388bb3-d367-4b8f-85a2-f57ae4acfaf5)

From here, I need to be in the bin directory. To get there I used this in the command line:
![Screenshot 2025-06-16 155652](https://github.com/user-attachments/assets/679a1916-7262-4dfc-b601-66b4416e3d44)

To run the splunk binary I needed, I typed in ./splunk.  This allowed me to create an account for splunk, which I needed to use the splunk interface on my laptop. To access the interface, I used my Splunk public IP address, along with port 8000.  AFter typing this in my search engine, nothing happened. After doing some research I realized I needed to open port 8000 for my Ubuntu machine in Azure. After openining this port. I was greet with this interface:
![Screenshot 2025-06-16 160725](https://github.com/user-attachments/assets/19b688f8-f713-461a-a5cd-cbc5f6dab148)

After signing in, I downloaded th app "Splunk Add-on for Microsoft Windows". This helps a lot with Windows machines, which is what I am using in this project. The next step was to create a new index that The windows machines would use. 
![Screenshot 2025-06-16 161304](https://github.com/user-attachments/assets/46c033f2-cd01-4595-93ef-986ff7c34cdb)

I made the index and named it tjadmin-ad. This recieved data from port 9997. 
![Screenshot 2025-06-16 161338](https://github.com/user-attachments/assets/390b5365-c7db-4eae-8769-86ce430199c4)

Next, I needed to download the Splunk universal forwarder on both windows machines. This program is what allows the traffic to be sent to the Splunk interface. Once downloaded, it asked for the Hostname/Ip. I put the private IP for the Splunk machine, along with the default port 9997
![Screenshot 2025-06-16 162431](https://github.com/user-attachments/assets/7dded178-1d6a-4263-943f-f12ab0ef4639)

In order to make this work, however, there was a little more configuration I needed to do. For both windows machines, I had to copy the inputs.conf file from the default folder in the SplunkUniversalForwarder system folder. I then pased that file into the local folder. 
![Screenshot 2025-06-16 162905](https://github.com/user-attachments/assets/fce7db7b-2ca7-4743-aa3c-f4039ac1fdf6)

Once this was done for both machines, I had to slightly edit the inputs.conf files. At the bottom of the files, I added this:
[WinEEventLog://Security]
Index = tjadmin-ad
disabled = false

After adding that to the inputs.conf files in both windows machines, I restarted the splunkforwarders on both machines
![Screenshot 2025-06-16 163747](https://github.com/user-attachments/assets/80b03dc7-365d-4ba0-809e-f610db5b96a2)

The final step before checking the interface was the allow the port 9997 in the Ubuntu shell:
![Screenshot 2025-06-16 164133](https://github.com/user-attachments/assets/223c72b0-98cd-43b3-978a-881d04572632)

Now that all the steps were complete, I can sign into the Slunk interface and monitor traffic through the tjadmin-ad index. I can see who signs into both Windows machines, along with the IPs of the devives accesing the machines. 
![Screenshot 2025-06-16 170055](https://github.com/user-attachments/assets/917d3130-824a-4b90-a1a0-88daabf70999)




### Conclusion/Final Thoughts

Overall, this was a huge learning experience for me. Nefore this I had never worked with virtual machines or active directory. It was also very interesting working with the Windows Powershell, using it to SSH into the Ubuntu machine. It was also very interesting working with Splunk. It was very interesting monitoring the traffic and seeing all the different services splunk offers. It was a great time working with Active Directory and it is something I definitely want to learn more! 

