---
title: "How: to navigate on AWS S3 bucket using CLI"
date: 2020-04-16T05:50:27+09:30
draft: false
toc: false
images:
tags:
  - s3
  - aws
  - cli
  - bash
  - cloud
---

Using the command line interface (CLI) can be very intimidating and make one completely lost if you always dealt with graphical drag and drop, click and point interface.

I grew up in the 80s and my first interactions with computer was always through CLI and remember that the 2 first commands I learned were then `cls` for clear the screen and `dir` to list the files in the current directory. Even applications like word for MS-DOS was all text and there was no mouse then. 

Soon after in the late 80 and early 90 came the windows graphical interface and the OS was loaded on a graphical interface we never had to deal with it again, unless you were a developer or worked with IT and had to configure the computer BIOS or the likes of it.

Now in Data Science I had to re-learn and get comfortable with.

All the work I do now is mostly using Linux or Mac which uses bash like command line commands so this is the one I know more commands.

As for Amazon Web Services (AWS) CLI to access S3 buckets uses the same commands as linux so if you already know how to use linux you already know how to use the AWS CLI.

Here is a crash course on how to use it to perform the basic operations.

## S3 command line crash course

There are 3 useful command that probably you can achieve 99% of what you want

1. ls (list directory and files)

2. cp (copy files from and to)

3. sync (synchronize directories from and to)

Lets get to is with examples

## 1. ls

examples:

* list your buckets

``` 
$ aws s3 ls
2013-04-22 20:47:26 veladelivery
```
* list the directory tree of a specific bucket

```
$ aws s3 ls s3://veladelivery
                           PRE FSWM/
                           PRE GCDBT72635G/
                           PRE GCDBT8547S/
                           PRE GCDBT9874/
                           PRE backtest/
                           PRE freereport/
```

* if you have spaces in file path name then you need to use "" quotes

```
$ aws s3 ls "s3://veladelivery/FSWM"
                           PRE FSWM/
```

* important that a forward slash `/` is needed at the end of the command to list the files in the directory

``` 
$ aws s3 ls "s3://veladelivery/FSWM/"
2016-05-19 06:21:24          0 
2016-08-18 14:51:39   10298199 15.mp4
2016-08-18 14:52:27   13516259 17.mp4
2016-08-18 14:35:28    8244901 8.mp4
2016-05-19 06:22:06     741773 Achieve Financial Freedom - How to create, grow and protect wealth.pdf
2016-08-01 21:38:09     907255 Achieve Financial FreedomV3.pdf
2016-08-18 06:18:26     763189 Achieve Financial FreedomV4.pdf
2016-08-21 17:12:10     763468 Achieve Financial FreedomV5.pdf
2016-08-26 05:41:11     923941 Achieve Financial FreedomV6.pdf
2016-08-01 12:34:40     763151 Achieve Financial Freedom_V2.pdf
2016-08-18 14:54:50    7426172 BONUS.mp4
2016-05-19 06:23:01     742532 Financial Freedom - Why some people achieve it while others dont.pdf
2016-08-18 09:38:00 1721777541 IMG_8303.mov
```

## 2. cp 

Usually you want to copy a file from a S3 bucket to your local machine or vice versa then will give example for copy to and one for copy from

**copying from S3 to your local machine** 

```
$ aws s3 cp "s3://veladelivery/FSWM/Achieve Financial Freedom_V2.pdf" /Users/pedrojunqueira/Desktop
download: s3://veladelivery/FSWM/Achieve Financial Freedom_V2.pdf to ../Achieve Financial Freedom_V2.pdf
```
Note that the to path does not necessarily need to have the file name

alternatively this command would have worked the same

`s3 cp "s3://veladelivery/FSWM/Achieve Financial Freedom_V2.pdf" "/Users/pedrojunqueira/Desktop/Achieve Financial Freedom_V2.pdf"`

## 3. sync

If your objective is to download a single file or upload only one file surgically `cp` is the best option but the option I use the most is `sync` which syncs any local folder to a folder path in my bucket.

Think of a working area on your computer where you do work save files and etc and at the end of the day you just run the `sync` command to upload the changes to the cloud. That's what `sync` is all about.

the pattern is 

`aws s3 sync <origin path> <destination path>`

here is an example

```
$ aws s3 sync s3://veladelivery/backtest /Users/pedrojunqueira/Desktop/mys3bucket
download: s3://veladelivery/backtest/GCDBT.zip to mys3bucket/GCDBT.zip
```
in the example above even if I did not have a path /mys3bucket aws would have created one and synced all the content down from the origin path /backtest to that folder. I used different names but a good convention would be to use the same name, but you can name it whatever you want.

This is just a quick example of what you may need but basically it follows the linux commands for more information look at the [documentation](https://docs.aws.amazon.com/cli/latest/reference/s3/). 