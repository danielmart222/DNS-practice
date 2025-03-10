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

![image](https://github.com/user-attachments/assets/9d00cfe1-b8dc-4b4d-ab0a-08b2b42a6085)


------------

1.	It Checks the Local  DNS Cache first (Fastest) memory.
2.	It Checks the Local Host File second (Faster) host file on the disk
3.	It Checks the DNS server third (slower) reaches out over the internet or checks the DNS server 

-------------

- To see the local DNS Cache Type the Command: ipconfig /displaydns

---------


- There is no  mainframe in the DNS local Cache


---------

![image](https://github.com/user-attachments/assets/db7db707-662d-42a3-8f9a-eb3b84d0e03b)


-------------


- Open notepad as an Administrator to check the Local Host File -> file -> open -> under file type: choose all files -> look for the drivers folder  -> ect -> hosts ->


-----------

![image](https://github.com/user-attachments/assets/57fd4f32-9124-4f0c-9f1d-b8cce824ad7f)


-----------


- In PowerShell Ping Zebra

---------

![image](https://github.com/user-attachments/assets/9bdafee3-7336-4b46-b608-688e90a42ba0)


--------------

- Assign Zebra to 127.0.0.1 (the local loopback address)  in the Host file and save it


-------

![image](https://github.com/user-attachments/assets/b33104ce-7a3d-44f2-b3fb-45c4d702c11a)


------------

- Now Ping Zebra again in PowerShell. You should get a reply. It will look in the Local Host file and find it.


---------------


![image](https://github.com/user-attachments/assets/1137935c-7236-4920-b48b-926695b1d880)


------------

- Ping mainframe again. It cannot find mainframe. It checks all the following.

----------


1.	It Checks the Local  DNS Cache first (Fastest) 
2.	It Checks the Local Host File second (Faster) 
3.	It Checks the DNS server third (slower) 

----------

- Type the command: nslookup mainframe. It cannot find anything for mainframe.

---------

![image](https://github.com/user-attachments/assets/8dc2e043-12bb-4937-936e-2984372cf85d)


------------------


<h2> Step 2: Create a DNS A-record on DC-1 for “mainframe” and have it point to DC-1’s Private IP address </h2>


-------------

- Within DC-1 -> open administrative tools -> Select DNS 

-------------


![image](https://github.com/user-attachments/assets/a11c5566-9eca-4a80-97fa-33a2a3020517)


------------------

- Within DNS manager -> Click on DC-1 -> Select Forward Lookup Zones -> click on Mydomain.com

----------


![image](https://github.com/user-attachments/assets/124b4a23-a3ed-4294-8c9b-d1bdf92a96ca)


-------------

- Find DC-1’s Private IP -> open Power Shell -> Type: ipconfig


---------------

![image](https://github.com/user-attachments/assets/a767d726-8875-4a4d-b61c-174f4e140abd)

---------


- Create a A-Record where the Hostname is "Mainframe" that points to the Private IP address

----------------


- Within DNS manager -> Right click -> select New Host (A or AAA)

-------------

![image](https://github.com/user-attachments/assets/4efe0625-49ff-4b84-b05f-d063446069a7)




-------------

- Name it Mainframe -> Type the Private IP address -> Add Host

-----------

![image](https://github.com/user-attachments/assets/443eaf65-a1a3-41a4-80c2-a9dfa47b9150)


--------------

- Now mainframe has a A-Record

-----------

![image](https://github.com/user-attachments/assets/76eba812-60c4-436d-9524-22d978b137b6)


------------------

- Back in Client-1 -> open Power Shell ->  ping mainframe 


-----------


- You get a Reply, it points to the Private IP address and a Ping is returned. It reached out to the DNS server and got a response. The DNS server now knows who Mainframe is. 


------

![image](https://github.com/user-attachments/assets/9c018231-12ca-40a6-a648-377106273f64)


------------

<h1> Local DNS Cache exercise  </h1>

---------

<h2> Step 1: Within the DNS manager, change the Mainframe A-Record to point to 8.8.8.8 </h2>


--------------

- Within DC-1 -> DNS manager -> double click Mainframe -> Change the IP address to point to 8.8.8.8 -> apply -> ok

-------------

![image](https://github.com/user-attachments/assets/9a831fce-0ee4-4990-9327-dc0948c52c42)


--------------

- Go back to Client-1 and Ping mainframe


--------------

- It’s Pinging 10.0.0.4 and not 8.8.8.8. It’s checking the Local Cache, that’s why it is still pointing to 10.0.0.4 (The old Private IP address)

---------

![image](https://github.com/user-attachments/assets/5dc9645a-ecb4-4998-bab2-2983b98f8429)


-------------

- Type ipconfig /displaydns

------------


- The old Private IP address for Mainframe is showing up in the DNS cache. 


------------

![image](https://github.com/user-attachments/assets/0e42bf17-f449-4155-86af-f6a1425c94f0)


----------

- Flush the DNS Cache

----------

- Type the command: ipconfig /flushdns -> Type: ipconfig /displaydns to observe that it is empty

----------


![image](https://github.com/user-attachments/assets/4a740fbf-8af9-4088-98ad-4a2210b75d89)


---------

- Now ping mainframe, you get a reply back.

-----------


1.	It Checks the Local  DNS Cache first (Fastest) nothing
2.	It Checks the Local Host File second (Faster) nothing 
3.	It Checks the DNS server third (slower) it reached out to the DNS server and found the new IP Address

-----------

![image](https://github.com/user-attachments/assets/94044d68-54e6-4956-9409-13b850745198)

----------



<h1> CName Record Exercise </h1>

-----------

<h2> Step 1:  Create A C-Name Record (matches a human readable name to another readable name) </h2>


-----------

- Map Search to www.google.com

--------------

-	Within DC-1 -> Go to DNS manager ->  Right click and choose New Alias (CNAME) -> Name it Search -> Then for the FQDN Type: www.google.com

-----------


![image](https://github.com/user-attachments/assets/8a7ca591-e0bd-4179-b912-464b91676c0d)


----------

![image](https://github.com/user-attachments/assets/fc1d0755-9343-4133-aa39-58b0e5a45ba5)


-----------

- Within Client-1, Ping Search

------

- You get a reply back. When you Ping Search, it resolves it to google.com

----------

![image](https://github.com/user-attachments/assets/097f15e6-ebe9-4e92-8722-f92d199c8bd3)


--------

- Type the command: nslookup Search


-----------

- It should resolve the name to Google.com


-----------

![image](https://github.com/user-attachments/assets/6002b6b4-0ce6-4f76-a6ab-aa8508b349cb)

