ons---
title: consul集群搭建
date: 2018-09-26 10:51:54
tags: maven
---

# 添加第三方jar到nexus

```sheel
 mvn deploy:deploy-file -DgroupId=greenplum  -DartifactId=greenplum -Dversion=1.0 -Dpackaging=jar -Dfile=D:\WorkSpace\analysis\api\lib\greenplum.jar -Durl=http://172.16.0.241:8081/repository/maven-releases/ -DrepositoryId=maven-releases
```

<!-- more -->