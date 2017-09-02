---
layout: post
title: More Powershell... SQLPS
---

Today I discovered how to execute a SQL script from Powershell... will be useful for me tomorrow, as I'll need to run a few complicated SQL scripts in quick succession as part of a demonstration.

I've been able to save the scripts (using Powershell IDE) so I can run them in literally a second tomorrow.

```code
import-module sqlps

invoke-sqlcmd -inputfile "C:\Users\myusername\Desktop\scripts\Supplier.sql" -serverins
```
