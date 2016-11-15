---
layout: post
title: React Native Set up
category: React
tags: React
description: React Native Learning
---
> Reference:[https://facebook.github.io/react-native/docs/getting-started.html]

- Set up React Native for IOS in MAC 

## 1.XCode

The easiest way to install Xcode is via the Mac App Store. Installing Xcode will also install the iOS Simulator and all the necessary tools to build your iOS app.

PS: RN require Xcode of version 7.0 above. 
 	Above 7.3, Mac OS require 10.11 above.

## 2.Homebrew
Using below command install Homebrew

	/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

## 3. Node

	brew install node

RN require NodeJS of 4.0 above.
If brew install lower version of nodejs, You can choose download from NodeJs Offical Website https://nodejs.org/en/download/

## 4.Npm
npm is a NodeJS package manager. As its name would imply, you can use it to install node programs

	npm install -g react-native-cli

if there is permission issue. try below command

	sudo npm install -g react-native-cli

## 5.Testing React Native Installation

	react-native init AwesomeProject

	cd AwesomeProject

	react-native run-ios
