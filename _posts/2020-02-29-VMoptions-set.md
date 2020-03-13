---
title: 设置VM_options
date:  	2020-02-29 14:56:36 +0800
category: Original
tags: [Java,Tomcat]
excerpt: 配置服务器的VM_options
---

### 前言

通过配置tomcat实现对VM options的设置（性能调优，ENC加密等）

设置放置于对应tomcat文件夹的bin/catalina.bat，大约223行

#### Linux

```
rem ----- Execute The Requested Command --------------------------------------- 
(export) JAVA_OPTS="-Djasypt.encryptor.password=jasypt"
echo Using CATALINA_BASE:   "%CATALINA_BASE%"
echo Using CATALINA_HOME:   "%CATALINA_HOME%"
echo Using CATALINA_TMPDIR: "%CATALINA_TMPDIR%" 
```

#### Windows

```
rem ----- Execute The Requested Command --------------------------------------- 
set JAVA_OPTS="-Djasypt.encryptor.password=jasypt"
echo Using CATALINA_BASE:   "%CATALINA_BASE%"
echo Using CATALINA_HOME:   "%CATALINA_HOME%"
echo Using CATALINA_TMPDIR: "%CATALINA_TMPDIR%" 
```

