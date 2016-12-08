---
title: SVN 没有填写备注不允许提交
date: 2016-12-08 14:34:55
tags: SVN
---

```bat
@echo off
SET SVN_LOOK="C:\Program Files (x86)\VisualSVN Server\bin\svnlook.exe"
SET SVN_STYLECOP=C:\SVNStyleCop\SVNStyleCop.exe

SET REV_OR_TRAN=transaction

IF "%3" == "test" SET REV_OR_TRAN=revision

%SVN_STYLECOP% -repository:%1 -%REV_OR_TRAN%:%2
IF %ERRORLEVEL% EQU 0 GOTO STYLECOP_OK
EXIT %ERRORLEVEL%

:STYLECOP_OK
%SVN_LOOK% log --%REV_OR_TRAN% %2 %1 | FindStr .
IF %ERRORLEVEL% EQU 0 GOTO FINAL_OK
ECHO "提交必须填写描述" >&2
EXIT -1

:FINAL_OK
EXIT 0

:FINAL_OK
EXIT 0
```