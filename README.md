## Description
[![Build status](https://circleci.com/gh/ARMmbed/mbed-ls/tree/master.svg?style=svg)](https://circleci.com/gh/ARMmbed/mbed-ls/tree/master)

mbed-lstools is a Python module that detects and lists mbed-enabled devices connected to the host computer. It will be delivered as a redistributable Python module (package) and command line tool.

Currently supported operating system:

* Windows 7.
* Ubuntu.
* Linux (generic).
* Mac OS X (Darwin).

The stand-alone mbed-lstools Python package is still under development, but it's already delivered as part of the mbed SDK's test suite and a command line tool (see below).

## Rationale

When connecting more than one mbed-enabled device to the host computer, it takes time to manually check the platforms' binds:

* Mount point (disk).
* Virtual serial port.
* mbed's TargetID and generic platform name.

mbedls provides these points of information for all connected boards at once in a simple console (terminal) output.

**Tip:** Because we are all automation fanatics, the ```mbedls``` command will also output mbed-enabled auto-detection data in JSON format (see below).

## Installation from PyPI (Python Package Index)

mbed-ls module is redistributed via PyPI. We recommend you use the [application pip](https://pip.pypa.io/en/latest/installing.html#install-pip).

**Note:** Python 2.7.9 and later (on the Python 2 series), and Python 3.4 and later include pip by default, so you may have pip already.

To install mbed-ls from Python Package Index use command:
```
$ pip install mbed-ls
```

To install latest version use command:
```
$ pip install mbed-ls --upgrade
```

## Installation from Python sources 

**Prerequisites:** you need to have [Python 2.7.x](https://www.python.org/download/releases/2.7/) installed on your system.

**Note:** if your OS is Windows, please follow the installation instructions [for the serial port driver](https://developer.mbed.org/handbook/Windows-serial-configuration).

To install the mbed-ls module:

Clone the mbed-ls repository. The following example uses the GitHub command line tools, but you can do this directly from the website:

```
$ git clone <link-to-mbed-ls-repo>

```
Change the directory to the mbed-ls repository directory:

```
$ cd mbed-ls
```

Now you are ready to install mbed-ls. 

```
$ python setup.py install
```

On Linux, if you have a problem with permissions please try to use ```sudo```:

```
$ sudo python setup.py install
```

The above command should install the ```mbed-ls``` Python package (import ```mbed_lstools```) and mbedls command.

To test if your installation succeeded try the ```mbedls``` command:

```
$ mbedls
```

Or use the Python interpreter and import ```mbed_lstools```:

```
$ python
Python 2.7.8 (default, Jun 30 2014, 16:03:49) [MSC v.1500 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
```

Generic mbedls API example:
```python
>>> import mbed_lstools
>>> mbeds = mbed_lstools.create()
>>> mbeds
<mbed_lstools.lstools_win7.MbedLsToolsWin7 instance at 0x02F542B0>
>>> mbeds.list_mbeds()
[{'platform_name': 'K64F', 'mount_point': 'E:', 'target_id': '02400203D94B0E7724B7F3CF', 'serial_port': u'COM61'}]
>>> print mbeds
```

Extended mbedls API example:
```python
>>> import mbed_lstools
>>> m = mbed_lstools.create()
>>> dir(m)
['DEBUG_FLAG', 
 'ERRORLEVEL_FLAG', 
 '__doc__', 
 '__init__', 
 '__module__', 
 '__str__', 
 'debug', 
 'discover_connected_mbeds', 
 'err', 
 'get_connected_mbeds', 
 'get_dos_devices', 
 'get_json_data_from_file', 
 'get_mbed_com_port', 
 'get_mbed_devices', 
 'get_mbed_htm_target_id', 
 'get_mbeds', 
 'get_mounted_devices', 
 'get_string', 
 'iter_keys', 
 'iter_keys_as_str', 
 'iter_vals', 'list_mbeds', 
 'list_mbeds_by_targetid', 
 'list_mbeds_ext', 
 'list_platforms', 
 'list_platforms_ext', 
 'load_mbed_description', 
 'manufacture_ids', 
 'os_supported', 
 'regbin2str', 
 'scan_html_line_for_target_id', 
 'usb_vendor_list', 
 'winreg']
>>> m.list_platforms()
['LPC1768', 'K64F']
>>> m.list_platforms_ext()
{'K64F': 1, 'LPC1768': 2}
```

## mbedls command line tool

After installation of the mbed-ls package, you can use the mbedls command. It allows you to list all connected mbed-enabled devices and gives you the correct association between your board mount point (disk) and the serial port. TargetID information is also provided for your information.

```
$ mbedls
+---------------------+-------------------+-------------------+--------------------------------+
|platform_name        |mount_point        |serial_port        |target_id                       |
+---------------------+-------------------+-------------------+--------------------------------+
|KL25Z                |I:                 |COM89              |02000203240881BBD9F47C43        |
|NUCLEO_F302R8        |E:                 |COM34              |07050200623B61125D5EF72A        |
+---------------------+-------------------+-------------------+--------------------------------+
```

If you want to use ```mbedls``` in your toolchain, continuous integration or automation script and do not necessarily want to use the Python module ```mbed_lstools``` - this solution is for you.

### Exporting mbedls output to JSON

You can export mbedls outputs to JSON format: just use the ```---json``` switch and dump your file on the screen or redirect to a file. It should help you further automate your processes.

```json
$ mbedls --json
[
    {
        "mount_point": "E:",
        "platform_name": "NUCLEO_L152RE",
        "serial_port": "COM9",
        "target_id": "07100200860579FAB960EFD7"
    },
    {
        "mount_point": "F:",
        "platform_name": null,
        "serial_port": "COM5",
        "target_id": "A000000001"
    },
    {
        "mount_point": "G:",
        "platform_name": "NUCLEO_F302R8",
        "serial_port": "COM34",
        "target_id": "07050200623B61125D5EF72A"
    },
    {
        "mount_point": "H:",
        "platform_name": "LPC1768",
        "serial_port": "COM77",
        "target_id": "101000000000000000000002F7F18695"
    },
    {
        "mount_point": "I:",
        "platform_name": "KL25Z",
        "serial_port": "COM89",
        "target_id": "02000203240881BBD9F47C43"
    }
]
```

## mbed-ls auto-detection approach for Ubuntu

Let's connect  a few mbed boards to our Ubuntu host. The devices should mount as MSC and CDC (virtual disk and serial port). We'll use regular Linux commands to see the boards, then see how ```mbed-ls``` displays them.

In this example, we've connected to our Ububtu machine's USB ports:

* 2 x STMicro's Nucleo mbed boards.
* 2 x NXP mbed boards.
* 1 x Freescale Freedom board.

We can see the mounting result in the usb-id directories in Ubuntu's file system under ```/dev/```. To list mbed boards mounted to serial ports (CDC) via USB, we use the general Linux command:

```
$ ll /dev/serial/by-id
```

We'll see:

```
total 0
drwxr-xr-x root 140 Feb 19 12:38 ./
drwxr-xr-x root  80 Feb 19 12:35 ../
lrwxrwxrwx root  13 Feb 19 12:38 usb-MBED_MBED_CMSIS-DAP_02000203240881BBD9F47C43-if01 -> ../../ttyACM0
lrwxrwxrwx root  13 Feb 19 12:35 usb-MBED_MBED_CMSIS-DAP_A000000001-if01 -> ../../ttyACM4
lrwxrwxrwx root  13 Feb 19 12:35 usb-mbed_Microcontroller_101000000000000000000002F7F18695-if01 -> ../../ttyACM3
lrwxrwxrwx root  13 Feb 19 12:35 usb-STMicroelectronics_STM32_STLink_066EFF525257775087141721-if02 -> ../../ttyACM2
lrwxrwxrwx root  13 Feb 19 12:35 usb-STMicroelectronics_STM32_STLink_066EFF534951775087215736-if02 -> ../../ttyACM1
```

To list boards mounted to disks (MSC) via USB, we use the general Linux command:
```
$ ll /dev/disk/by-id
```

We'll see:

```
total 0
drwxr-xr-x root 340 Feb 19 12:38 ./
drwxr-xr-x root 120 Feb 19 12:35 ../
lrwxrwxrwx root   9 Dec  3 09:10 ata-HDS728080PLA380_40Y9028LEN_PFDB32S7S44XLM -> ../../sda
lrwxrwxrwx root  10 Dec  3 09:10 ata-HDS728080PLA380_40Y9028LEN_PFDB32S7S44XLM-part1 -> ../../sda1
lrwxrwxrwx root  10 Dec  3 09:10 ata-HDS728080PLA380_40Y9028LEN_PFDB32S7S44XLM-part2 -> ../../sda2
lrwxrwxrwx root  10 Dec  3 09:10 ata-HDS728080PLA380_40Y9028LEN_PFDB32S7S44XLM-part5 -> ../../sda5
lrwxrwxrwx root   9 Dec  3 09:10 ata-TSSTcorpDVD-ROM_TS-H352C -> ../../sr0
lrwxrwxrwx root   9 Feb 19 12:35 usb-MBED_MBED_CMSIS-DAP_A000000001-0:0 -> ../../sdf
lrwxrwxrwx root   9 Feb 19 12:38 usb-MBED_microcontroller_02000203240881BBD9F47C43-0:0 -> ../../sdb
lrwxrwxrwx root   9 Feb 19 12:35 usb-MBED_microcontroller_066EFF525257775087141721-0:0 -> ../../sdd
lrwxrwxrwx root   9 Feb 19 12:35 usb-MBED_microcontroller_066EFF534951775087215736-0:0 -> ../../sdc
lrwxrwxrwx root   9 Dec  3 16:10 usb-MBED_microcontroller_0670FF494956805087154420-0:0 -> ../../sdc
lrwxrwxrwx root   9 Feb 19 12:35 usb-mbed_Microcontroller_101000000000000000000002F7F18695-0:0 -> ../../sde
lrwxrwxrwx root   9 Dec  3 09:10 wwn-0x5000cca30ccffb77 -> ../../sda
lrwxrwxrwx root  10 Dec  3 09:10 wwn-0x5000cca30ccffb77-part1 -> ../../sda1
lrwxrwxrwx root  10 Dec  3 09:10 wwn-0x5000cca30ccffb77-part2 -> ../../sda2
lrwxrwxrwx root  10 Dec  3 09:10 wwn-0x5000cca30ccffb77-part5 -> ../../sda5
```

***Note:*** ```mbed-ls``` tools pair only serial ports and mount points (not CMSIS-DAP - yet).

We can see that on our host machine (running Ubuntu) there are many 'disk type' devices visible under ```/dev/disk```. The mbed boards can be distinguished and filtered by their unique ```USB-ID``` conventions. In our case, we can see pairs of ```usb-ids``` in both ```/dev/serial/usb-id``` and ```/dev/disk/usb-id``` with embedded ``` TargetID```.  ```TargetID``` can be filtered out, for example using this sudo-regexpr: ```(“MBED”|”mbed”|”STMicro”)_([a-zA-z_-]+)_([a-fA_F0-0]){4,}```

For example, we can match the board 066EFF525257775087141721 by connecting a few dots:

* ```usb-MBED_microcontroller_066EFF525257775087141721-0:0 -> ../../sdd```
* ```usb-STMicroelectronics_STM32_STLink_066EFF525257775087141721-if02 -> ../../ttyACM2``` Based on the TargetID hash.

From this we know that the target platform has these properties:

* The unique target platform identifier is ```066E```.
* The serial port is ```ttyACM2```.
* The mount point is ```sdd```.

Your ```mbed-ls``` implementation resolves those three and creates a “tuple” with those values (for each connected device). Using this tuple(s), ```mbed-ls``` will convert the platform number to a human-readable name etc.

Note that for some boards the ```TargetID``` format is proprietary (see STMicro boards) and ```usb-id``` does not have a valid TargetID where the four first letters are the target platform's unique ID. In that case, ```mbed-ls``` tools inspects the ```mbed.htm``` file on the mbed mounted disk to get the proper TargetID from the URL in the ```meta``` part of the HTML header.

In the following example, the URL ```http://mbed.org/device/?code=07050200623B61125D5EF72A``` for the STMicro Nucleo F302R8 board contains the valid TargetID ```07050200623B61125D5EF72A```, which ```mbed-ls``` uses to detect the ```platform_name```. ```mbed-ls``` will then replace the invalid TargetID in ```usb-id``` with the value from ```mbed.htm```.

```html
<!-- mbed Microcontroller Website and Authentication Shortcut -->
<!-- Version: 0200 Build: Aug 27 2014 13:29:28 -->
<html>
<head>
<meta http-equiv="refresh" content="0; url=http://mbed.org/device/?code=07050200623B61125D5EF72A"/>
<title>mbed Website Shortcut</title>
</head>
<body></body>
</html>
```

This is the result of ```mbedls``` listing the connected devices that we saw above:
```
$ mbedls
+---------------------+-------------------+-------------------+----------------------------------------+
|platform_name        |mount_point        |serial_port        |target_id                               |
+---------------------+-------------------+-------------------+----------------------------------------+
|KL25Z                |I:                 |COM89              |02000203240881BBD9F47C43                |
|LPC1768              |H:                 |COM77              |101000000000000000000002F7F18695        |
|NUCLEO_F302R8        |G:                 |COM34              |07050200623B61125D5EF72A                |
|NUCLEO_L152RE        |E:                 |COM9               |07100200860579FAB960EFD7                |
|unknown              |F:                 |COM5               |A000000001                              |
+---------------------+-------------------+-------------------+----------------------------------------+
```

## Porting instructions

You can help us improve the mbed-ls tools by - for example - committing a new OS port. You can see the list of currently supported OSs in the [Description](#description) section; if your OS isn't there, you can port it.

For further study please check how Mac OS X (Darwin) was ported in [this pull request](https://github.com/ARMmbed/mbed-ls/pull/1).

# Mocking new or existing target to custom platform name
Command line switch ```--mock``` provide simple manufacturers ID masking with new platform name.
Users should be able to add locally new ```MID``` -> ```platform_name``` mapping when e.g. prototyping.

Mock configuration will be stored in directory where ```mbedls --mock``` command was issues, in local file ```.mbedls-mock```.

**Note***: ```MID```: "manufacturers ID", first 4 characters of ```target_id```. Example: If ```target_id``` is ```02400221A0811E505D5FE3E8```, corresponding manufacturers ID is ```0240```.

## Mock command line examples
* Add new command line parameter ```--mock``` (switch -m)
* Add new / mask existing mapping ```MID``` -> ```platform_name``` and assign MID
    * ```$ mbedls --mock MID:PLATFORM_NAME``` or
    * ```$ mbedls --mock MID1:PLATFORM_NAME1,MID2:PLATFORM_NAME2```
* Mask existing manufacturers ID with new platform name
* Remove masking with '!' prefix
    * ```$ mbedls --mock !MID```
* Remove all maskings using !* notation
    * ```$ mbedls --mock !*```
* Combine above using comma (```,```) separator:
    * ```$ mbedls --mock MID1:PLATFORM_NAME1,!MID2```

## Mocking example with Freescale K64F platform
Initial setup with 1 x Freescale ```K64F``` board:
```
$ mbedls
+--------------+---------------------+------------+------------+-------------------------+
|platform_name |platform_name_unique |mount_point |serial_port |target_id                |
+--------------+---------------------+------------+------------+-------------------------+
|K64F          |K64F[0]              |F:          |COM146      |02400221A0811E505D5FE3E8 |
+--------------+---------------------+------------+------------+-------------------------+
```

* We can mask current mapping ```0240``` -> ```K64F``` to something else. For example we can replace ```K64F``` name with maybe more suitable for us in current setup ```FRDM-K64F```:
```
$ mbedls --mock 0240:FRDM_K64F
```
Current mocking mapping is stored in local file ```.mbedls-mock```:
```
$ cat .mbedls-mock
{
    "1234": "NEW_PLATFORM_1",
    "0240": "FRDM_K64F"
}
```
We can observe changes immediately. Please note this change only works in the same directory because we save ```.mbedls-mock``` file locally:
```
$ mbedls
+--------------+---------------------+------------+------------+-------------------------+
|platform_name |platform_name_unique |mount_point |serial_port |target_id                |
+--------------+---------------------+------------+------------+-------------------------+
|FRDM_K64F     |FRDM_K64F[0]         |F:          |COM146      |02400221A0811E505D5FE3E8 |
+--------------+---------------------+------------+------------+-------------------------+
```

* We can remove mapping ```1234``` -> Anythying using ```!``` wildcard.
Note: We are using flag ```-json``` to get JSON format output of the ```--mock``` operation.
```
$ mbedls --mock !1234 --json
{
    "0240": "FRDM_K64F"
}
```

* We can add multiple mappings at the same time:
```
$ mbedls --mock 0000:DUMMY,1111:DUMMY_2 --json
{
    "1111": "DUMMY_2",
    "0240": "FRDM_K64F",
    "0000": "DUMMY"
}
```

* We can remove (```!```) all mappings using ```*``` wildcard:
```
$ mbedls --mock !*
```

We can verify our mapping is reset:
```
$ cat .mbedls-mock
{}
```

# mbed-ls unit testing
* ```mbed-ls``` package contains basic unit tests.
* Tests are stored under ```\mbed-ls\test ``` directory.
* Tests cover basic function calls, object construction and check if minimal requirements for OS porting are fulfilled.
* Standard Python’s ```unittest``` library was used so it is easy to contribute to test effort.
To invoke test procedure from command line please change directory to current mbed-ls repo directory and call setup.py with 'test' option.
```
$ cd mbed-ls
$ python setup.py test
```
```
running test
running egg_info
writing requirements to mbed_ls.egg-info\requires.txt
writing mbed_ls.egg-info\PKG-INFO
writing top-level names to mbed_ls.egg-info\top_level.txt
writing dependency_links to mbed_ls.egg-info\dependency_links.txt
writing entry points to mbed_ls.egg-info\entry_points.txt
reading manifest file 'mbed_ls.egg-info\SOURCES.txt'
writing manifest file 'mbed_ls.egg-info\SOURCES.txt'
running build_ext
test_example (test.basic.BasicTestCase) ... ok
test_detect_os_support_ext (test.detect_os.DetectOSTestCase) ... ok
test_porting_create (test.detect_os.DetectOSTestCase) ... ok
test_porting_mbed_lstools_os_info (test.detect_os.DetectOSTestCase) ... ok
test_porting_mbed_os_support (test.detect_os.DetectOSTestCase) ... ok
.
.
.
----------------------------------------------------------------------
Ran 18 tests in 0.302s

OK
```

# Known issues
* [mbedls fails to list devices on OS X El Capitan](https://github.com/ARMmbed/mbed-ls/issues/38).
