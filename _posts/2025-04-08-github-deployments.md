---
title: Github Actions Deployments
date: 2025-04-10 02:11 # HH:MM format
categories: howto
tags: git # always lowercase
---

# Using Github Actions for Deployments

## Setup Github Actions for Deployment

Action to deploy PRs made against main branch (in my case Development).
![image](https://github.com/user-attachments/assets/23a7af40-3dc3-4a14-8c98-c40f1be3ec17)

Action to deploy to QA, Production, etc. after tests pass in Development.
![image](https://github.com/user-attachments/assets/07fc7c00-a243-4ced-acc9-00e96f30b262)

## Setup Rulesets for enforcement or assistance in following processes
![image](https://github.com/user-attachments/assets/5083e175-2e5e-4200-b164-0563b47335b1)

Ruleset on Development to Require a PR, and code review.
- Target branches: Development
- Restrict deletions
- Require a pull request before merging
  - Require Approvals: 1
- Block force pushes

Ruleset for Deploying Tags
- Target tags: *-Deploy-QA, *=Deploy-PROD
- Restrict updates - we don't want changes after a deployment. Raise a new tag.
- Restrict deletions - we want to maintain a nice history for audits.
- Block force pushes

Ruleset for Tag Naming (just to avoid typos)
- Target tags excluding: *-Deploy-QA, *-Deploy-PROD
- Restrict creations
- Block force pushes
