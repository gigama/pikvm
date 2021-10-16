# First steps

## First power on

??? example "Optional setting up Wi-Fi"
    !!! warning
        There is nothing more reliable than wired Ethernet, so it's better to use the cable. But who are we to stop you... :)

    If you want to connect PiKVM to Wi-Fi network, you need to tell the device ESSID and password before first boot.
    To do this, mount the first partition of the memory card (FAT32) and edit the `pikvm.txt` file there.
    Do not remove line `FIRSTBOOT=1`, just add your wifi settings like this:

    ```
    FIRSTBOOT=1
    WIFI_ESSID="mynet"
    WIFI_PASSWD="p@s$$w0rd"
    ```

    There is a possibility that, in countries that support CH13, the Raspberry will not connect.
    You will need to configure your router to disable channels 12-14 or disable Auto scan mode so it will connect.
        
    Save, unmount and follow the next step.

**Power up the device.**

After turning on the power, PiKVM OS generates unique SSH keys and certificates. Do not turn off the Raspberry Pi until it's fully booted. If you set up Wi-Fi in step 1, it won't be able to connect to the network on the first boot. You need to wait 10 minutes for all the OS preparations to complete, then just restart the device. On the second boot, the Raspberry will connect to the network without any problems.


## Getting access to PiKVM

By default, PiKVM receives a dynamic IP address via DHCP.

??? example "Finding PiKVM in the network"
    To determine the IP address of your PiKVM, use one of the following methods:

    * **Common way:** Open the web interface of your router and find the list of issued IP addresses there. It depends on the router model.
    * **Linux-only:** Use the command `arp-scan --localnet`.
    * **Linux, MacOS, Windows:** Download and run [Angry IP Scanner](https://angryip.org).

For future examples, let's assume that your PiKVM has received the address **192.168.0.100**, which you have successfully detected using the instructions above. Then your device was assigned a hostname: **pikvm**.

??? example "Access to PiKVM Web Interface"
    In most networks you should be able to reach PiKVM via any browser with the URL `https://192.168.0.100/` or `https://pikvm/`. Google Chrome (Chromium), Firefox and Safari work best. Microsoft Edge and Internet Explorer are not supported.

    **The default user is `admin` and the password is also `admin`.** After logging in, you will get access to the menu with the main functions. Using the Web terminal, you can change system settings and passwords.

    The latest versions of Chrome do not allow access to the page with a self signed certificate, which is used in PiKVM by default. You can proceed by typing `thisisunsafe` and Chrome will then load the page.

??? example "Access to PiKVM via SSH"
    SSH is the most common remote access method in the Linux world. PiKVM is accessible via SSH. This method is used to manage the device:

    * **Linux, MacOS:** Open any terminal application and run: `ssh root@192.168.0.100` or `ssh root@pikvm`.
    * **Windows:** Use [PuTTY](https://www.putty.org/) for this.

    **The default `root` password is `root`.**

??? danger "✮ ✮ ✮ CHANGE THE PASSWORDS! ✮ ✮ ✮"
    PiKVM comes with the following default passwords:

    * **Linux admin** (SSH, etc.): user `root`, password `root`.
    * **PiKVM Web Interface**: user `admin`, password `admin`.

    **These are two separate entities with independent accounts.** To change passwords, you will need to use the terminal (read below) access via SSH or Web Terminal. If you are using the Web Terminal, use the `su -` command to get root access (enter the root user password).

    ```
    # rw
    # passwd root
    # kvmd-htpasswd set admin
    # ro
    ```


## Configuring PiKVM

Most of the PiKVM settings are done through configuration files. All configuration changes must be made from under the `root` user (that is, the administrator).

!!! tip "Obtaining root access"
    * If you have logged in via SSH, then most likely you are already `root`.
    * To get `root` in the Web Terminal, use command `su -` and enter the root password.

The PiKVM memory card is mounted in read-only mode. It protects the filesystem from damage in case of sudden power outage. To edit any files and make changes, it is necessary to remount the file system to the read-write mode.

!!! tip "Enabling write mode"
    * To enable write-mode, run command `rw` (under `root`).
    * To disable it, run command `ro`.
    * If you receive the message "Device is busy", perform `reboot`.

In this handbook, you will often find instructions for editing configuration files. The simplest and most beginner-friendly text editor is `nano`, but you can also use `vim`.

??? example "Editing files in the Web Terminal"
    ```
    $ su -
    # rw
    # nano /etc/kvmd/override.yaml
    # ro
    ```

-----
## Structure of configuration files

Most of the PiKVM configuration files are located in the `/etc/kvmd` directory.

The `/etc/kvmd/main.yaml` file defines the platform config and **you should never edit it**. To redefine system parameters use the file `/etc/kvmd/override.yaml`. All other files that are also not recommended for editing have read-only permissions.

In the `/etc/kvmd/meta.yaml` file you can specify some information regarding the host that this PiKVM manages.

!!! tip
    A complete list of all parameters can be viewed using the `kvmd -m` command.

Files with the suffix `*.yaml` uses the [YAML syntax](https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html)
and describes a parameter tree with key-value pairs of different types.
To define the parameters within one section, an indent of 4 spaces is used.
Comments starts with the `#` symbol.

!!! warning "Only 4 spaces should be used for indentation"
    Be careful when editing YAML and follow this rule.
    Invalid indentation or tabs instead of spaces will cause an error when starting the services.

??? example "Sections under the same keys should be merged"
    * **Wrong:**

        ```yaml
        kvmd:
            gpio:
                drivers: ...
        kvmd:
            gpio:
                scheme: ...
        ```

    * **Correct:**

        ```yaml
        kvmd:
            gpio:
                drivers: ...
                scheme: ...
        ```


-----
## What's next?
* Set up Internet access using [port forwarding](port_forwarding.md) or [Tailscale VPN](tailscale.md).
* Explore PiKVM features using the table of contents on the left.
* Join our [Discord](https://discord.gg/bpmXfz5) to contact the community and developers.
* Check out the [GitHub](https://github.com/pikvm) - PiKVM is a fully Open Source project!


-----
## FAQ and Troubleshooting
If you have any questions or run into problems, take a look at the [FAQ](faq.md).
Seriously, it's really useful! We've probably already found a solution for it :)

For any other help and support, you can contact us via the [Discord chat](https://discord.gg/bpmXfz5).