---
layout: post
title: Working late on a Friday (Post migrated from Ghost blog)
---

So last night I found myself at work until about 8.30pm, and it was Friday. Unheard of!

But I'm finding that time just falls away during the day as I'm really into what I'm doing - usually either some SQL development or general troubleshooting. And I generally end up leaving work because I'm hungry, not because I want to.

Last night I finally cracked the first 'end-to-end' piece of this integration project, and I just couldn't put it down.

I'm going to do another detailed post at some point around database triggers - a lot to say there, but keeping this one simple this is how it works:

The general premise of this project is that there are two systems. They need to interact with each other, but because one of them is not designed to do that it requires a hell of a lot of development to get it to work (my job). My system is an ERP system, the other one is a standalone purchasing system. Both are SQL server based.


#### This is what I cracked last night:

* The standalone system has a dataset (from user input). This dataset is comprised of four variables, and two of them are parametrised based on the first two selections (thanks to my stored proc).

* This dataset needs to pass to my system, be validated and then trigger a data write to 4 different tables in my system; the combination of which creates a valid order which users can then do things to within the application.

this is the bit I started to make progress with at about 4pm yesterday afternoon).


#### And this is how it now works:

* Other system writes date to a random holding table, basically a table just for input. It's not a temporary table, but the data is temporary.

* There's a trigger on this input table so that when it receives data a stored proc kicks off.

* The stored proc runs a series of validations based on what will successfully populate the 4 tables required by the application to pull up a valid order in the UI.

* If all the validations pass, the tables get written to and the rows in the input table get deleted. If any of them fail, the whole transaction is rolled back and the failed input remains in the input table.

* The SQL error of the the validation is stored in an error table.

I guess one day I might look back and think this was really simple crap to get excited about, but last night it was pretty cool seeing it all work for the first time.

_EDIT: 2 years later - Yeah, this is simple crap and triggers are bad, man..._
