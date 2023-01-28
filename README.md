# Memory-Forensics---TryHackMe

## Task 2 - Login ##
### What is John's password? ###
In order to retrieve the password we need to figure out what operating system the memory dump is running on. This can be done by utilising the ```imageinfo``` command ```vol-f snapshot6.vmem imageinfo``` will present us with this. 
![image](https://user-images.githubusercontent.com/18509521/215230547-ac3ae353-c2bb-45a0-97d8-cd9efc879b49.png)
From here we will use the Win7SP1x64 profile. Now, where are passwords stored on a windows box? The password hases are stored in the HKEY_LOCAL_MACHINE\SAM registry, to get the a list of the registries on the system we will ust the ```hivelist``` plugin. Once we run that we will get the outpur we need.

![image](https://user-images.githubusercontent.com/18509521/215231255-bdcbe9d9-62d9-4ccb-b8fd-6f9a3415bf21.png)
So in order to extract the hashes from the ram we will need the SAM virtual offset and the system virtual offset. This can be done with the command ```hashdump``` followed by ```-y``` to specify the SYSTEM virtual address and then ```-s``` to specify the SAM virtual adddress.
![image](https://user-images.githubusercontent.com/18509521/215231931-f157df04-a397-4d4a-bea1-80266bfd0a97.png)
![image](https://user-images.githubusercontent.com/18509521/215231947-5b0277c5-8c74-41f6-9d0f-eea90226997f.png)
Now because we only got the hashes we need to crack the passwords, we can do this using hashcat or john. For this one I will be using hashcat.
![image](https://user-images.githubusercontent.com/18509521/215232384-84645cc4-4dd2-4992-8f64-08296468848f.png)
![image](https://user-images.githubusercontent.com/18509521/215232366-ad9a12e6-f88f-4ed1-815b-cb1a7ce801ac.png)

After hashcat has been run we can see we got the first flag for john's password which was ```charmander999```

## Task 3 - Analysis ##
### When Was the machine last shutdown? ###
In order to find the last shutdown we can search through the registry keys and find the last shutdown information in there. The shutdown time is stored in this registry ```HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Windows``` Because we only need the key values we can use the ```printkey``` plugin to find what we need.
![image](https://user-images.githubusercontent.com/18509521/215235684-755bdffa-6773-41bc-a29c-9c1c6e568b87.png)
The search returned nothing let let's start at CurrentControlSet and go key by key.
![image](https://user-images.githubusercontent.com/18509521/215235898-80b5329b-12db-4b1d-8708-8633dd7ab192.png)
As we can see here the CurrentControlSet is a symbol link which points to ControlSet001. This will always point to the controlset that is currently loaded so if we change our key to ControlSet001 we should find out information.
![image](https://user-images.githubusercontent.com/18509521/215236224-21d3a25f-ca1f-4464-86d8-a16d122bd501.png)
And here is the Windows key and as we can see there is a ShutdownTime subkey. However, I looked at the last upaded time frame and put that in which was our flag for this question ```2020-12-27 22:50:12```
### Alternative Solution ###
Just use the shutdowntime plugin

![image](https://user-images.githubusercontent.com/18509521/215236359-71784646-413e-4927-81c4-e65e5c346f3d.png)

### What did John write? ###
For this question our best bet would be to scan the cmdline for anything a user has written in there. We can use either the ```cmdline``` ```cmdscan``` or ```clipboard``` plugins. cmdscan returned with nothing of use however the other two have what we are looking for. 
![image](https://user-images.githubusercontent.com/18509521/215237371-e79f81b9-e2c8-49c1-9597-8ad0b0ae04da.png)

The flag is ```You_found_me```

## Task 4 - TrueCrypt ##
### What is the TrueCrypt passphrase? ###
For this question we can simply run the pluing ```truecryptpassphrase``` to get the information we need.
![image](https://user-images.githubusercontent.com/18509521/215237540-ba119eec-7c64-4f0e-aad9-99f5868491cf.png)

The passphrase is ```forgetmenot```



