---
layout: single
title:  "Office Online 'The file is locked for shared use' error"
date:   2020-01-15 12:00:00 +0100

#categories: jekyll update
---
# Short breakdown

After editing files in Office Online (Office 365 and OnPrem) in a chromium based browser you might experience the following: 
 
 The file is locked for shared use by ...

![Alt Text](/assets/img/2020-01-15/87w5lhgv56z4mh22pcpr.png)

# Background

chromium based browsers all have the flag `allow-sync-xhr-in-page-dismissal`. This flag can be found under

* Edge chromium: `edge://flags`
* Chrome: `chrome://flags`

Recently it seems the default value for ths flag has been changed to ["disabled"](https://www.chromestatus.com/feature/4664843055398912). Furthermore this flag will be removed from both Chrome and [Edge](https://docs.microsoft.com/en-us/deployedge/microsoft-edge-policies#allowsyncxhrinpagedismissal). If this functionality is disabled, users will frequently experience issues where recently edited office documents are locked for editing by other users and metadata cannot be changed by the current user from the web interface. 

Office Online seems to rely on this functionality to unlock documents when you navigate away from the document editor interface. If not able to unlock a document, an exclusive lock will remain on the document until it expires after 30 minutes.

If you cannot reproduce the error currently, you can try to change the flag to "disabled" and you should be able to consistently reproduce the error. 


# How to reproduce 

Note: use a chromium based browser

1) in a document library, create a new document 

![Alt Text](/assets/img/2020-01-15/qxybkvd71vkzmuflsgy8.png)

2) edit the document if you like, return to the document library when done

![Alt Text](/assets/img/2020-01-15/r9u2ezjd7en8jbedlpxv.png)

3) now try to add a title to your document

![Alt Text](/assets/img/2020-01-15/xr2s8t6ytcxxdfkxi0mt.png)

4) the file is locked for editing. Note that other users will not be able to edit the document at all since the current user will have an exclusive lock