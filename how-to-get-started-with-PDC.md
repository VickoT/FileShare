# How to get started with PDC

**Author:** Viktor Törnblom

## Description
This step-by-step guide describes how to get started using PDC by:

* Creating a PDC account
* Connecting to PDC
* Syncing your home directory on Uppmax with the new one on PDC 

**Consider applying some of the tips in this [guide](https://github.com/theLabUpstairs/how-to-guide/blob/main/how-to-streamline-ssh-setup.md) when setting up the PDC SSH connection.**

## Guide

### Apply for a PDC account

1. Log in to your SUPR account at [https://supr.naiss.se/](https://supr.naiss.se/).
2. Go to the [Accounts page](https://supr.naiss.se/account/).
3. At the bottom of the page, there is a link to apply for a PDC account. Click it and follow the instructions. It may take up to 24 hours to receive the account.

### Connect to PDC

Unlike Uppmax, it is not possible to connect to PDC via SSH using a password, due to security reasons. Only SSH keypair authentication is permitted, which is managed through the [PDC Login Portal](https://loginportal.pdc.kth.se/overview).

1. Log in to the [PDC Login Portal](https://loginportal.pdc.kth.se/overview).
2. Click "Add new key".
3. Add the public SSH key generated on the machine from which you want to connect. The keys can be found by running `cat ~/.ssh/*.pub`. If new keys need to be generated, follow this [guide](https://github.com/theLabUpstairs/how-to-guide/blob/main/how-to-streamline-ssh-setup.md). Under "Key name", choose a name to identify the specific key, e.g., "Viktor's Mac".
4. Press "Save".

    **Note:** If you log in from a new network, you will have to add the new IP address.

5. You should now be able to SSH into PDC through your terminal, e.g.,

    ```ssh vtorn@dardel.pdc.kth.se```

    **Note:** your PDC username might differ from your Uppmax username. You can find your PDC username on your [SUPR](https://supr.naiss.se/) account page under "Accounts".

### Copy your home directory from Uppmax to PDC

To facilitate the transfer of files from Uppmax to PDC, use the tool [darsync](https://www.uppmax.uu.se/support/user-guides/move-to-dardel/darsync/), which generates a SLURM script to rsync a specified directory on Uppmax with a destination directory on PDC. Additional information is available in the [Migration to Dardel Guide](http://docs.uppmax.uu.se/cluster_guides/dardel_migration/).



1. SSH into your Uppmax account.

2. Load the darsync module: `module load darsync`.

3. Generate a temporary SSH keypair for connecting to PDC: `darsync sshkey`.

    * The keypairs will be located in the root of your home directory. 

4. Add the public SSH key to the [PDC Login Portal](https://loginportal.pdc.kth.se/overview) as described above. After the saving you need to modify the registered SHH key:

    - Below the registered key, click: "Add address".
    - Under "Or define one yourself" add the following: "*.uppmax.uu.se".
    - Click "Add custom address".

5. Inside Uppmax, 'cd' into the directory you want to rsync.

6. Run `darsync gen`
    
    After running this command, you will be prompted with a set of questions:

    **Specify local directory:** . (current directory)
    
    **Specify project id:** naiss2024-5-77 

    **Specify cluster:** \<ENTER>  (Rackham is default)

    **Specify Dardel username:** vtorn

    **Specify Dardel path:** /cfs/klemming/home/v/\<usr_name>/Private (find this path by running `pwd` inside your home directory on PDC)

    **Specify SSH key:** \<ENTER> (the ssh in the home directory will be used by default)

    **Specify SLURM script path:** \<ENTER> (will be located in the root of your home dir.)

7. Performe a test run on the login node to see if the script is working:

    `bash ~/dardel_transfer_script.sh`

    If it runs without any errors stop it (**ctrl+c**). If the size of the source directory is small, it is acceptable to run the script on the login node, and you can skip step 8.

8. Run the scrip using SLURM: `sbatch dardel_transfer_script.sh`
9. Check the log files, to make sure the transfer did not produce any error messages:
    ```
    less ~/dardel_naiss2024-23-9999.out
    less ~/dardel_naiss2024-23-9999.err
    ```
     And, SSH into PDC to check the files got transferred as wanted.
10. Remove the temporary SSH keypair: `rm ~/id_ed25519_pdc*`. And, also from the [PDC Login Portal](https://loginportal.pdc.kth.se/overview).
