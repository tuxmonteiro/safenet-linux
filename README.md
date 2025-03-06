# safenet-linux

## Introduction

How to configure SafeNet eToken in Linux

### Tested On

* Ubuntu 22.04
* Ubuntu 24.10

### Install pcscd and libnss3-tools

```bash
sudo apt install --yes pcscd libnss3-tools
```

### Download SafeNet eToken driver

1. Download at ![SafeNet Drivers](https://support.globalsign.com/code-signing/safenet-drivers)
2. Unzip
3. Install
```bash
sudo dpkg -i ~/Downloads/GlobalSign-SAC-Ubuntu-2204/Ubuntu-2204/safenetauthenticationclient_10.8.1050_amd64.deb
```

### Recover the safenet eToken lib path

Once SafeNet is installed, recover the path of the safenet lib:

```bash
$ find /lib* /usr/lib* /usr/local/lib* -name "*libeToken.so*"
/usr/lib/libeToken.so.10.7.77
/usr/lib/libeToken.so.10
/usr/lib/libeToken.so
```

We will use the `/usr/lib/libeToken.so.10` for the example

## How to configure Firefox

Open Firefox and go to the security preferences (`about:preferences#privacy` in firefox navbar).

At the end of the page, click on `Security Devices` and a new window will appear:

![security devices](images/security-devices.png)

Click on Load and enter the following information:

* Module name: eToken PCKS#11 module
* Module Filename: `/usr/lib/libeToken.so.10`

Click Ok.

Firefow is now configured

## How to configure Chromium

Chromium does not offer a graphical interface to manage the PKCS devices. We will use libnss3-tools in order to configure it:

```bash
cd ~
modutil -dbdir sql:.pki/nssdb/ -add "eToken" -libfile /usr/lib/libeToken.so.10 # Will configure the token. Press enter if prompted
modutil -dbdir sql:.pki/nssdb/ -list # Check if the token appears in the list
```

If the "NO DB FOUND" error appers, create the db before and repeat last step:
```
mkdir -p ~/.pki/nssdb
modutil -dbdir sql:.pki/nssdb/ -create
```
