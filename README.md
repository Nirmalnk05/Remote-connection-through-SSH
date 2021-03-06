# How to use SSH with Vertex notebooks

### 1. Find the Vertex AI - User Managed notebook instance IP address

  Step  1. In the GCP Web Console, go to:
            Vertex AI > Workbench > User Managed tab > click instance name > View VM Details

  Step  2. Find the ip address of the AI Platform Notebook VM that is supporting it, write it down, copy it / remember (smile)

          ### Cross Check

          Verify the Notebook instance is running the SSH daemon
          At time of writing, most notebook instances run a Debian variant of Linux for the OS.   
          You may encounter a notebook type that does not run the SSH daemon by default.   You can check if your notebook is running SSH with the following.  
          If it is you should see similar output as below

          Cmd 1:  $ ps aux | grep -i sshd

          (base) jupyter@python-20210215:~$ ps aux | grep -i sshd
          root 526 0.0 0.1 15852 6684 ? Ss Feb15 0:00 /usr/sbin/sshd -D

          Cmd 2: $ sudo netstat -plant | grep :22

          tcp 0 0 0.0.0.0:22 0.0.0.0:* LISTEN 526/sshd
          tcp 0 0 10.13.80.145:22 10.120.246.216:50936 ESTABLISHED 4397/sshd: jupyter
          tcp 0 0 10.13.80.145:22 10.120.246.216:50937 ESTABLISHED 4407/sshd: jupyter



### 2. Create SSH keys
SSH keys are just a pair of files that contain one public and one private 'key' that allow two computers to establish an identity trusted to talk to each other.  
You can create ssh key pairs on almost any Linux OS or on Windows using a git bash terminal session or a WSL session as follows.   
Entering a passphrase is optional, so you can just hit return to not enter a passphrase or provide a passphrase that you need to store somewhere securely.

Use the following command in a the Notebook instance within a terminal window.   (or another machine with ssh-keygen available)

Step 1: Go to your jupyterlab and launch a terminal in remote machine. (Here GCP Vertex AI)
Step 2: Generate the key pair.Note! passphrase can be empty 
        if ssh-keygen command not found, it can be installed by "apt-get install openssh-client"


        $ ssh-keygen -f gcp-notebook
        Generating public/private rsa key pair.
        Enter passphrase (empty for no passphrase):     
        Enter same passphrase again:
        Your identification has been saved in gcp-notebook
        Your public key has been saved in gcp-notebook.pub
        The key fingerprint is:
        SHA256:jTyJQt+cfMbTWVsrKAg4inncJ4uax/IoEmWdB36fqJU yourusername@yourcomputername
        The key's randomart image is:
        +---[RSA 3072]----+
        | |
        | o |
        | =.+ . .|
        |.o+o=.+*.* ..o o.|
        |o+o +o+=So*.o... |
        |.. . =E o+.. . |
        | o. .o |
        |++o . |
        |*+. |
        +----[SHA256]-----+
        
Step 3: Configure AI Platform Notebook instance to accept SSH connections using a public key
        Once you have created the key pair files, you will need to place the 'public' key file content  (*.pub) into a file on the machine you want to connect to 
        (in this use case, the Notebook VM instance).  If you create the keys on the Notebook instance itself, using a terminal, you can do the following.

        $cd /home/jupyter/.ssh/
        $cat gcp-notebook.pub > /home/jupyter/.ssh/authorized_keys
        
        Notte : Make sure the .ssh folder permission is 755 and 644 for authorized_keys file

      Example: If you create the keys elsewhere, copy the content of the *.pub file and paste it into the file at /home/jupyter/.ssh/authorized_keys using an editor.  (nano, vi ).  
      If you do copy/paste, get the full text like the example below:

      ssh-rsa 
      AAAAB3NzaC1yc2EZZZZZZZDAQABAAABgQCurnxEKMmM7HF07RbN+AJn9GasVyAbuaLvC5TS6glA4VVEen13ihsaEnNznowIwj0mfY+hdmDRWK5SKZ+v5wJuPl3IAowyhn8fg3aAEo6NLVWg4LJVF+
      b9BPGxLXHYXJYmlXA4gTjlCi7UrObqqjjvfgfbdIMxmFnpHE6u1gDGcA0MMMMMKauJbGCnN5NmZJpqAjqzWJOU812VUAPE4FFZwH6PZCJGmlHL0vYZ6+2GDYiMzAkDtBgFAVAGDM5rpw6+dxDvmQ5+
      p2fKzk0EaINj5CJ76H9ZyGbRgJyHJFCdiMPy0g8/NDj0hZOEio8V/nrogCuACuFd835BpnWExCQ+38PLATEQAsV+rqs0wr27il7IpozU9xSyFI7ypR4Y61kOU812IKa21OVyoggat2v/eW7kZ2ijy7z
      QJSxbgiQa5n8haKfLOzu2BHvB78JnvokXUKBOc9eI4oprkL06crqcYTAs0SGWCiOYacOr5swezytqTv3SCGFIXkGdIxg4iU= yourusername@yourcomputername


