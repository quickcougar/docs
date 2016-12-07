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

This getting started guide will take you through the various steps it takes to start using Murano and GWE such as creating and configuring a Murano account, downloading GWE, configuring tools and installing GWE onto a gateway.

## Download and Unpack Gateway Engine

To download the latest version of the Public Release of GWE, navigate to the [GWE Release Packages](/exositeready/gwe/release_packages/) page and choose a method for downloading GWE. Once downloaded, copy/move it to a directory on the filesystem of your development machine so its resources can be referenced later on.

```
mkdir ~/code
cp ~/Downloads/GatewayEngine.v*.tar.gz ~/code
```

The GWE download contains useful resources and tools that we use outside the context of a gateway. To make these resources available on your development machine, decompress the tarball:

```
cd ~/code
tar zxvf GatewayEngine.v*.tar.gz
```

You will be doing more with this download and the newly unpacked directory `~/code/gateway-engine`, but for now you can move on to the next step.

## Create an Account

<a href="https://exosite.com/signup/" target="_blank">Sign up for a Murano Account.</a>

**NOTE:** When signing up for a new account, there will be emails you will need to
take action on in order to activate your account and log in.

## Create and Configure a Product
Once your account is set up, add a Product and name it (e.g. "gateway-engine", "my-product", etc). 

Once you have a product, you must configure it with resources either using **MrMurano** (*recommended*) or manually by using the Murano web UI.

### Configure a Product Using MrMurano

