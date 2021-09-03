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



-br33z3
