---
title: "The mysterious Intune Policy Error"
date: 2024-05-09
categories:
  - Intune
tags:
  - Bugs and Limitations
  - Configuration Profile
  - Group Policy
  - Site to Zone Assignment
---

So here I am on a bank holiday in Denmark, helping a customer migrate old group policies into Intune. Everything is going fine, until I get to their Internet Explorer Policy. They have a whopping 410 entries in the [Site to Zone assignment list](https://learn.microsoft.com/en-us/deployedge/per-site-configuration-by-policy#windows-security-zones).
For those of you who doesn't know site to zone assignment: It's basically a list of sites you would specify to be put in the "Local Intranet", "Trusted Sites" or maybe even "Restricted Sites". When you add sites to the policy, you can add your URL and then the corresponding number to specify what zone the site should divert to. It goes like so:
* (1) Intranet zone
* (2) Trusted Sites zone
* (3) Internet zone
* (4) Restricted Zone

For those of you who have been in the game for a while, you have probably used these alot in our good old group policies. Sometimes we need to divert a specific site to the Trusted Site Zone to ensure it has more leeway to run old stuff. The first thing that comes to mind is banking and accounting websites, they just use ancient systems and need to load outdated ActiveX modules and run java code. Site to zone assignment list to the rescue!

# The strange (and undocumented) issue
So here I am migrating all of these sites to an Intune policy. I'm using Settings Catalog and found the corresponding Administrative template
![Policy](/assets/images/2024-05-09-TheMysterious-PolicyLimit/SiteToZoneAssignmentBegin.png?raw=true "Site to Zone Assigment List")

However, as I'm almost halfway through adding all of my customers sites from the old GPO, i start receiving a generic error in Intune, "Failed to edit policy, try again later"? What could the reason be?

![Policy](/assets/images/2024-05-09-TheMysterious-PolicyLimit/NotificationError.png?raw=true "Error")

I tried using a different browser, different PC nothing seemed to work, kept getting the same error when I add additional sites. So obviously this is where I figured I hit some kind of hard limit in the policy. But looking through the docs, no such limit is described anywhere. I thought about trying my luck with the [PolicyCSP and Custom OMA-URI](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-internetexplorer#allowsitetozoneassignmentlist) but with that amount of trusted sites, I don't think the customer will be too happy with how to add new sites, the formatting etc.. While it can be relatively easy for someone whose daily life is in Intune, GraphAPI etc, we as consultants also have to remember we have to help our customers so they can also help themselves in case we are not there anymore. So no PolicyCSP for site to zone assignments this time.

So what to do? I decided to raise a support ticket to the Microsoft Intune support team

# The mystery unravelled
I created my support ticket with Microsoft, and sent screenshots + videos showing the issue. Within a short while a friendly Microsoft-support rep reached out to me via teams and asked me to provide something known as a "HAR" log. I was in instructed to open the developer tools in Edge, press the "Network" tab, and the reproduce the issue. Then there is a very small button (at least in my Edge version), where we can export what is known as the "HAR" log (Which I later learned more about [here](https://support.hmhco.com/s/article/Creating-a-HAR-file-in-Microsoft-Edge-Chromium)). The export button is otherwise very easy to miss. I exported it and sent it to the friendly Microsoft support rep.

![Policy](/assets/images/2024-05-09-TheMysterious-PolicyLimit/GeneratingHARLog.png?raw=true "GeneratingHARLog.png")

After a short while he came back to me, and wanted to show me what he discovered. I was sent towards the [HAR Analyzer Tool created by Google](https://toolbox.googleapps.com/apps/har_analyzer/). We imported the HAR log to the tool, I previously sent to the support rep, navigated to a HTTP 400 Response -> Pressed "Response content". In that view, the cause of the error was clear as day.
```
{
  "error": {
    "code": "",
    "message": "The request is invalid.",
    "innerError": {
      "message": "dCV2Policy.Settings[0].SettingInstance.ChoiceSettingValue.Children[0].GroupSettingCollectionValue : Count is not >= 0 and <= 200.\r\n",
      "date": "2024-05-09T08:54:08",
      "request-id": "4bced37d-59da-4bc6-beeb-6169ae72e31e",
      "client-request-id": "0f13de36-5fc6-41e8-a63f-cde2e64e27a4"
    }
  }
}
```

![Policy](/assets/images/2024-05-09-TheMysterious-PolicyLimit/PolicyLimit-200.png?raw=true "Policy Limit 200")

Somebody in the Microsoft Intune team had decided to set the limit to this policy to 200 entries, and just forgot to document it anywhere.

Well I'm happy he did, because now I know what a HAR log is and also know how to use Google's HAR Analyzer, so thank you Intune-man. And special thanks to the Microsoft support rep for the quick and awesome support. 

The Microsoft support rep promises to raise a request internally to have this limitation documented and the policy limit increased. In the mean time, we can simply create an additional policy with our trusted sites to workaround the 200 policy limit. :)


