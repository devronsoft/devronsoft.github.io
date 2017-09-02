---
layout: post
title: The past 4 months (Post migrated from Ghost blog)
---

So 4 months have now flown by, and although it's been crazy busy I'm still pretty disappointed in myself for not posting any of the cool stuff I've been doing, as I did it.

I've learnt so much already.

I don't think I'll be able to 'catch up' this blog as such - what I'm going to do in this post is just summarise the last 4 months and then move forward with the view to posting little and often when there's something to say (which I wish I had been doing!).

So - in my last post I wrote a bit about hardware, because I was ordering some servers to set up the environments for this project.

I've now got the 7 servers (5 environments) in place across the two systems.

From this exercise, I learnt a lot about why it is important to move with the times and continuously adopt the latest versions of software to support infrastructure. Specifically, SQL Server. This company was running all their business information systems on SQL Server 2000... back then I didn't appreciate how crazy this was.

Which brings me to...

#### Server Compatibility

I had a vague idea of linked servers - the concept that you could link physically different servers to each other and then query across them with fully qualified names.

So I wasn't expecting this:

Compatibility Error

Umm what?

Yeah so basically, Microsoft stopped backwards compatibility with SQL Server 2000 after 2005... fair enough really. It's to do with the native client - which is a data accessing API that was first introduced with SQL Server 2005.

All my new servers were running SQL Server 2012 across both systems, so there wasn't going to be a problem within Production.

However, this presented a problem for proof of concept for some of the interfacing between the two systems.

You can't even copy tables or restore the actual DB from the old server, it just won't touch it...

#### Workaround - lie to the database

There is a workaround, and it is this:

1 - Create a new 64 bit data source (System DSN) on the 2012 server that needs to connect to the 2000 server. 


2 - Tell the server that the data source is another server and create a MSDASQL connection using T-SQL:

          ```EXEC master.dbo.sp_addlinkedserver @server = N'DIAMDREAMTEST200064', @srvproduct=N'DIAMDREAMTEST200064', @provider=N'MSDASQL', @datasrc=N'DIAMDREAMTEST200064' 
     For security reasons the linked server remote logins password is changed with ######## */
      EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DIAMDREAMTEST200064',@useself=N'True',@locallogin=NULL,@rmtuser=NULL,@rmtpassword=NULL

      GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'collation compatible', @optvalue=N'false'
 GO

''''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'data access', @optvalue=N'true'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'dist', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'pub', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'rpc', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'rpc out', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'sub', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'connect timeout', @optvalue=N'0'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'collation name', @optvalue=null
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'lazy schema validation', @optvalue=N'false'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'query timeout', @optvalue=N'0'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'use remote collation', @optvalue=N'true'
GO

'''EXEC master.dbo.sp_serveroption @server=N'DIAMDREAMTEST200064', @optname=N'remote proc transaction promotion', @optvalue=N'true'
GO

3 - Change MSDASQL provider properties so that ‘Level zero only’ is set to 0. 
If we set it to 1 then only level 0 OLE DB Providers are allowed. If it is 0 (default), all levels of OLE DB provider are allowed. So the value specifies whether all OLE DB providers are supported or just those that are compliant with the level 0 OLE DB interface.

Note this was intended as a temporary workaround for some proof of concept testing, I'm now running SQL Server 2012 on everything. (With Windows Server 2012 R).

I was pretty pleased about sorting this out, and it allowed us to prove that the interfacing concept worked before going full steam ahead with the rest of the design.

This is probably enough for one post though...
