# Kerberos_SSH
This project is about configuring ssh service to use kerberos


Kerberos is a network authentication protocol used to verify the identity of two or more trusted hosts across an untrusted network



## Configure IP addresses

we used `hostname -I` know the IP addresses of our machine <br><br>
Then we configure them both in the machine client and kdc
![Screenshot (28)](https://user-images.githubusercontent.com/42326533/236062742-e4611548-df06-4026-9264-b5b25aaeae52.png)
<br> <br>





## Installing Kerberos
`sudo apt install krb5-kdc krb5-admin-server krb5-config` <br> <br>

### Set Realm <br> <br>
![Screenshot (4)](https://user-images.githubusercontent.com/42326533/236068718-769df5f2-8492-490f-9f83-6f7ee0f5cebb.png)
<br> <br>

### Set kerberos Server <br> <br>
![Screenshot (5)](https://user-images.githubusercontent.com/42326533/236069016-c598393d-1db9-4e8f-82bd-75c1245829f0.png)
<br> <br>

### Set Admin Server <br> <br>
![Screenshot (6)](https://user-images.githubusercontent.com/42326533/236069080-18c1a0af-de9b-4701-9595-915ccf9f75fd.png)
<br> <br>

### Done <br> <br>
![Screenshot (7)](https://user-images.githubusercontent.com/42326533/236069107-9d8d9baf-886d-4041-9c45-e72a8458fd68.png)
<br> <br>





## Configure Kerberos
<br> <br>

Initialization Base Realm with <br> <br>
`krb5_newrealm`
<br> <br>
![Screenshot (11)](https://user-images.githubusercontent.com/42326533/236070179-173186e7-e0fb-4f41-8181-42aaf0dd91a3.png)
<br> <br>
![Screenshot (12)](https://user-images.githubusercontent.com/42326533/236070861-b9d3cef2-035b-41d9-b871-feb8a91d0366.png)
<br> <br>

Change kadm5.acl <br> <br>  `sudo nano /etc/krb5kdc/kadm5.acl`  <br> <br> 
![Screenshot (13)](https://user-images.githubusercontent.com/42326533/236071050-b59aa275-5791-4600-ae2b-b9fff11417bd.png)
<br> <br>

Open Kadmin local<br> <br>
`kadmin.local`
<br> <br>
![Screenshot (17)](https://user-images.githubusercontent.com/42326533/236071303-0413865c-416e-41d8-914e-f88fbb0caf92.png)
<br> <br>

Add Principals (utilisateur , root) `addprinc [name of principal]` <br><br>
The Principal 'utilisateur' is required for you to order access to the service <br><br>
![Screenshot (18)](https://user-images.githubusercontent.com/42326533/236071526-952de6ee-9c41-4081-b198-1384866cfd80.png)
![Screenshot (19)](https://user-images.githubusercontent.com/42326533/236071536-d4ef72ee-f689-4add-ac6e-3f663c7ab8f3.png)
<br><br> 

Check keytab path <br><br> 
`sudo nano /etc/krb5kdc/kdc.conf` <br><br>
![Screenshot (10)](https://user-images.githubusercontent.com/42326533/236079929-38bfc0fb-858c-404c-aaf8-c261d7a46b24.png)
<br><br>

Add kadmin/admin principal to keytab <br><br> 
`ktadd -k /etc/krb5kdc/kadm5.keytab kadmin/admin`
<br><br>
![Screenshot (20)](https://user-images.githubusercontent.com/42326533/236081382-53513c7d-0c79-461a-937e-240a4a9c3950.png)
<br><br>

Add kadmin/changepw principal to keytab <br><br> 
`ktadd -k /etc/krb5kdc/kadm5.keytab kadmin/changepw`
<br><br>
![Screenshot (21)](https://user-images.githubusercontent.com/42326533/236082852-5e4f39f0-76fc-4bee-b597-29949bbff54f.png)
<br><br>

Add host principal ( host principal is used for exporting services ) <br> <br>
`addprinc -randkey host/kdc`<br><br>
![Screenshot (22)](https://user-images.githubusercontent.com/42326533/236083001-a9cf914e-7ee9-41b9-8282-b9307745e61f.png)
<br> <br>

Add keytab to host principal <br> <br>
`ktadd host/kdc`<br><br>
![Screenshot (23)](https://user-images.githubusercontent.com/42326533/236083080-289407a4-05ab-49fd-83f3-027af2268364.png)
<br> <br>

After finishing the kerberos configuration part, we have to restart the kerberos server by typing this command <br><br>
`sudo systemctl restart krb5-admin-server` <br><br>





## Configure openssh

OpenSSH is a suite of secure networking utilities based on the Secure Shell protocol, which provides a secure channel over an unsecured network in a client–server architecture

<br><br>

#Step 1
we start by checking if openssh service is installed or not  <br><br>
![Screenshot (14)](https://user-images.githubusercontent.com/42326533/236084054-db4a54b8-02c6-4a8f-96db-40812d5d082a.png)
<br><br>

#Step 2
now we are going to change sshd_config to allow GSSAPI <br><br>
`sudo nano /etc/ssh/sshd_config` <br><br>
you need to make sure that GSSAPIAuthentication and GSSAPICleanupCredentials are enabled <br><br> 
![Screenshot (15)](https://user-images.githubusercontent.com/42326533/236084591-420bb00e-a299-4e91-9c75-4ee66f47996e.png)

#Step 3
we do the same for ssh_config <br><br>
`sudo nano /etc/ssh/ssh_config` <br><br>
![Screenshot (16)](https://user-images.githubusercontent.com/42326533/236084681-c98ee84d-5cee-40ea-9c84-874977abe951.png)
<br><br>

Then we need to restart openssh service <br><br>
`sudo systemctl restart ssh`
<br><br>


## Kerberos Client

in client side we chould install the krb5-user <br><br>
`sudo apt install krb5-user` <br><br>
![Screenshot (29)](https://user-images.githubusercontent.com/42326533/236085177-d33410b9-bb6f-430e-85c2-6a4f85a4e317.png)
<br><br>
and then make sure that kerberos config are same in kdc and client <br><br>
![Screenshot (30)](https://user-images.githubusercontent.com/42326533/236085262-47a2fed0-4885-4c86-bc94-3112a47b186b.png)

## Test 

![Screenshot (31)](https://user-images.githubusercontent.com/42326533/236085309-1b27f65e-a0d0-486b-b348-2628f3a267ec.png)
