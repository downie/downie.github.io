---
layout: post
title:  Hubot & Slack, a Robot Love Story
date:   2016-04-19 12:34:19 -0700
categories: archive
---
<!-- cspell:ignore hubot hubot's Sullins oncall -->
> This post originally appeared on [the Usermind blog](https://www.usermind.com/blog/hubot-slack-a-robot-love-story). Some of the images have been lost, but I'm preserving most of the content here.

Here at Usermind, we love robots. I spend most of my time in our Seattle office as a robot controlled from my mountain lair in Montana. In addition to powering a super-efficient [remote working culture](https://usermind.com/blog/the-robot-uprising-working-remotely-at-usermind), robots also make our daily communication smarter, too.

## What is Hubot?

Hubot is a chatbot developed by Github. Originally built for older chat clients like Campfire, Hubot is adaptable to any chat client, including Slack.So we made our own instance for Usermind, and named him Rob.

We have given Rob a few awesome skills that have made team communication way more effective. We’ve also open-sourced a few of them so you can use them yourself. Here are just a few of the many things we’ve built Rob to do.

## Feature #1: @oncall

We use PagerDuty to rotate who is on call each week. Their main responsibility is to keep the engineering team on track by handling all the randomization themselves. Sometimes that means alarms going off, sometimes it’s an engineer noticing that an alarm should be going off, and sometimes it’s our customer success team with an urgent need for a customer.

The problem was that everyone needed to have the on-call rotation in their heads in order to randomize the correct person. I reached out to Sullins, but his on-call shift ended two days ago, things like that. And support people don’t care that you’re not on call. 

This custom plugin listens for any mention of @oncall. When it hears it, it figures out who’s on call by querying the PagerDuty API and then mentioning the appropriate person. That way you can just mention @oncall to have your issue seen by the primary on-call person.You can [add it to your own Hubot instance here](https://github.com/usermindinc/hubot-at-oncall).

## Feature #2: PR poller

We use GitHub for so many things that email notifications from them can get noisy. We generally try to get someone’s attention by mentioning or assigning them, and the emails get lost in the wind. So we wanted to have a means of getting the attention of long-open Pull Requests into the context of where we already are — Slack!

At its core, this extension is a report builder for all open Pull Requests in the company. This spans multiple repos across multiple organizations. You can narrow the query to filter by organization, team, or user. This lets you quickly see who’s got actionable things to do in GitHub.

And it gets better.

It was a chore to remember to ask for the report every day, so we did what any good robot should do, and automated it.With the subscribe commands, you’re able to request a report with a given frequency. On its own, Hubot will regularly pull and collate all the open pull requests from across the company and present them in a simple text view. This makes it very clear who is getting blocked by long-living PRs, who is responsible for acting on them, and ultimately, how good we are at getting our jobs done in a timely manner.

This is also something you can try at home, by [installing the script from our public repo](https://github.com/usermindinc/hubot-github-pr-reporter).

## Feature #3: Rob, quote!

We’re a funny bunch of people. We say funny things, and sometimes those things are even funnier out of context. Enter Rob’s quote command.

When someone says something funny, quote them to Rob. When you want to relive a past hilarious quote without any context whatsoever, ask Rob to give you someone’s quote at random.

## Try it out

Those are just a few of the ways Hubot's Slack integration helps us talk to the right people, keeps us up to date with our coworkers, and generally makes work chat more fun. Do these tools sound useful for your company? Add any of our [open-source Hubot scripts from GitHub](https://github.com/usermindinc?utf8=✓&query=hubot) today!