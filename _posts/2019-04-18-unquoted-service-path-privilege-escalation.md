---
title: "Unquoted Service Path Privilege Escalation"
date: 2021-09-03T15:34:30-04:00
categories:
  - activedirectory
tags:
  - Local
  - Privilege Escalation
---

**Lab Scenario:** After you comprimise a machine with low privilege user, you need to privilege escalation to get **local admin** after that you can enumerate Active Directory domain. 

 	![whoami](/assets/images/Screenshot_1.png)

<br>In these scenario we are the **br33z3_lowpriv user** inside a win10 machine. **br33z3_lowpriv** is a **local user** so its not about active directory domain.

So what are we gonna do that in here is find the **Unquoted Service Paths.** We can do this with a lot of way, but I like **PowerUp**(*https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerUp/PowerUp.ps1*) the most.

So open a powershell window and import **PowerUp.ps1**

![PowerUp](/assets/images/Screenshot_2.png)

After Import the PowerUp.ps1 execute **Get-UnquotedService**
This script is uses Get-WmiObject to query all win32_service objects and extract out the binary pathname for each. Then checks if any binary paths have a space and aren't quoted.

![Get-UnquotedService](/assets/images/Screenshot_3.png)

So when we look at the result there is a service call as ***"LocalAdmin Control Service"*** and its executable path "C:\Program Files\osquery\control user\local admin\control.exe"<br>

We also need to look directory permissions to write that service so you can execute ```**(Get-UnquotedService)."ModifiablePath"**
You can see there is a;

![Modifiable-Path](/assets/images/Screenshot_4.png)

> **C:\Program Files\osquery\control user** path and BUILTIN\Users have a lot of permissions in this directory
<br><br>
> **C:\Program Files\osquery\control user\local admin\control.exe** path and BUILTIN\Users have a lot of permissions in this directory

<br>Windows can not interprete spaces. So if there is a unquoted path like this, windows interpret this executable path like this;

![Windows-Interpret](/assets/images/Screenshot_5.5.png)

Also when you look at **services.msc** you can see this service informations. What are the important things in here?

+ Is this service after the machine startup can be triggered as manual or automatic?
+ Is this service Log on As who?(Local System, Network Service, Local Service)
<br>

<p>When we look at the picture we can see this service can startup automatically this means even if I dont have permission to restart this services I can restart machine and wait for this service going to be start. This service Log On As Local System this means this is high privileged service so our manipulated executable going to executed as privileged account. <br>Why are we privilege account here because we are going to register our account inside Local Administrators group and this can be only done by privileged accounts or services.</p>
<br>

Finally as we mention top of the page BUILTIN\Users have write permissions to * C:\Program Files\osquery\control user\ * path so if we write a service binary in this path like this;<br>
**C:\Program Files\osquery\control user\local.exe** windows going to execute this when start the "LocalAdmin Control Service"<br>
PowerUp has a solution for that. 

![Write-ServiceBinary](/assets/images/Screenshot_7.png)
<br>

Now when we restart the computer windows going to start automatic services and our service call "LocalAdmin Control Service" is going to start automatically from Local System permissions.<br>

We put *C:\Program Files\osquery\control user\local.exe* this exe is going to add our user in local Administrators group and this service because of the Unquoted Path Service vulnerability going to execute our malformed executable instead of *C:\Program Files\osquery\control user\local admin\control.exe*
<br>

Lets restart the machine and look at the Local Administrators group members :))

![Local Administrators Group Members](/assets/images/Screenshot_8.png)

<br><br>

-br33z3
