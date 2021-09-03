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

So when we look at the result there is a service call as ***"LocalAdmin Control Service"*** and its executable path "C:\Program Files\osquery\control user\local admin\control.exe"





-br33z3
