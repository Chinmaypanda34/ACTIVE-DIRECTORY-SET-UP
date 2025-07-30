# ACTIVE-DIRECTORY LAB SET-UP 🔥🔥🔥🔥


**1. Lab Overview & Architecture**

<img width="1070" height="865" alt="Screenshot 2025-07-30 023034" src="https://github.com/user-attachments/assets/d379a2a9-d304-4769-a297-1d3864d1acc2" />
**Diagram Explanation:**
 1:-Host Machine: Your physical computer running VMware

 2:-VMnet2: Isolated host-only network (10.0.0.0/24)

 3:-DC-01: Domain Controller providing:

      (*)Authentication

      (*)DNS services

      (*)DHCP (optional)

      (*)Group Policy enforcement

 4:-Domain Members:

      (*)Windows 10 workstations

      (*)Ubuntu desktop joined to domain

 5:-Kali Attacker: Separate machine on the same network

 **VM Inventory & Specs**
 
  1:- DC-01(Domain Controller, 2 CPU, 4 GB RAM, 50 GB DISK, Win Server 2022 Eval [ISO- https://www.microsoft.com/eval])    

  2:- WIN10-ADM(Admin Client, 1 CPU, 2 GB RAM, 40 GB	DISK, Windows 10 Enterprise [ISO-    https://www.microsoft.com/eval])

  3:- UBUNTU-JOINED(Ubuntu Desktop,	1 CPU,	2 GB RAM , 30 GB DISK,	Ubuntu Desktop 24.04 [ISO-https://ubuntu.com/download/desktop])

  4:- KALI	Attacker(Optional)(	2 CPU,	4 GB RAM,	100 GB DISK,	Kali Linux 2025.x [ISO-https://kali.org])

**2. Network & VMnet2 Setup**

    Steps:
     1.Edit → Virtual Network Editor in VMware → Add VMnet2 → Host‑only → Subnet 10.0.0.0/24 → No DHCP.
     2. Attach each VM’s NIC to VMnet2.
     3. In each machine, you have to connect to VMnet2
  <img width="864" height="480" alt="Screenshot 2025-07-30 235858" src="https://github.com/user-attachments/assets/280a0344-ed6c-451b-8686-b6bcc6f07d9e" />


**3. Domain Controller Deployment**

   Startup process:
     
  1.Partition & Install Win Server 2022 (Desktop Experience).
     <img width="1179" height="741" alt="Screenshot 2025-07-30 003350" src="https://github.com/user-attachments/assets/6cf5e507-c8ab-42e5-922e-92df4db03804" />

  2. Rename computer to DC-01
  
  3. Set static IP
     . IP: 10.0.0.100/24
     . Gateway: 10.0.0.1
     . DNS: 127.0.0.1(itself)
     <img width="1045" height="779" alt="Screenshot 2025-07-30 003759" src="https://github.com/user-attachments/assets/7f5e2b9c-954a-4076-9c9c-4033eb1f5cd7" />
  
  4. Install neccesary tools using powershell
     <img width="1023" height="770" alt="Screenshot 2025-07-30 003217" src="https://github.com/user-attachments/assets/5b58207b-d588-46b9-aa5d-4f1cc922f752" />
  
  5. Install AD DS & Promote to DC (we just try using powershell but u can do with windows server )
     <img width="1041" height="775" alt="Screenshot 2025-07-30 003453" src="https://github.com/user-attachments/assets/304e4817-9453-42db-8f85-99efb03819ed" />
     <img width="1044" height="778" alt="Screenshot 2025-07-30 003544" src="https://github.com/user-attachments/assets/c966b8a2-513a-4ffd-911f-e595e91094a6" />


**4. Windows 10 Admin Client Setup**
    
  Steps:
   1. Install Windows 10 Enterprise.

   2. Set hostname to WIN10-ADM.

   3. Configure static IP/DNS (10.0.0.10 → 10.0.0.1 → DNS 10.0.0.100).
        <img width="1030" height="776" alt="Screenshot 2025-07-30 010341" src="https://github.com/user-attachments/assets/f59b5ea2-6237-4ab4-ae86-90ca8b9e81f3" />
   
   4. Join the domain via System Properties → Computer Name → “Change” → pentest.lab
        <img width="1024" height="775" alt="Screenshot 2025-07-30 010425" src="https://github.com/user-attachments/assets/7dcd2f9a-332b-4115-98af-35b2637644ef" />
        <img width="1028" height="774" alt="Screenshot 2025-07-30 010529" src="https://github.com/user-attachments/assets/bb2648ab-5bc5-44bd-aeec-edc34d4a385b" />
        ( click on domain search your domain then it will show you administrator password ,then it will reboot now you can use DC user name/pass to log into windows )
   
   5. check your DC for insure that our window is now join 
        <img width="1023" height="780" alt="Screenshot 2025-07-30 004732" src="https://github.com/user-attachments/assets/0d2178f3-11e2-4501-835a-7e6c76a07354" />
        


**5. Ubuntu Desktop Client Setup**
    
  Steps:
   1. Install Ubuntu Desktop 24.04.
    
   2. Rename to ubuntu-client-01.
   
   3. Configure static IP via nm-connection-editor:
      
       . open terminal type :- nm-connection-editor
      
       . In the GUI that pops up:- select wired connection 1(or whichever is active)-> click edit

       . Go to IPV4 settings tab ( Method -> selct manual and fill the bellow deatils
      
       . Address: 10.0.0.51/24
      
       . Netmask: 255.255.255.0
      
       . Gateway: 10.0.0.1
      
       . DNS: 10.0.0.100 (OUR DC IT MUST RESOLV THE DOMAIN)

       . click save -> Disconnect and reconnect your Ethernet Connection, or reboot

       # unfortunatly😑😑  here i was face some probleam  my DNS was not resolved so i did something diffrent:- 

      ✅ How to Fix It Temporarily:
      
     -> You can enable dual networking for your Ubuntu VM to have both:

     -> Internal network to talk to DC (via vmnet2)

     -> Internet access via NAT or Bridged

     -> 🛠 Option 1: Add a Second Network Adapter
    
     -> Shut down your Ubuntu VM.

     -> Go to VM settings → Network Adapter

     -> Keep adapter 1 as Custom: VMnet2 (to talk to DC)

     -> Add Adapter 2:

     -> Enable "Network Adapter"

     -> Set "NAT" (or Bridged if you prefer direct internet)

     -> Boot the VM again.

  4. Next open terminal and do exact steps :-

         -> sudo nano /etc/systemd/resolved.conf

         -> Find the [Resolve] section. You’ll see example lines prefixed with #

         -> Remove the leading  (#) on the lines you’re adding or changing, so they become active.

         -> [Resolve]
         #DNS=1.1.1.1

         #FallbackDNS=8.8.4.4

         #Domains=example.com

         #DNSStubListener=yes

         -> save it

         -> # 1. Recreate the symlink
            sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf

            # 2. (Re)start the resolver
     
            sudo systemctl daemon-reload
     
            sudo systemctl restart systemd-resolved

            # 3. Verify the file now exists and points correctly
     
            ls -l /etc/resolv.conf
     
            cat /etc/resolv.conf

         -> You should see something like:

             /etc/resolv.conf -> /run/systemd/resolve/resolv.conf
     
             nameserver 10.0.0.100
     
             search pentest.lab

  5. after this now its time for DC joining

         -> sudo apt-get update
     
         -> sudo apt-get install realmd sssd sssd-tools adcli samba-common krb5-user packagekit
 
         -> sudo realm discover PENTEST.LAB
                                       
         -> sudo realm join --user=administrator PENTEST.LAB

         -> If It Succeeds, You’ll See No Output or Just Successfully enrolled machine (for confirm type [ realm list ]

         -> last test :- nslookup pentest.lab , dig +short pentest.lab   (check its working or not )

     HELL YEAH 🔥 Boom!

<img width="1027" height="618" alt="Screenshot 2025-07-30 011858" src="https://github.com/user-attachments/assets/1c84f034-b416-41ce-90cf-db9760aac4d1" /> 
<img width="1282" height="802" alt="Screenshot 2025-07-30 011707" src="https://github.com/user-attachments/assets/71b93597-082b-429c-a294-d60e49c8dafb" />
<img width="1280" height="805" alt="Screenshot 2025-07-30 011755" src="https://github.com/user-attachments/assets/ae41c4ee-e1f1-4295-8091-32aecbae45ea" />
<img width="894" height="532" alt="Screenshot 2025-07-30 004828" src="https://github.com/user-attachments/assets/51743b39-7a47-486d-b438-d57ddb9f30a1" />


**6. AD Object Creation (OUs & Users)**

      1.Steps & Commands: In powershell run as admin..  and run

        -> New-ADOrganizationalUnit -Name "LAB_Assets" \

        -> New-ADOrganizationalUnit -Name "Workstations" -Path "OU=LAB_Assets,DC=pentest,DC=lab" \

        -> New-ADUser -Name "ADM_John" -SamAccountName jadm … -Enabled $true \

        -> Add-ADGroupMember "Domain Admins" -Members jadm \

        -> New-ADUser -Name "USER_Sarah" -SamAccountName sarah … -Enabled $true \


  2. we can follow any youtube vedio for  crate (OU & User) here is steps to create:-

      <img width="1029" height="777" alt="Screenshot 2025-07-30 003854" src="https://github.com/user-attachments/assets/5f0742e9-7486-4fc0-b3c3-a354667a99ee" />
      <img width="1027" height="781" alt="Screenshot 2025-07-30 004027" src="https://github.com/user-attachments/assets/fa03645c-239e-487c-b60c-8eda9c1309f8" />
      <img width="1026" height="774" alt="Screenshot 2025-07-30 004135" src="https://github.com/user-attachments/assets/a7d358ba-f328-4f60-aefd-ce7272b79cb6" />
      <img width="1021" height="768" alt="Screenshot 2025-07-30 004241" src="https://github.com/user-attachments/assets/cbd6d1d0-234f-4e4d-8d4a-e4d3b9a416d2" />
      <img width="1025" height="771" alt="Screenshot 2025-07-30 004612" src="https://github.com/user-attachments/assets/ad9b482f-e742-41db-a2d8-236779a33415" />



 
 
 # NOW IT'S TIME FOR ALL TESTING   ![SighOfReliefGIF](https://github.com/user-attachments/assets/6d0fee4b-93d3-4ace-9b28-6b519967c1a4)

   **1. Verify SRV Records**
    
     Why?
     AD clients locate LDAP and Kerberos services via DNS SRV records—if these are missing or wrong, domain joins and logins fail.
     Where:
     Your Ubuntu Desktop (or any Linux client with dig installed).

     1.**	Open a terminal.**

     2.	Run: dig _ldap._tcp.pentest.lab SRV  -> o/p like [ _ldap._tcp.pentest.lab.  300 IN SRV 0 100 389 dc-01.pentest.lab ]

     3.	Run: dig _kerberos._tcp.pentest.lab SRV -> o/p like [ You should see lines pointing at dc-01.pentest.lab with ports 389 (LDAP) and 88 (Kerberos) ]

     4.	If you want to inspect those records on the DC itself, you have two easy options:

     5.	 DNS Manager GUI -> On DC-01, open Server Manager → Tools → DNS. -> In the left pane expand Forward Lookup Zones → pentest.lab.

     6.	 Right click _tcp (it may be under a “_msdcs” folder or directly under your zone, depending on your setup) and select “Show All Records”

     7.	 You should see _ldap and _kerberos entries in the list, with the target dc-01.pentest.lab and the correct ports.
 
     8.**	NEXT IN WINDOWS -> **

     9.	Open PowerShell as Administrator and run:-> Get-DnsServerResourceRecord -ZoneName "pentest.lab" -Name "_ldap._tcp"   (this is for LDAP SRV record)

     10.	for Kerberos SRV record :- Get-DnsServerResourceRecord -ZoneName "pentest.lab" -Name "_kerberos._tcp" 

     11.	Each will list the SRV record’s properties (priority, weight, port, and target).


  **2. Time Synchronization**

    Why?
    Kerberos (used by AD) is very sensitive to clock skew. If your client’s clock is more than ~5 minutes off from the DC, Kerberos tickets will be rejected.
    Where:
    Verify on Ubuntu Desktop

    1. **Open a terminal**.

    2. Run:- timedatectl status

    3. Check the output, specifically these lines:
        yaml
      Local time:      Sat 2025-07-26 14:23:45 IST
      Universal time:  Sat 2025-07-26 08:53:45 UTC
      RTC time:        Sat 2025-07-26 08:53:45
      Time zone:       Asia/Kolkata (IST, +0530)
      System clock synchronized: yes
      NTP service: active
      o	System clock synchronized: yes → good
      o	NTP service: active → Ubuntu is syncing

    4.	If it says “no” or “inactive,” enable NTP:
        sudo timedatectl set-ntp true
      Then re check timedatectl status.

    5. **Verify on Windows (DC or WIN10 ADM)**

	  6. Log in to your Windows machine (e.g., DC-01 or WIN10-ADM).

    7.	Open PowerShell as Administrator-> Run:-> w32tm /query /status

    8.	Check these fields in the output:
        yaml
        Leap Indicator: 0(no warning)
        Stratum: 2
        NtpServer: dc-01.pentest.lab,0x8
        Poll Interval: ...
        Precision: ...
        o	Stratum ≥ 1 (1 is ideal for authoritative, 2 means it’s syncing upstream)
        o	NtpServer should list your DC or the parent NTP source.

     9.  What to Look For
         •	Both machines’ clocks should be within a minute of each other (check the “Local time” on Ubuntu vs “Local time” stamp on Windows).
         •	System clock synchronized: yes on Ubuntu
         •	Leap Indicator: 0, Stratum: 2, and correct NtpServer on Windows

<img width="1026" height="775" alt="Screenshot 2025-07-30 010704" src="https://github.com/user-attachments/assets/9efa0052-ea09-4c9e-bef2-4aca25a7c9ba" />


     


