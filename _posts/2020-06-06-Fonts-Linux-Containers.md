---
layout: post
title: Installing fonts in Linux Containers
---

#Overview
I just added to my back-end application (.NET Core 3.1 deployed in Azure Linux based App Service) a feature to create a report (XtraReports), export it as PDF and send it by mail (Twilio SendGrid).

As usual everything worked quite well on my computer, but when I deployed it to my testing environment in Azure, the first thing I noticed was the fonts on the PDF weren't the same I used (I used Segoe UI). Then I realized it was because the font wasn't installed on the server. I've never faced this problem when deploying in a Windows based server, as the fonts I always use, are installed by default. In this case I have to upload the ttf and "install them" on the server.

This article describes how I achieved it, and it's goal is to be a quick reference for future projects.

#Tasks

1. [Upload the ttf files](#upload-the-files)
2. [Install the fonts](#install-the-fonts)

#Upload the files {#upload-the-files}

## Add the fonts to my project

1. Create a "_Fonts_" folder in the project
2. Copy in the _Fonts_ folder the TTF files
3. Set "**Copy to output directory**" property to "**Copy always**" for all the TTF files

The above will include the TTF files in the artifact to be deployed to the server at:

```console
/home/site/wwwroot/Fonts
```

You can verify if the files were properly copied, through the SSH option from the Azure App Service console:
![Azure SSH Console](/images/AzureAppService-SSH.png "Azure SSH Console")

```bash
~/site/wwwroot# ls /home/site/wwwroot/Fonts
```

#Install the fonts {#install-the-fonts}
The Linux command `fc-cache` scans the font directories and builds a cache with the available fonts, kind of equivalent to installing the fonts on Windows.

This command looks for fonts on predetermined folders:

```console
/home/.local/share/fonts
/home/.fonts
/usr/share/fonts/*
...
```

So, prior to running the `fc-cache` we'll copy the files from our _Fonts_ folder to any of the folders will be scaned by the `fc-cache` command.

```bash
# First create the folder for the font under a scanned directory
~/site/wwwroot# mkdir /usr/share/fonts/truetype/segoeui

# Then copy the files to the scanned directory
~/site/wwwroot# cp ~/site/wwwroot/Fonts/* /usr/share/fonts/truetype/segoeui

# Rebuild the fonts cache
~/site/wwwroot# fc-cache -fv
```

If you get an "**fc-cache command not found**" error, then you have to install first the "fontconfig" package:

```bash
~/site/wwwroot# apt install fontconfig
```

Now you should be able to use `fc-cache`

Once the above is done, you can check if the font is actually installed:

```bash
~/site/wwwroot# fc-list
```

This command will list the cached fonts, you should see your fonts on the list.

Here is where I tested the report is working and taking the font. If everything is working, now is time to persist it.

##Persisting the changes
As all the above actions did happened on the current instance of the server (container), the next time the container get restarted or re-created all the above changes will be lost. So, you need to make sure this actions being run everytime the container starts up.

At this point I realize the best approach would be to include this changes on the docker profile. However, at this time I've been using the Azure App Service default template and don't want to spend more time on changing that, so, I'll use the Configuration option from the App Service.

![StartupOptions](/images/2020-06-06-Fonts-Linux-Containers-StartupCommand.png "StartupOptions")

Here we'll concatenate the above commands with &&. In my case will be:

```console
apt-get update && apt-get install -y apt-utils libgdiplus libc6-dev && apt-get install -y libicu-dev libharfbuzz0b libfontconfig1 libfreetype6 && apt install fontconfig && mkdir /usr/share/fonts/truetype/segoeui &&  cp ~/site/wwwroot/Fonts/* /usr/share/fonts/truetype/segoeui && fc-cache -fv && dotnet myApplication.dll

```

In the above line I'm issuing other packages required for **XtraReports Core**. If you're using DevExpress XtraReports you should include it.
