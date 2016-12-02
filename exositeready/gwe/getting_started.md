# Getting Started

This section is provided to help you quickly download Gateway Engine,
install it onto your gateway and start using it as quickly and easily as
possible. Once you've completed this section you will have a Gateway
Engine installed and running on a gateway. If you encounter any issues, please contact the dedicated GWE support team at [gwesupport@exosite.com](mailto:gwesupport@exosite.com).

## Minimum Requirements

Before beginning, you should first verify that your
gateway has the minimum requirements for running Gateway Engine.

* Linux OS
* Python 2.7.9+
* Network interface (`eth0`, `wlan0`, `ppp0`, etc.)
* GNU shell
* 128MB Flash
* 64MB RAM

**NOTE:** Some testing has been done on Python 2.7.3, but hasn't been tested extensively and is not recommended.

# Step One - Making an Account

Sign up for a [Murano account](https://exosite.com/murano/). 

**NOTE:** When signing up for a new account, there will be emails you will need to
take action on in order to activate your account and login.

# Step Two - Making and Configuring a Product

## Make a New Product
Once your account is set up, add a Product and name it (e.g. "Gateway Engine", "My Product", etc). 

## Configure the New Product

Now that you have a product, you must configure it with resources either using **MrMurano** (recommended) or **Manually**.

### Use MrMurano

The [MrMurano tool](https://github.com/tadpol/MrMurano) is a command-line tool you can configure and use with your Murano Solutions and Products. 

#### Quick Configuration

Below are the minimum steps needed to configure MrMurano for using Gateway Engine.

##### Configure User Name

```
mr config user.name USER_NAME
```

##### Select and Configure your Business Account

```
mr account --business
mr config business.id XXXXXXXXXXX
```

**NOTE**: The `mr config` dialog stores configuration entries in `.mrmuranorc` files, but treats passwords differently. The first time you log in to an account and are prompted for your password, MrMurano stashes the password in the file `~/.mrmurano/passwords`. 

If you don't want this stored in a local file, run `rm ~/.mrmurano/passwords` after each session.

##### Configure Product ID

```
mr config product.id XXXXXXXXXXX
```

Once you configure MrMurano, you can use the following
commands to create the required Gateway Engine resources to the Murano
device. 

##### Tell MrMurano Where to Find the Spec File

The default location MrMurano uses is `$PWD/specs`. This can be overridden with the following command:

```
mr config location.specs relative/path/to/folder/containing/spec.file
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

### Manually

The table, below, shows the resources you must add to the Product Definition, what to name them
and what format to choose for them.

| Alias         | Format        | Description   |
| ------------- | ------------- | ------------- |
| usage_report  | string        | Gateway Engine sends a report of all processes using its resources (`gmq`, `device-client`) to send data to Murano. This report contains information about how much network bandwidth is being consumed as well as other meta data about network requests.|
| engine_report | string | Gateway Engine reports information about what applications are installed and other meta data like uptime, exit codes, and versions.         |
| device_info | string | Gateway Engine reports filesystem and OS data like OS and kernel version as well as free memory and disk space usage.|
| engine_fetch | string | Gateway Engine regularly checks this dataport for formatted messages containing instructions on new apps and updates to install.        |
| fetch_status | string | Once an app is installed over-the-air or an update to an app is executed, Gateway Engine reports the STDOUT and STDERR from the app installer. This dataport is also used for uncaught exception logging.       |
| update_interval | string | This value, in seconds, is the delay between each series of Gateway Engine reports and OTAU checkins.|

## Verify Resources Exist in Product Definition

To verify the resources are created on your new product, you can visually inspect the Product Definition on your Business > Product page and click the Definition tab.

Another way to do verify is to use MrMurano:

```
mr product spec pull
```

You should see output similar to this:

  ```
  +-----------------+--------+------------------------------------------+
  | Alias           | Format | RID                                      |
  +-----------------+--------+------------------------------------------+
  | engine_fetch    | string | 584375baa68eb9e3d3c342caa5cf783a24965029 |
  | engine_report   | string | d1cdfece4e080c723e7b2d57e0c508f6e4c96c20 |
  | device_info     | string | 875c312cf0532a17556a05118374d215cbbfb4ee |
  | update_interval | string | ab3885bc85c68fc0290ee1adbf7264671651e816 |
  | usage_report    | string | b8405345023bb5dc72488ed072e96a5dc5f3424a |
  | fetch_status    | string | 4c237e449a6fdff9e2ca72b3832c89902413eb6b |
  +-----------------+--------+------------------------------------------+
  ```

# Step Three - Adding a Device

Copy down the MAC address of your Gateway and use it as the serial number for your new Murano Gateway Engine device. 

Add a Device to your Murano Product with the MAC address of your gateway and name it something relevant (e.g. My Getting Started Gateway).

# Step Four - Download, Install, Configure Gateway Engine

To download the latest version of the Public Release of Gateway Engine,
follow these steps:

1.  Navigate to the [Gateway Engine Release Packages](/exositeready/gwe/release_packages/) page and follow the instructions to
    download Gateway Engine.
2.  Run these commands to copy Gateway Engine to your gateway (the
    actual filename in the command may differ):

    ```
    ssh <USER>@<GATEWAY_IP> "mkdir /opt"
    scp GatewayEngine.v1-1-2.tar.gz <USER>@<GATEWAY_IP>:/opt 
    ```

    At this point, you have downloaded the latest release of Gateway Engine and copied it to your gateway.

3.  Run this command to untar the release package and install Gateway
    Engine onto your gateway:

    ```
    ssh <USER>@<GATEWAY_IP> "cd /opt
      tar zxvf GatewayEngine.v1-1-2.tar.gz
      cd gateway-engine
      ./install.sh"
    ```

    **NOTE:** In some Linux environments, you'll need to use Super-User permissions to run the installer. In this case, replace the `./install.sh` command to:

    ```
    sudo ./install.sh
    ```

4.  Once the installation completes, you'll need to configure Gateway
    Engine for your IoT solution and Exosite account. This will require
    one piece of information from your Murano account and the serial 
    number of your gateway.

    1.  In your Murano account, navigate to your Product and click on
        the Info tab. Copy the Product ID and use it in the commands,
        below, in place of &lt;PRODUCT\_ID&gt;.
    2.  Determine the serial number of your gateway. Gateway Engine is
        programmed to retrieve the MAC address from the internet
        interface of your choosing (e.g. `eth0`, `wlan0`,
        `ppp0`, etc.) when the `--set-iface` command-line switch
        is used. Or you can just specify any serial number you want
        with the `--set-uuid` command line switch.
   
    3.  Once you've gathered this information and determined what serial
        number to use for your gateway (interface MAC address or custom
        serial number), run the following command to configure Gateway
        Engine:

    ``` 
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id <PRODUCT_ID> --set-iface <THE_INTERFACE>""
    ```

    **Example:** A simple example that uses `eth0`.

    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id dubhxzv0r4e1m7vj --set-iface eth0"``
    ```

    **Example:** A simple example that uses `eth0` and a one second update interval.

    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id dubhxzv0r4e1m7vj --set-iface eth0 --set-update-interval 1.0"
    ```

 Or if you want to just specify your own serial number:
 
    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id <PRODUCT_ID> --set-uuid <THE_SERIAL_NUMBER>""
    ```
        
    **Example:**

    ```
    ssh <USER>@<GATEWAY_IP> "gwe --set-product-id dubhxzv0r4e1m7vj --set-uuid 12345"
    ```
    
5.  To complete the installation you will need to reboot the gateway.
    To reboot, you can toggle the power or use the following command:

  ```
  ssh <USER>@<GATEWAY_IP> "reboot"
  ```
  
**NOTE:** Gateway Engine uses `supervisord` to start itself on boot and once it starts, it will start Gateway Engine as well as all other installed Custom Gateway Applications.

# Step Five - Verify

Watch for new data in the Gateway Engine Device on your Product device.

Once the reboot has completed, you will notice that `supervisord` and
`gwe` processes are running in the output of the `ps -ef` command. Some
gateways have alot of processes and the `ps -ef` command can be too much
to read through. If this is the case, filter the output with `grep`
(e.g. `ps -ef | grep 'super\|gwe\|gmq'`). You can also use the
`supervisorctl status` command to view the status of the Gateway Engine
applications.

```
ssh <USER>@<GATEWAY_IP> "supervisorctl status"
gmq                           RUNNING    pid 621, 00:01:38
gwe                           RUNNING    pid 620, 00:01:38
```

A few seconds after rebooting the gateway you should see data appear in
the aliases of your GatewayEngine device. You can either view this in the 
Business > Product > Device view of the Murano website, or you can run the 
following MrMurano command a few times until you start seeing new data.

```
mr product device read <SERIAL_NUMBER_OF_GATEWAY> engine_report 
```

For additional functionality of Exosite products available on your gateway, take a look at
the output of the following commands:

## The Device Client cli

```
gdc --help
```

## The Gateway Engine cli

```
gwe --help
```

## The Gateway Message Queue cli

```
gmq --help
```

# Summary

The steps in this section were designed to get you moving as quickly as
possible with Gateway Engine and Exosite. If you have questions,
concerns or suggestions on how to make this documentation better, please
contact gwesupport@exosite.com with a detailed message.
