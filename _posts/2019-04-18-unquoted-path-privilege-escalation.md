---
title: "Unquoted Path Privilege Escalation"
date: 2021-08-16T15:34:30-04:00
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
> **C:\Program Files\osquery\control user** path and BUILTIN\Users have a lot of permissions in this directory
> **C:\Program Files\osquery\control user\local admin\control.exe** path and BUILTIN\Users have a lot of permissions in this directory

![Modifiable-Path](/assets/images/Screenshot_4.png)

<br>Windows can not interprete spaces. So if there is a unquoted path like this, windows interpret this executable path like this;

![Windows-Interpret](/assets/images/Screenshot_5.png)





-br33z3
