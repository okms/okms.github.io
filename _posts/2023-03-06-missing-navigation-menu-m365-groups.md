---
layout: single
title:  "Missing navigation menu in M365 Groups"
date:   2023-03-06 09:00:00 +0100
excerpt: ""
header:
  overlay_filter: rgba(0, 0, 0, 0.6)
  show_overlay_excerpt: true
categories: Teams, SharePoint, M365
---

Recently I stated getting reports from users who had problems with the navigation menu dissapearing from SharePoint Team sites. Illustrated below, the first screenshot is the expected behaviour, with the site navigation on the left side of the page. The second screenshot is the problem, where the navigation menu is missing. 

![Expected behaviour](/assets/img/2023-03-06/expected.png)
*Regular library experince*

![Missing navigation menu](/assets/img/2023-03-06/missing.png)
*Only list elements visible, all navigation items missing*

When looking closer at the second screenshot, we can see the URL to our list is `https://<tenant>.sharepoint.com/sites/<group>/_layouts/15/listallitems.aspx?app=teamsPage&listUrl=...`

Usually the `AllItems` view will have a URL like `https://<tenant>.sharepoint.com/sites/<group>/Lists/<listname>/AllItems.aspx`, and looking at the link in our left navigation does indeed confirm we are linking to the normal Allitems view. We can therefor conclude we are redirected to `/_layouts/15/listallitems.aspx?` when clicking the link. Armed with this information I had a quick look at local storage for the site, and found the following:

![Local storage](/assets/img/2023-03-06/localstorage.png)
*Local storage for the site contains `TeamsHostClientType`*

Whenever this issue occurs, the `TeamsHostClientType` key is present in local storage. Out of curiousity I tried to remove the key, and the navigation menu reappeared. I then tried to set the key to `web`, and the navigation menu disappeared again. Some further investigation revealed that all users experiencing this issue had accessed Teams in the same web-browser as they were experiencing the issue in. In Teams, it turned out, these users had access to Teams channels where SharePoint pages were added as tabs. This causes a the key `TeamsHostClientType` to be set in local storage, and any links to All Items views will redirect to the `/_layouts/15/listallitems.aspx?` Page. 

So, these are the steps to reproduce the issue

1. Create a new M365 Team
2. In Teams add a SharePoint page from the associated SharePoint site as a tab
3. Create a list in the SharePoint site, add a link to this list to the navigation menu. Ensure you can access the list via the navigation menu
4. Visit the new Teams Team in your web browser and make sure to select the SharePoint tab you added previously
5. Refresh the home page of the SharePoint site, and click the navigation element previously added

You should now be redirected to the `/_layouts/15/listallitems.aspx?` page, and the navigation menu should be missing. Accessing dev tools (F12) and looking at local storage should reveal the `TeamsHostClientType` key. Removing this key should restore the navigation menu if you go to the home page of the SharePoint site and again click the navigation element previously added.