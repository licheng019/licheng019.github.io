---
layout: post
title: Customzie Scheduler on Runbook
category: Azure
tags: Azure
description: Customize Scheduler on Runbook
---

## Why we need to Customize Scheduler 

The reason is because Azure doesn't provide flexible schedule control for runbook. Such as my case. Only run the runbook from Thursday to Monday. It's really hard to control the runbook stop on Tuesday and Wesday. Either set up expiration manually each week or control on the runbook script. The second one is better than the first one. So I am going to present how to do it on the script. I hope Azure will add more flexible schedule in the future.

## Azure
Add below scipt in your runbook script
	
	$CurrentDateTimeUTC = (Get-Date).ToUniversalTime()
	$Weekdays = @(Tuesday","Wednesday")
 
	# Check if day of the week is a working day (Monday - Friday). If not, exit.
	If (-Not ($Weekdays -contains $CurrentDateTimeUTC.DayOfWeek))
	{
	   return
	}
