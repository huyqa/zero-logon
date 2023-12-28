# CVE-2020-1472 - Zero-Logon POC

![alt text](https://github.com/harshil-shah004/zerologon-CVE-2020-1472/blob/master/PoC%20-%20CVE-2020-1472.png)

This exploit requires you to use the latest impacket from [GitHub](https://github.com/SecureAuthCorp/impacket).

Ensure impacket installation is done with netlogon structures added.

Note : 

1. By default, successful exploitation changes the password of the DC Account. 

2. Allows DCSync

3. Breaks communication with other domain controllers (Be careful!)

Original Research & information [here](https://www.secura.com/blog/zero-logon)

## Installation

Requires Python 3.7 or higher and Pip. Install dependencies as follows:

    pip install -r requirements.txt
<p align='center'>
  <img src='./Screenshots/install-req.png' alt="HTB">
</p>
    
## Before Zerologon 
<p align='center'>
  <img src='./Screenshots/before-zerologon.png alt="HTB">
</p>
Unable to dump secrets!

## Performing Zerologon Attack!
<p align='center'>
  <img src='./Screenshots/Screenshot from 2023-12-28 15-35-49.png' alt="HTB">
</p>

## After Zerologon
<p align='center'>
  <img src='./Screenshots/after-zerologon1.png' alt="HTB">
</p>
Successfully dumped NTDS.dit secrets!

## Exploit Steps
- Read the related articles/blogs/whitepapers to know the objective of the exploit
- Run `python3 zero-logon-exploit.py` with IP and netbios name of DC
- DCSync with secretsdump, using `-just-dc` and `-no-pass` or empty hashes and the `DCHOSTNAME$` account


## Restore steps
If you make sure that [this line](https://github.com/SecureAuthCorp/impacket/blob/64ce46580286b5ab15a4737bddf85201ce2adde3/impacket/examples/secretsdump.py#L1530) in secretsdump passes (so make it `if True:` for example) secretsdump will also dump the plaintext (hex encoded) machine account password from the registry. You can do this by running it against the same DC and using a DA account.

Alternatively you can dump this same password by first extracting the registry hives and then running secretsdump offline (it will then always print the plaintext key because it can't calculate the Kerberos hashes, this saves you modifying the library).

With this password you can run `restorepassword.py` with the `-hexpass` parameter. This will first authenticate with the empty password to the same DC and then set the password back to the original one. Make sure you supply the netbios name and IP again as target, so for example:

```
python restorepassword.py testsegment/s2016dc@s2016dc -target-ip 192.168.222.113 -hexpass e6ad4c4f64e71cf8c8020aa44bbd70ee711b8dce2adecd7e0d7fd1d76d70a848c987450c5be97b230bd144f3c3...etc
```

Credits for Exploit Provision : [dirkjanm](https://github.com/dirkjanm) 
