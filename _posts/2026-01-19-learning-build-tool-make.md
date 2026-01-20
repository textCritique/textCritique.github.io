---
layout: post
title: 'Learning Build Tool: make'
date: 2026-01-19 17:47 +0530
description: some notes based on software carpentry's lesson
image:
category:
tags:
author: textCritique
published: true
sitemap: true
---

# Intro

It is a automation tool which can help run commands to read files, process files and write out the processed files. Mostly, people think of
it as tool for defining how to build a executable from multiple source files.

Basically, it specifies what depends on what and how to update the stuffs that are out of date.

# Syntax

## Terms

### target

It is the file that will be created. A target can have zero or more [dependencies](#dependency). It also has zero or more [actions](#action) associated with it.

### dependency

It is the required file that is needed build or rebuild the given target.

### action

It is the command or script that is executed to build the **target**.

### rule

Target and its dependencies and its actions togther constitute a rule.

## Example

```make
# count words
isles.dat : books/isles.txt
  python countwords.py books/isles.txt isle.dat
```
