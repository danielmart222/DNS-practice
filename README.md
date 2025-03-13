<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1> DNS Practice Lab </h1>
In this tutorial, I will use Client-1 and DC-1 to do a few exercises to better understand DNS. I will inspect DNS A-Records on the server (hostname to IP address mappings), create some of our own A-Records on the server, and observe them from the client-1. I will also delete records from the server and observe the Client-1 DNS cache to gain understanding. Then, I will create a CNAME Record (mapping one name to another name).

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- DNS Manager
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)



<h1> Domain Naming System  </h1>

---------
![image](https://github.com/user-attachments/assets/d08d7b4d-6c60-49e6-91fb-b22fc74ceaac)


-----------

![image](https://github.com/user-attachments/assets/ded9546b-9d22-40ab-a063-fe9fb2889725)


------------




<h1> A-Record Excercise </h1>

------------

<h2> Step 1: Log into DC-1 and Client-1 from the previous labs.  </h2>

--------

- Open Powershell and Ping mainframe within Client-1, the ping attempt fails 

--------

![image](https://github.com/user-attachments/assets/34fb6862-db8d-4036-a57a-70987b97ba8e)


------------

- When a Computer tries to interact with any Hostname on a Network 

1.	It Checks the Local  DNS Cache first (Fastest) memory.
2.	It Checks the Local Host File second (Faster) host file on the disk
3.	It Checks the DNS server third (slower) reaches out over the internet or checks the DNS server 

-------------

- To see the local DNS Cache Type the Command: ipconfig /displaydns

---------


- There is no  mainframe in the DNS local Cache


---------

![image](https://github.com/user-attachments/assets/60781656-f56f-447f-a52b-dacad56dcd5b)

-------------


- Open notepad as an Administrator to check the Local Host File -> file -> open -> under file type: choose all files -> look for the drivers folder  -> ect -> hosts ->


-----------

![image](https://github.com/user-attachments/assets/57fd4f32-9124-4f0c-9f1d-b8cce824ad7f)


-----------


- In PowerShell Ping Zebra

---------

![image](https://github.com/user-attachments/assets/0a72ff7d-f992-4911-a9d9-731441947396)


--------------

- Assign Zebra to 127.0.0.1 (the local loopback address)  in the Host file and save it


-------

![image](https://github.com/user-attachments/assets/212bfdff-8d51-4cb3-9c86-3fbe18ea9fdf)


------------

- Now Ping Zebra again in PowerShell. You should get a reply. It will look in the Local Host file and find it.


---------------


![image](https://github.com/user-attachments/assets/b8b1a0ea-bff5-49dd-b5dc-bacc94cb116c)


------------

- Ping mainframe again. It cannot find mainframe. It checks all the following.

----------


1.	It Checks the Local  DNS Cache first (Fastest) 
2.	It Checks the Local Host File second (Faster) 
3.	It Checks the DNS server third (slower) 

----------

- Type the command: nslookup mainframe. It cannot find anything for mainframe.

---------

![image](https://github.com/user-attachments/assets/e2fdd25c-70e3-47ee-9225-ba4f5c89828c)


------------------


<h2> Step 2: Create a DNS A-record on DC-1 for “mainframe” and have it point to DC-1’s Private IP address </h2>


-------------

- Within DC-1 -> open administrative tools -> Select DNS 

-------------


![image](https://github.com/user-attachments/assets/f99947d4-e46c-44a7-8632-311837e6842c)


------------------

- Within DNS manager -> Click on DC-1 -> Select Forward Lookup Zones -> click on Mydomain.com

----------


![image](https://github.com/user-attachments/assets/7e6fadf1-0b06-46a4-92b9-c37568459ef2)


-------------

- Find DC-1’s Private IP -> open Power Shell -> Type: ipconfig


---------------

![image](https://github.com/user-attachments/assets/48ea7dda-0023-44b6-a917-2fcf19969627)


---------


- Create a A-Record where the Hostname is "Mainframe" that points to the Private IP address

----------------


- Within DNS manager -> Right click -> select New Host (A or AAA)

-------------

![image](https://github.com/user-attachments/assets/7eeab52a-5339-4eef-8715-2e623aa28f0b)


-------------

- Name it Mainframe -> Type the Private IP address -> Add Host

-----------

![image](https://github.com/user-attachments/assets/36f891db-ebd1-45c7-b654-5406863cc9b0)


--------------

- Now mainframe has a A-Record

-----------

![image](https://github.com/user-attachments/assets/76eba812-60c4-436d-9524-22d978b137b6)


------------------

- Back in Client-1 -> open Power Shell ->  ping mainframe 


-----------


- You get a Reply, it points to the Private IP address and a Ping is returned. It reached out to the DNS server and got a response. The DNS server now knows who Mainframe is. 


------

![image](https://github.com/user-attachments/assets/437d1348-82f6-41fb-835a-4be2a9412f76)


------------

<h1> Local DNS Cache exercise  </h1>

---------

<h2> Step 1: Within the DNS manager, change the Mainframe A-Record to point to 8.8.8.8 </h2>


--------------

- Within DC-1 -> DNS manager -> double click Mainframe -> Change the IP address to point to 8.8.8.8 -> apply -> ok

-------------

![image](https://github.com/user-attachments/assets/61460be2-99ae-43f2-ab65-9ddc23b140ef)


--------------

- Go back to Client-1 and Ping mainframe


--------------

- It’s Pinging 10.0.0.4 and not 8.8.8.8. It’s checking the Local Cache, that’s why it is still pointing to 10.0.0.4 (The old Private IP address)

---------

![image](https://github.com/user-attachments/assets/73b64528-bfbe-4d6f-b616-b839e7746f81)


-------------

- Type ipconfig /displaydns

------------


- The old Private IP address for Mainframe is showing up in the DNS cache. 


------------

![image](https://github.com/user-attachments/assets/7d3f4247-f29e-4162-b2b6-f48c3f9bc3e7)


----------

- Flush the DNS Cache

----------

- Type the command: ipconfig /flushdns -> Type: ipconfig /displaydns to observe that it is empty

----------


![image](https://github.com/user-attachments/assets/4a740fbf-8af9-4088-98ad-4a2210b75d89)


---------

- Now ping mainframe, you get a reply back pointing to the New IP address 8.8.8.8

-----------


1.	It Checks the Local  DNS Cache first (Fastest) nothing
2.	It Checks the Local Host File second (Faster) nothing 
3.	It Checks the DNS server third (slower) it reached out to the DNS server and found the new IP Address

-----------

![image](https://github.com/user-attachments/assets/878aadc0-ef10-4e1b-8572-60fa299cace4)


----------



<h1> CName Record Exercise </h1>

-----------

<h2> Step 1:  Create A C-Name Record (matches a human readable name to another readable name) </h2>


-----------

- Map Search to www.google.com

--------------

-	Within DC-1 -> Go to DNS manager ->  Right click and choose New Alias (CNAME) -> Name it Search -> Then for the FQDN Type: www.google.com

-----------


![image](https://github.com/user-attachments/assets/b45432ad-b20d-4f6c-affb-be17ca846018)


----------

![image](https://github.com/user-attachments/assets/a268fd0c-41c1-448f-a227-ba52ef0db4f3)

-----------

- Within Client-1, Ping Search

------

- You get a reply back. When you Ping Search, it resolves it to google.com

----------

![image](https://github.com/user-attachments/assets/95c0efd5-2311-4db1-8aec-c4ae3f84f339)


--------

- Type the command: nslookup Search


-----------

- It should resolve the name to Google.com


-----------

![image](https://github.com/user-attachments/assets/5642bf00-ad89-4a86-a01e-068c719caf60)


