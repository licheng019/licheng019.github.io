---
layout: post
title: January 15-21 2017 Working Notes
category: Working Notes
tags: Working Notes
description: Working Notes for every week
---

- Working Notes for Jan 15 - 21, 2017

## 1. Work
1) IA-187

	Name: Update the Database Form so that the fields load based on user's selections

	Solve the Dynamic show values for database service in service now.(story point is 5)

2) IA-307

	Name: Wildcard Certificate Coverage

	Refactor the whole get method, and added the corresponding feature for wildcard only cover for one level of the cert.

3) IA-312

	Name: AD-REST: Don't allow users to create accounts with invalid characters

	change for account name error check

4) IA-202 

	Name: Remove Application Delivery Control Service dependency on Auspice

	Remove the Auspice dependency and Auth0 dependency and test whether all methods are working in both Dev and Prod.


## 2. Code review

1) IA-216

	Name: Expire the new hire link once manager finishes submitting the NHRF form

2) IA-332

	Name: Update the Text on the Hardware Request Form to meet global CTS requirements 

## 3. Thinking in this week

	1) Since we don’t have enough metrics, such as ADC, only monitor the create method
	Should add more metrics for Kelly and Sarah, and then determine which service is more 
	important and then update the corresponding feature

	2) Create Database metrics as a single service, so don’t have to implement the repeat code 
	for every service, saving time, saving code, saving the configuration size
	provide the service as one interface, so just call it instead of implement it.

	3) Write a js syntax standard, really important from the team perspective, saving code review time and make it more efficient.
	Since Everyone using their own style, make standard style , such as use === always.

	4) write unusable script for automatically configure the server, this is for CI/CD
	May need new server for the service, and think about how to regorganize the server
	and saving money and resource for company.

	5) Asking for trust, I know my own code better.

## 4. Knowledge Learning (Free Time)

1) Ansible

	1. Ansible Inventory and Configuration

	2. Ansible Module

	3. Ansible Plays and PlayBook

2) AWS

	1. IAM

	2. EC2

	3. Load Balancer