### 3. Using a private key for SSH clients on Windows (putty, moba, etc)
    Download/copy the private key file from the Notebook instance (you can use the Notebook browser, right click file and download). 
    If you created the key locally, recommend to copy the private key file to  c:\users\username\.ssh\<private-key-file-name>

    Appraoch 1 : Moba Xterm
            Step 1 : Create a new session-> SSH
            Step 2 : Set remote host to IP address of your AI Platform Notebook instance 
            Step 3 : Check specify username, supply jupyter as value
            Step 4: Check use private key option
            Step 5: Supply location (browse button) to ssh private key created above. Click ok (you should connect)
            
    Approach 2 : Using Putty
    
    Putty is more finicky than Moba xtern.  If you created an SSH2 key file on the notebook instance, or another computer, you will need to convert it to Putty's key file format using PuttyGen.  
    If you do not have Putty, you can install both Putty & PuttyGen.
    
    Step 1: Start PuttyGen
            Click Load, select private key file from above setup
            Click Save Private Key, save to c:\user\username\.ssh as a *.ppk file
    Step 2: Under Session
            1. Set hostname to jupyter@<your-notebook-ip>
            2. Set saved session to something you like (notebook name is prefered)
            3. Go to:  Connection > SSH > Auth
               click browse button, select *.ppk file you just created with PuttyGen
               Go back to session, click save
            4. Click Open, you should connect
            
 
 ### 4. Setting SSH Config in local machine
   Create a file named "Config" under .ssh directory (Path :  c:\users\<username>\.ssh)
        Host <your-nickname>
        User jupyter
        HostName <your notebookinstance ip>
        IdentityFile c:\\users\\<username>\\.ssh\\<name-of-private-key-file>
        IdentitiesOnly yes

      Example:
        Host 10.13.81.7
        User jupyter
        Hostname 10.13.81.7
        IdentityFile C:\Users\nnarayanaswa\.ssh\gcp-notebook
        IdentitiesOnly yes 

 
  
 # Using Visual Studio Code for Remote Development
  
 #### Pre-Requisites
      Before you attempt this, you should:
      1. Install Python 
      2. Setup a GCP Vertex AI Notebook
      3. Install a SSH client compatible with VS Code 
      4. Git for Windows
      5. Windows OpenSSH Client
      6. Create SSH keys to enable Notebook remote SSH login
      7. Install Visual Studio Code
      8. Install the VS code Remote Development extension pack
      9. Install the VS code Jupyter extentsion pack
  
  
### 1. Connect VS Code using SSH
   Verify SSH works (cmd prompt, ps terminal,etc)
   You may need to make sure you have an id_rsa & id_rsa.pub file in c:\users\username\.ssh directory as well as 
   have installed the id_rsa.pub file content in the notebooks /home/jupyter/.ssh/authorized_keys file.  

  Step 1 : Find your ssh client, make sure it is on the path
           Cmd 1: where ssh
  
  Step 2 : Test SSH connectivity with default client
           Cmd 2: ssh username@<ip-address> (jupyter@10.13.81.7)
  
  Step 3: Test VS Code connect to Notebook instance
          1. Open the command pallette (f1)
          2. Choose Remote SSH, Connect to Host
          3. Choose add new host
          4. Enter ssh connection string: (ex: ssh jupyter@my-notebook-ip-addr)
             If prompted for ssh config file to edit, choose c:\users\username\.ssh\config
             If prompted for OS type (Linux, etc) Select Linux

          A new window should open indicating ssh connection in lower left IDE corner
  
  Step 4:   Everything done. 
            Nope.  We need the Jupyter plug in.  But we have it already... Right?
            Nope. you need to install it in the 'remote' VS Code Server process running
            Click the cloud (install to remote) and search for Jupyter
            You may get options for python, pylance and jupyter, select all
            wait for installs to complete. (they should appear in remote installed left tool pane)
  
  References:
  https://code.visualstudio.com/docs/datascience/jupyter-notebooks#_connect-to-a-remote-jupyter-server 
  https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client
