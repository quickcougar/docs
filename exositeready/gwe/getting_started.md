# Getting-started Guide: Install and Run ExositeReady™ Gateway Engine on a Gateway Device

In this guide, you will be able to quickly download ExositeReady™ Gateway Engine (GWE),
install it onto your gateway, and start using it. Once you have completed this section you will have GWE installed and running on a gateway. If you encounter any issues, please contact the dedicated GWE support team at [gwesupport@exosite.com](mailto:gwesupport@exosite.com).

# Requirements

Before beginning, you should first verify that your
gateway has the minimum requirements for running GWE:

* Linux OS
* Python 2.7.9+
* Network interface (`eth0`, `wlan0`, `ppp0`, etc.)
* GNU shell
* 128 MB Flash
* 64 MB RAM

**NOTE:** Some testing has been done on Python 2.7.3, but it has not been tested extensively and is not recommended.

# Getting Started 

## Create an Account

Sign up for a [Murano account](https://exosite.com/murano/). 

**NOTE:** When signing up for a new account, there will be emails you will need to
take action on in order to activate your account and log in.

## Create and Configure a Product
Once your account is set up, add a Product and name it (e.g. "gateway-engine", "my-product", etc). 

Once your account is set up, add a new product and name it (e.g., "Gateway Engine," "My Product," etc). 

Once you have a product, you must configure it with resources either using MrMurano (recommended) or manually.

### Use MrMurano

The [MrMurano tool](https://github.com/tadpol/MrMurano) is a command-line tool you can configure and use with your Murano solutions and products. 

#### Quick Configuration

Below are the minimum steps needed to configure MrMurano for using GWE.

##### Configure User Name

This is the user name or email address with which you use to login to Murano.

```
mr config user.name USER_NAME
```

##### Select and Configure your Business Account

If it's your first time logging into your Murano account with MrMurano on the command-line, you will be prompted for your password. Once you've successfully logged in, the following command will show you a listing of all of the business accounts you have access to. 

```
mr account --business
```

**NOTE**: The `mr config` dialog stores configuration entries in `.mrmuranorc` files but treats passwords differently. The first time you log in to an account and are prompted for your password, MrMurano stashes the password in the file `~/.mrmurano/passwords`. 

If you do not want this stored in a local file, run `rm ~/.mrmurano/passwords` after each session.

##### Configure Product ID

```
mr config product.id XXXXXXXXXXX
```

Once you configure MrMurano, you can use the following
commands to create the required GWE resources to the Murano
device. 

##### Tell MrMurano Where to Find the Spec File

The default location MrMurano uses is `$PWD/specs`. This can be overridden with the following command:

```
mr config business.id XXXXXXXXXXX
```

##### Configure Product ID

You can find the Product ID (often referred to in this context as the **PID**) in the Murano web UI under the "INFO" tab. Use this Product ID in the command, below:

```
mr config product.id XXXXXXXXXXX
```

### Manually

The table below shows the resources you must add, what to name them,
and what format to choose for them.

| Alias         | Format        | Description   |
| ------------- | ------------- | ------------- |
| usage_report  | string        | GWE sends a report of all processes using its resources (`gmq`, `device-client`) to send data to Murano. This report contains information about how much network bandwidth is being consumed as well as other metadata about network requests.|
| engine_report | string | GWE reports information about what applications are installed and other metadata like uptime, exit codes, and versions.         |
| device_info | string | GWE reports filesystem and OS data like OS and kernel version as well as free memory and disk space usage.|
| engine_fetch | string | GWE regularly checks this dataport for formatted messages containing instructions on new apps and updates to install.        |
| fetch_status | string | Once an app is installed over-the-air or an update to an app is executed, GWE reports the STDOUT and STDERR from the app installer. This dataport is also used for uncaught exception logging.       |
| update_interval | string | This value, in seconds, is the delay between each series of GWE reports and OTAU checkins.|

## Add a Device

Copy down the MAC address of your gateway and use it as the serial number for your new Murano GWE device. 

Add a device to your Murano Product with the MAC address of your gateway and name it something relevant (e.g., "My Getting Started Gateway").

## Download, Install, and Configure GWE

To download the latest version of the Public Release of GWE,
follow these steps:

1.  Navigate to the [GWE Release Packages](/exositeready/gwe/release_packages/) page and follow the instructions to
    download GWE.
2.  Run these commands to copy GWE to your gateway (the
    actual filename in the command may differ):

    ```
    ssh <USER>@<GATEWAY_IP> "mkdir /opt"
    scp GatewayEngine.v1-1-2.tar.gz <USER>@<GATEWAY_IP>:/opt 
    ```

    At this point, you have downloaded the latest release of GWE and copied it to your gateway.

3.  Run this command to untar the release package and install GWE onto your gateway:

    ```
    ssh <USER>@<GATEWAY_IP> "cd /opt
      tar zxvf GatewayEngine.v1-1-2.tar.gz
      cd gateway-engine
      ./install.sh"
    ```

    **NOTE:** In some Linux environments, you will need to use Super-User permissions to run the installer. In this case, replace the `./install.sh` command to:

    ```
    sudo ./install.sh
    ```

4.  Once the installation is complete, you will need to configure GWE for your IoT solution and Exosite account. This will require
    one piece of information from your Murano account and the serial 
    number of your gateway.

    1.  In your Murano account, navigate to your product and click on
        the *INFO* tab. Copy the Product ID and use it in the commands
        below in place of "&lt;PRODUCT\_ID&gt;".
    2.  Determine the serial number of your gateway. GWE is
        programmed to retrieve the MAC address from the Internet
        interface of your choosing (e.g., `eth0`, `wlan0`,
        `ppp0`, etc.) when the `--set-iface` command-line switch
        is used. Or you can just specify any serial number you want
        with the `--set-uuid` command-line switch.
   
    3.  Once you have gathered this information and determined what serial
        number to use for your gateway (interface MAC address or custom
        serial number), run the following command to configure GWE:

    ``` 
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id <PRODUCT_ID> --set-iface <THE_INTERFACE>""
    ```

    **Example:**

    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id dubhxzv0r4e1m7vj --set-iface eth0"``
    ```
    
 Or if you want to just specify your own serial number:
 
    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id <PRODUCT_ID> --set-uuid <THE_SERIAL_NUMBER>""
    ```
        
    **Example:**

    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id dubhxzv0r4e1m7vj --set-uuid 12345"
    ```
    
5.  To complete the installation, you will need to reboot the gateway.
    To reboot, you can toggle the power or use the following command:

  ```
  ssh <USER>@<GATEWAY_IP> "reboot"
  ```
  
**NOTE:** GWE uses `supervisord` to start itself on boot, and once it starts, it will start GWE as well as all other installed custom gateway applications.

## Verify

Watch for new data in the GWE device on your product device.

Once the reboot has completed, you will notice that `supervisord` and
`gwe` processes are running in the output of the `ps -ef` command. Some
gateways have a lot of processes and the `ps -ef` command can be too much
to read through. If this is the case, filter the output with `grep`
(e.g., `ps -ef | grep 'super\|gwe\|gmq'`). You can also use the
`supervisorctl status` command to view the status of the GWE
applications.

```
ssh <USER>@<GATEWAY_IP> "supervisorctl status"
gmq                           RUNNING    pid 621, 00:01:38
gwe                           RUNNING    pid 620, 00:01:38
```

A few seconds after rebooting the gateway you should see data appear in
the aliases of your GWE device.

For additional functionality of Exosite products available on your gateway, take a look at
the output of the following commands:

### The Device Client cli

```
gdc --help
```

### The GWE cli

```
gwe --help
```

### The Gateway Message Queue cli

```
gmq --help
```

# Summary

The steps in this guide were designed to get you moving as quickly as
possible with GWE and Exosite. If you have questions,
concerns, or suggestions on how to make this documentation better, please
contact [gwesupport@exosite.com](mailto:gwesupport@exosite.com) with a detailed message. Thank you.
