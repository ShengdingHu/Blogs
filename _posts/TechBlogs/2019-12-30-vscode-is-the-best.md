---
layout: post
title: vscode is the best
categories: software
description: vscode configuration
keywords: vscode,configuration
---



# Vscode is The Best

Recently I was doing my project on Operating System Class and was working together with students in Software Engineering Department. And We were working on Linux where we start from blank. And my teammates recommand VSCODE for writing code in linux because it's very flexible and small. Now I like it very much ! And I switch from using pycharm to vscode for python as well.

## Comparison between Vscode and pycharm

|               | vscode        | pycharm                                       |
| ------------- | ------------- | --------------------------------------------- |
| size          | small         | big (can't afford 2+ in my machine 8G corei7) |
| open speed    | 2 sec         | 10+ sec                                       |
| for new comer | a bit hard    | direct use                                    |
| flexible      | very flexible | can only use what JetBrain provide            |

## Amazing Plugins

vscode itself doesn't have many features. but it's **opensourced**, so many third party plugins are supported.

-  file transfer : SFTP
- filesize : filesize
- python and lint : python
- intelligent completion: vs intellicode
- git work flow: Git-Graph

![1577638433822](..\..\images\screenshots\1577638433822.png)

## Open Vscode in WSL

this the best feature for a wsl user.

after installing it in windows, we need to install plugin: Remote-WSL. then  we can open a folder in WSL which containing our code and type 

```bash
code .
```

this will open vscode from wsl for you and your console in vscode h is no longer cmd but bash.



this is what to follow:

<https://blog.csdn.net/weixin_43734095/article/details/99373925>

## change setting

We ofter need to change setting to satisfy our need.

ctrl+shift+p and input the plugin's name you will see the feature of it



## show and hind bars

Alt will change the visibility of top navigation bar

in “查看” (view) > "外观"（outlook）can show or hide different bars



 

## Enable  auto complete of site-packages



in setting of corresponding setting  (if open is WSL, modify in WSL:ubuntu, if open in cmd, modify in User), add python.autoComplete.extraPath



![1577637699108](..\..\images\screenshots\1577637699108.png)

![1577637758364](..\..\images\screenshots\1577637758364.png)

![1577637775470](..\..\images\screenshots\1577637775470.png)