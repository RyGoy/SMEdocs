# SME-10-Server-Gateway
Goals:
We will setup Koozali SME server 10 in Server/Gateway mode and test the mailserver.

### Part 1 - Installation Target and Presumptions

We have used a bare metal server to host SME Server 10, If you have experience with virtual machines then it can also be easily installed on a VM using ESX.(images included in this tutorial were taken from a VM installation as it was easier to capture images this way)

Your Installation Target (Server/Gateway Machine) must have 1 Network Interface for your local (GREEN) network and 1 Network Interface for your connection to the ISP / outside world (RED network).

We presume that you have a STATIC IP address that is assigned by your ISP. The reason to use a Static IP instead of Dynamic DNS is that our Mail Server needs to have a PTR record added to the STATIC IP to not get caught in spam filers used by gmail's or yahoo's ilk.

---

### Part 2 - Creating The Installation Boot Disc For SME Server 10.
The [following tutorial](vhttps://www.howtogeek.com/127377/the-best-free-tools-for-creating-a-bootable-windows-or-linux-usb-drive/) is summarized below for our purposes.

1. Download [Rufus](https://rufus.ie/en/);

2. Download the [SME Server 10](https://wiki.koozali.org/SME_Server:Download) .iso installation file;

3. Prepare the bootable USB, Insert the disk into your Installation Target;

4. Power on the Installation Target and force the machine to boot from the USB.

Depending on your machine you will be prompted to "press a button" on startup to enter the BIOS or UEFI, [watch this](https://www.youtube.com/watch?v=LGz0Io_dh_I) for more information about your machine's boot procedure.

---

### Part 3 - Installing Via The Text Installer
The SME Server version 10 was launched with an unusable GUI installer, The text installer is quite easy to set up though.

1. Select the proper time zone;

2. Begin the installation.

![image](https://user-images.githubusercontent.com/94795740/211231181-6e7ccc9a-1ac7-41be-8b82-fae0088e7a8d.png)

The installation takes a few minutes and prompts you to "press enter" to continue to the configuration screen when the installation is complete.

---

### Part 4 - Configuing The [SME Server Gateway](https://wiki.koozali.org/SME_Server:Documentation:Administration_Manual:Chapter5)
Most of the defaults are already set and will work if left unchanged, we will go through this process anyway.

1. You will be prompted to restore from backup or not, Choose "No";

2. Set the desired admin password to access the system, retype the password on the next screen to verify it;

![image](https://user-images.githubusercontent.com/94795740/211233345-da44a5eb-1f47-489a-a9e7-d3ecffce6511.png)

![image](https://user-images.githubusercontent.com/94795740/211233498-a12eac61-0496-4288-855b-72e54bbbde01.png)

3. Set the primary domain for the server, virtual domains can be added later, we used companywebsite.com in our example;

![image](https://user-images.githubusercontent.com/94795740/211235205-3b3bdd72-fc23-4ac5-940c-a5ab67b43935.png)

4. Set the unique system name of the server, information on naming schemes can be found [here](https://en.wikipedia.org/wiki/Computer_network_naming_scheme);

![image](https://user-images.githubusercontent.com/94795740/211236017-5947099d-3ca1-4c34-ac99-cb6d5172c276.png)

5.Select the Ethernet adapter that will connect your local network to the SME Server/Gateway,this connection will be wired to a switch where you can connect other physical machines to the network.

![image](https://user-images.githubusercontent.com/94795740/211177810-2be4fb39-ff0f-47ba-9792-fe0b2d4a9c04.png)

6.Indicate the IP address for the GREEN network and the subnet mask, the default setting will work unless you have reason to change them,

![image](https://user-images.githubusercontent.com/94795740/211236101-cd9110d5-9c30-49d4-b8a4-fce227dcc73c.png)

![image](https://user-images.githubusercontent.com/94795740/211236163-64048d8c-36c6-424d-841d-c59b90d7b02a.png)

7. Choose the operation mode, we want to use this as a SERVER AND GATEWAY and access the server from the outside (RED network), in our case we are connected to Fiber so we won't be selecting "dialup" as the external access mode.

![image](https://user-images.githubusercontent.com/94795740/211236198-2648fa8b-c67b-4241-8039-22a3da4840cc.png)

![image](https://user-images.githubusercontent.com/94795740/211236246-8dfe782a-d4ed-49c0-9085-b875333f2e5b.png)

8. Select the external network device of your installation target that connects to the external (RED) network.

9. Configure the External Interface to use a STATIC IP address that your ISP has provided you with, there is usually an extra fee for STATIC IP's, all consumer internet users have an IP that is DYNAMIC so you don't assume that what you have is a STATIC IP. The Gateway IP address will be the same as your static IP, except the suffix number will be 1.

![image](https://user-images.githubusercontent.com/94795740/211236296-e2728675-d983-4dda-8474-31343bdf0b3f.png)

![image](https://user-images.githubusercontent.com/94795740/211236490-8fd08561-c827-40d8-9288-0dfab26a014a.png)

![image](https://user-images.githubusercontent.com/94795740/211236659-4eaf8a1d-f8ff-435b-a3fd-347f02e1a0c1.png)

10. Configure the server to provide DHCP service to the GREEN network, we will select an IP range starting at 192.168.xx.200 - 192.168.xx.250
***NOTE*** we experianced what appears to be a glitch when setting the DHCP range that brings you back to previous steps in a type of "loop", If you leave the DHCP range as the default, it can be configured in the Server Manager later.

![image](https://user-images.githubusercontent.com/94795740/211236694-490be0e5-84e7-48f6-a44d-56a25b55a57a.png)

![image](https://user-images.githubusercontent.com/94795740/211236901-8a129e63-de24-40e6-9511-99a085d1ca41.png)

![image](https://user-images.githubusercontent.com/94795740/211237512-9b5f0169-df85-488b-a150-52fd4104217a.png)

11. DNS can be set in the final window, or it can be left blank if you're connected to the internet or don't need to specify a certain address.

![image](https://user-images.githubusercontent.com/94795740/211237548-be516994-6104-4a9d-8904-6ea15b47fc92.png)

![image](https://user-images.githubusercontent.com/94795740/211238684-cd6af846-82c2-4acd-a5b2-3a337924ec63.png)

12. After completing the initial configuration you can log into the Installation Target as "root" and use the password you created in Step 2 of this Part. Run the following command to install the DHCP Server Manager:
>yum --enablerepo=smecontribs install smeserver-dhcpmanager

Then press enter, then "y" when prompted. 

---

### Part 5 - SME Server Manager - CLI

We will use the Server Manager directly on our Installation Target, In later steps we will refer to the WUI Server Manager only. We've found that understanding how to use the Server Manager in the command line is useful when learning about SME server. First we should Reconfigure and reboot the system as we just installed DHCP Manager.
 
1. Log in as "admin" if you're still logged in as "root" just type "exit" and press enter. The admin password is the same, we are only switching users.

2. Select "Reboot ,reconfigure or shut down this server", option [4], and Reconfigure the Server. 

3. Log in as "admin" again, and access the Server Manager through the Server Console by selecting option [6].
![image](https://user-images.githubusercontent.com/94795740/211240498-06538b98-4974-49dd-ba3b-11e8d0670aed.png)

4. Take note that we are informed in the next window as to how to access the Server Manager from a WUI on any computer on our GREEN network;
![image](https://user-images.githubusercontent.com/94795740/211240596-777cba86-5ea5-4de7-9078-2711e14b311e.png)

5. Type "admin" as the user, then press the "down arrow" and type in your password again.
![image](https://user-images.githubusercontent.com/94795740/211240664-e561900b-1a61-4c81-95be-faab395bacb1.png)

### Part 6 - Setting Up Your First Device On The New Network.
We now have the Server/Gateway configured, our next step is to add our first machine, in this case we will use a Raspberry Pi 400 with Raspbian desktop installed.

1. Connect a network switch to the Ethernet adapter that serves up the GREEN network;

2. Connect the Pi 400 to the Switch and run "ifconfig" in a terminal window to check if DHCP is serving an 192.168.xx.200 IP to your Pi 400. (it's not in our case);

3. Return to the Server Manager from the previous steps on the Installation Target, highlight "DHCP Manager" and press "Tab"  and "Scan your network". If no device with 192.168.xx.200 is present, check your physical connections and/or restart your devices.

4. Return to the DHCP Manager and select "Show DHCP clients" our Pi 400 is now visible and we have access to the internet on the Pi 400. Any new devices plugged into the network will be handed out an appropriate address suffix in our DHCP range.

---

### Part 7 - System Backup to Network Attached Storage.

At this point, we don't have a lot of settings that we would need to restore quickly, but as we make changes to the system we want it to automatically store a backup to a trusted machine on the network.

1. Set a static IP address on the Network Attached Storage device, you're on you own here, check your machines documentation for how to complete this. We will be using 192.168.xx.yy where yy is a value between 2 and 10.

2. We created a new user and a new shared file on our NAS, this information must be set by you and used for the next steps so that SME server can access the backup location. Be sure to enable read/write functionality for the newly created user on that shared file.

3. Navigate to the Server Manager and click on "Backup or restore", then select "Configure workstation backup"; then "Perform".

![image](https://user-images.githubusercontent.com/94795740/212568229-0e6e4fd0-63dd-4d92-aba9-9f74c091c9a2.png)

4. We are using the Common Internet File System protocol for backing up our server.

![image](https://user-images.githubusercontent.com/94795740/212568271-f8993656-a291-4e91-ad4c-d8bc0e07ab99.png)

5. Enter the address of the machine, the log in credentials, and the name of the file the machine will back up to. Then set the back up date/time to a few minutes from now and confirm the backup is written to the shared folder. The backup will appear as a fully qualified domain name.

![image](https://user-images.githubusercontent.com/94795740/212568679-b5ccd0dd-8e41-432a-acfa-195d3c4203fb.png)

6. After the backup writes to the shared file on the NAS, you can go back to the restore option to check that the server is ready to restore from that backup.

![image](https://user-images.githubusercontent.com/94795740/212568672-c2d1201a-fd48-4a44-a572-1f924eed5e9f.png)

---

### Part 8 - Users

1. Users can be created in the "Collaboration" section under "Users", Click "Add user account" and begin creating your first user.

2. The **Account name** will be the prefix of this user's email on the primary domain, setting up email and virtual domains will be covered later. Example - name@yourcompany.com will be the primary email address associated with each user.

3. You can add each user to a group to organize access at group levels.

4. Create Groups by selecting "Groups" right below "Users".

### Part 9 - Email

Certain changes need to be made to the Domain Name System to set up email properly, in addtion to our STATIC IP address that the Internet servie Provider delivers, we will ne a Reverse DNS (PTR) record added to our STATIC IP. To check if your STATIC IP has this record, type the following into your Linux Command Line:
> dig -x <STA.TIC.IP.ADDRESS>

To do the same in Windows type the following into a command prompt:
>nslookup <STATICIPADDRESS>
or
>nslookup <YOURDOMAIN.WHATEVER>

The following entries are required on your DNS records to send and receive mail with your server:

1. A record - Looks like this -  A   mail   <STATICIPADDRESS>   1 hour
2. MX record - Looks like this - MX   @   mail.yourdomain.com  Priority 0  1 hour
3. TXT record - Looks like this - TXT   @   v=spf1 a:mail.yourdomain.com -all   1 hour
4. TXT record - Looks like this - TXT   -dmarc   v=DMARC1; p=quarantine; fo=1; rua=mailto:info@yourdomain.com; ruf=mailto:info@yourdomain.com; pct=100   1 hour

Learn a bit more about DNS for Mail servers [here](https://signaltransmitter.de/en/faq/domains/dns-records-fuer-mailserver).

Now you should be able to send a test email from yourdomain.com/webmail.

Log in and send an email to a gmail or yahoo account to test the spam filtering and your DNS record setup.

If you download [Delta Chat](https://delta.chat/en/) then you can use your email and your server to facilitate instant messaging with other Delta Chat users.

### Part 10 - Virtual Domains

Coming soon! 
