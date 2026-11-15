---
title: "What I've been up to lately: An update"
date: 2026-11-15
categories:
  - BlogThings
tags:
  - Blog
---

If you've been following along, you might have noticed it's been a bit quiet on the blog lately. My last post was back in September, and that's not because I've run out of things to write about. Quite the opposite actually. I've been hyper-engaged with 2 different customers on 2 very large projects, and they have pretty much eaten up every available hour I've had.

I'm not going to go into too many details, but I wanted to give you a quick update on what's been keeping me busy.

## Project 1: A global retail rollout

The first project is a global retail rollout, where the customer took over a bunch of stores. Taking over stores means taking over everything that comes with them, including a lot of existing hardware that needed to be migrated into the customer's tenant.

On top of the migration, we built a new Autopilot setup, so the devices could be onboarded in a consistent way across all the stores.

And then there was the packaging. 2 apps servicing the Point of Sale (PoS) systems needed to be packaged and deployed with Intune. Let's just say these were not modern apps. They were old, they were not built with security in mind. If you've ever had to package a legacy line-of-business app that expects to run with full admin rights and writes to places it really shouldn't, you know exactly what I'm talking about. We got there in the end, but it took a lot more time and patience than I'd like to admit.

The good news is that we managed to remove the local admin rights, so the end-users no longer need to be local admins for the apps to work. In the process, though, I pinpointed a security issue with one of the apps: it writes passwords to its log files in clear text. Yeah, you read that right. Not exactly what you want to find on a device sitting in a store, so that has been flagged and is being dealt with.

## Project 2: A global cybersecurity initiative

The second project came out of thin air. It's with a customer I've been working with for several years on all sorts of different things. Suddenly, they started calling me in for these super long workshops, several hours at a time, with one main goal: elevating their security posture.

The work has focused on 3 main areas:

* **Conditional Access with device compliance:** Making sure only compliant devices can access company resources.
* **Keeping devices up-to-date:** Making sure all devices are up-to-date across Windows, macOS and iOS.
* **A true up between AD, SCCM and Intune:** Making sure the inventory is consistent across all 3, so we actually know what's out there. You can't secure what you don't know exists.

It's been a lot of work, but it's also been really rewarding to be part of an initiative like this, where the customer is genuinely committed to raising the bar on security.

## So, what's next?

I've literally been in that period of the year where it feels like there are not enough hours in the day. To be honest, that's usually around April/May for me, but this year it came late, I guess.

The good news is that I have several blog posts lined up and ready to go. I'll start posting again once I get some breathing room during the next few weeks, so stay tuned :)