The [MrMurano tool](https://github.com/tadpol/MrMurano) is a command-line tool you can configure and use with your Murano solutions and products. 

#### Quick Configuration

Below are the some of the steps needed to configure MrMurano for using GWE. There will be more configuration commands for MrMurano, but information for these commands is not yet available at this step of our Getting Started guide.

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

**NOTE**: The `mr config` dialog stores configuration entries in `.mrmuranorc` files but treats passwords differently. The first time you log in to an account and are prompted for your password, MrMurano stashes the password in the file `~/.mrmurano/passwords`. If you do not want this stored in a local file, run `rm ~/.mrmurano/passwords` after each session.

Once you have logged in, choose the right **Biz ID** and use it in the command, below in place of "&lt;BUSINESS\_ID&gt;":

```
mr config business.id <BUSINESS_ID>
```

##### Configure Product ID

In your Murano account, navigate to your product and click on the *INFO* tab. Copy the Product ID (sometimes referred to in this context as the **PID**) and use it in the commands below in place of "&lt;PRODUCT\_ID&gt;".

```
mr config product.id <PRODUCT_ID>
```

You can also find the Product ID using the following MrMurano command:

```
mr product list
```

##### Tell MrMurano Where to Find the Spec File

A "spec" file is a YAML file that describes the resources of a Murano Product. The GWE spec file is available in the release tarball and, now that you've unpacked it on your development machine, can be found at `~/code/gateway-engine/specs`. The contents of the file are shown, below.

    $ cd ~/code/gateway-engine
    $ cat specs/gwe.spec
    ---
    resources:
    - alias: usage_report
      format: string
    - alias: engine_report
      format: string
    - alias: engine_fetch
      format: string
    - alias: device_info
      format: string
    - alias: update_interval
      format: string
    - alias: fetch_status
      format: string


The default location MrMurano uses for spec files is `$PWD/specs`. This can be overridden with the following command:

```
mr config location.specs relative/path/to/folder/containing/spec.file
```

  **Example:**

  ```
  cd ~/code/gateway-engine
  mr config location.specs specs
  ```

Verify the path with `mr config --dump`.

##### Tell MrMurano Which Spec File to Use

```
mr config product.spec gwe.spec
```

#### Create the Resources

```
mr syncup -V --specs
```

### Configure a Product Manually

If you haven't configured MrMurano, you can create the GWE resources manually using the Murano web UI. 

The table, below, shows the resources you must add to the Product Definition, what to name them and what format to choose for them.

| Alias         | Format        | Description   |
| ------------- | ------------- | ------------- |
| usage_report  | string        | Gateway Engine sends a report of all processes using its resources (`gmq`, `device-client`) to send data to Murano. This report contains information about how much network bandwidth is being consumed as well as other meta data about network requests.|
| engine_report | string | Gateway Engine reports information about what applications are installed and other meta data like uptime, exit codes, and versions.         |
| device_info | string | Gateway Engine reports filesystem and OS data like OS and kernel version as well as free memory and disk space usage.|
| engine_fetch | string | Gateway Engine regularly checks this dataport for formatted messages containing instructions on new apps and updates to install.        |
| fetch_status | string | Once an app is installed over-the-air or an update to an app is executed, Gateway Engine reports the STDOUT and STDERR from the app installer. This dataport is also used for uncaught exception logging.       |
| update_interval | string | This value, in seconds, is the delay between each series of Gateway Engine reports and OTAU checkins.|

Once you have finished configuring your Product, you can confirm it has the correct definition either by using the Murano web UI or with MrMurano:

```
mr product spec pull
```

## Install GWE Onto Your Gateway

Run the following commands from your development machine to copy GWE to your gateway:

    ```
    ssh <USER>@<GATEWAY_IP> "mkdir /opt"
    scp ~/code/GatewayEngine.v*.tar.gz <USER>@<GATEWAY_IP>:/opt 
    ```

At this point, you have downloaded the latest release of GWE and copied it to your gateway.

Run the following command to untar the release package and install GWE onto your gateway:

    ```
    ssh <USER>@<GATEWAY_IP> "cd /opt
      tar zxvf GatewayEngine.v*.tar.gz
      cd gateway-engine
      ./install.sh"
    ```

    **NOTE:** In some Linux environments, you will need to use Super-User permissions to run the installer. If the type of Linux you're using has both `root` and non-`root` users, then in order to use GWE, you'll likely need to install GWE as `root`. In this case, replace the `./install.sh` from the command, above with `sudo ./install.sh`. For example:

        ```
        ssh <USER>@<GATEWAY_IP> "cd /opt
          tar zxvf GatewayEngine.v*.tar.gz
          cd gateway-engine
          sudo ./install.sh"
        ```

### The Installation Log File

The installation script generates a lot of logging output. In addition to showing the log output on the console's STDOUT, it is captured to a file named `gwe_install_${DATE_TIME}.log` in the `gateway-engine` directory. Take note of the Installation Summary and Self Test at the end of the log. If you see any test failures or exceptions, please visit [the Gateway Engine forum](https://community.exosite.com/c/GWE) and create a new topic with information about your experience.

## Configure the GWE Serial Number On Your Gateway

Once the installation is complete, you will need to configure GWE for your IoT solution and Exosite account. This will require the PID (*Product ID*) from your Murano account and the serial number of your gateway.

You can determine the serial number of your gateway in two ways:

  * The MAC address
  * Any Other Serial Number

### Use the MAC Address of an Internet Interface

GWE can be configured to use the MAC address of the internet interface of your choosing (e.g., `eth0`, `wlan0`, `ppp0`, etc.). Using the MAC address of a piece of hardware like an ethernet port, a wifi port or a cellular modem is a great option for deconflicting serial numbers and offers a standard way of identifying hardware between customers, vendors, manufacturers and most others in the chain of IoT goods and services.

When configured this way, GWE uses the MAC address of the internet interface (i.e. iface) you specify with ALL CAPS and ':' formatted. To view a list of the available internet interfaces on your gateway, use the `ifconfig` command.

        **Example**: 

        The example, below, shows the command for configuring GWE to use the MAC address of iface `eth0` as its serial number (a.k.a. *uuid*). After configuring GWE, it prints the new configuration to the console.

            ```
            $ gwe --set-iface eth0 -d DEBUG
            Found interface 'eth0' with MAC address (serial/uuid): FF:10:C2:9B:A8:46
            [device]
            cik = ''
            model = ''
            vendor = ''
            uuid = ''
            iface = eth0
            ```

        To see what the MAC address of any interface is, you can use the `gwe --mac-address IFACE` command.

### Use a Your Own Serial Number

Depending on the needs of you IoT solution, you may need to specify your own serial number. To do this, use the `--set-uuid UUID` command-line switch.

        **Example**: 

        The example, below, shows the command for configuring GWE to use the MAC address of iface `eth0` as its serial number (a.k.a. *uuid*). After configuring GWE, it prints the new configuration to the console.

            ```
            $ gwe --set-uuid 12345
            [device]
            cik = ''
            model = ''
            vendor = ''
            uuid = 12345
            iface = eth0
            ```

If you configure GWE with a uuid, it will override any iface configuration and the MAC address will not be used. This is true even if you change the iface configuration, so the only way GWE will use a MAC address again as its serial number is if you clear, or re-initialize, the uuid configuration to "''".

You can use the following command to clear the uuid, or any other, configuration to start over. 

    ```
    gwe --set-uuid \'\'
    ```

## Configure the GWE Product ID On Your Gateway

Using the PID from a previous step, use the following command to configure GWE for your Murano account:

``` 
gwe --set-product-id <PRODUCT_ID>
```

Due to legacy reasons, GWE uses the PRODUCT_ID as both the *vendor* and *model* entries in the GWE configuration. See the example below:

    **Example:**
    ```
    $ gwe --set-product-id dubhxzv0r4e1m7vj
    [device]
    cik = ''
    model = dubhxzv0r4e1m7vj
    vendor = dubhxzv0r4e1m7vj
    uuid = 12345
    iface = eth0
    ```


## Add a Device

Navigate to the Products section of the Murano web UI, select the GWE product (*whatever you named it*) and click on the DEVICES tab. When you click the **+ NEW DEVICE** button, use the serial number GWE is configured for as the serial number for your new Murano GWE device and name it.

You will see this new device appear in the list of available devices in the GWE Product of your Murano account. If you are following this Getting Started guide in order, the new device should be showing a STATUS of `notactivated`.


## Reboot

To complete the installation, you will need to reboot the gateway. 

The reason we deem the installation incomplete until a gateway reboot is because it is the last step in determining whether or not GWE will start-on-boot. If after rebooting the gateway GWE is not running, then something went wrong with the installation (*if this happens please visit [the community forum](https://community.exosite.com/c/GWE)*). 

To reboot, you can toggle the power or use the following command:

    ```
    reboot
    ```
  
**NOTE:** GWE uses `supervisord` to start itself on boot, and once it starts, it will start GWE as well as all other installed default and custom gateway applications.

## Verify

Watch for new data in your new Murano GWE device.

Once the reboot has completed, you will notice that `supervisord` and `gwe` processes are running in the output of the `ps -ef` command. Sometimes there are alot of processes and the `ps -ef` command can be too much to read through. You can filter the output with `grep` (e.g., `ps -ef | grep 'super\|gwe\|gmq'`). You can also use the `supervisorctl status` command to view the status of the GWE applications.

```
supervisorctl status
gmq                           RUNNING    pid 621, 00:01:38
gwe                           RUNNING    pid 620, 00:01:38
```

A few seconds after rebooting the gateway you should see two things change on the GWE device in your Murano account.

  * The device's STATUS should be showing `activated`.
  * Data appear in the aliases/resources of your GWE device.

You can also check to see if the gateway has successfully activated by checking for the existence of a CIK in the GWE configuration with the following command:

    ```
    gwe --gateway-cik
    ```

    **Example**: A non-activated GWE.

        ```
        $ gwe --gateway-cik
        ''
        ```

    **Example**: An activated GWE.

        ```
        $ gwe --gateway-cik
        3803226073f6f2feb51a9010bd1d3e53143214a7
        ```


For additional functionality of Exosite products available on your gateway, take a look at the output of the following commands:

### The supervisorctl cli

The `supervisorctl` command-line interface is extremely powerful and incredibly useful. It is highly recommended that you run this command (*`supervisorctl` using `sudo` if necessary*) and then type `help` when you've entered the interactive cli.

    **Example**:

    ```
    $ supervisorctl
    gmq                                RUNNING      pid 1184, uptime 1:02:51
    gwe                                RUNNING      pid 1184, uptime 1:02:51
    supervisor> help

    default commands (type help <topic>):
    =====================================
    add     clear    fg        open   quit     remove    restart    start   update
    avail   exit     maintail  pid    reload   reread    shutdown   stop    version

    supervisor> 
    ```

### The Device Client cli

The device-client library has a lightweight command-line interface accessible with the callable `gdc`.

```
gdc --help
```

### The GWE cli

The `gwe` process seen in `ps -ef` and `supervisorctl status` output has a robust command-line interface.

```
gwe --help
```

### The Gateway Message Queue cli

The `gmq` process has a command-line interface that helps configure some runtime behavior like timers and logging.

```
gmq --help
```

# Summary

The steps in this guide were designed to get you moving as quickly as possible with GWE and Exosite. If you have questions, concerns, or suggestions on how to make this documentation better, please contact [gwesupport@exosite.com](mailto:gwesupport@exosite.com) with a detailed message.

At this point you are probably ready to start using GWE to host your Custom Applications. Next, take a look at the following sections:

  * [Custom Gateway Applications](custom_gateway_applications)
  * [Over the Air Updates](otau)
