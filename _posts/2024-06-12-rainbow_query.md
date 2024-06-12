---
title: Rainbow Queries for little data
date: 2024-06-12 14:00 # HH:MM format
categories: data
tags: VScode rainbowCSV
---

# Rainbow CSV with queries

My tool of choice for big-but-not-too-big text files that need viewing and maybe a bit of filtering is Rainbow CSV extension in VS Code. This works well as long as the file isn't over the 20-40MB threshold where VScode no longer wants to load it. Once we are working with files that size, [Python Pandas](https://sarlaac.github.io/posts/big_data/) seems to fit the requirements.

Similiar to the example done for Pandas, slicing down the data by a simple condition is just as easy. Running this will create a new file with just the results.
![image](https://github.com/Sarlaac/sarlaac.github.io/assets/92181960/4e5011d2-e0d0-4444-94ae-29c935178cc0)

More to come, all the other sql like things you can do.
