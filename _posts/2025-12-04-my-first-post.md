---
layout: post
title: My First Post
date: 2025-12-04 20:48 +0530
description: My experience hosting static site generator on github pages for the first time 
image:
category: [meta, blog]
tags: [meta, blog, first, jekyll-chirpy]
author: textCritique
published: true
sitemap: true
---

## Prelude

So this is my first post on this site. I have hosted earlier in this domain but the way I did was unsustainable and flat-out boring: manually writing html pages and enchancing using copilot and then generating css from scratch. At that time I was naive and not confident enough to use site generator. I have also toyed with Wordpress but I didn't like it because it was way too simple and I didn't like its no-code low-code interface (*That isn't to say that I am against it*).

I also heard about jekyll, even tried using it but its doc and tutorial are too short and not enough context is available for learning and using it as a noob. 

### What changed now?

I have become a competent. That's certainly not it. Because I had tried just few months ago - I can't suddenly become pro in span of months. 

Actually, what changed is **patience** (aka butt in chair for long hours). I spent a lot of time reading others blog, stackoverflow and experimenting apart from reading official guides/docs; not to mention LLM chat. Some of things that I angered me is fact that I overlooked or somehow skipped critical piece of info from official docs. Anyways it works now.


## Overview of my blog setup

There are few things that will help immensely with the setup.

### Use jekyll-compose

Append following to gemfile:

```
gem 'jekyll-compose', group: [:jekyll_plugins]
```

Then execute following in the root of site directory:

```console
bundle3.1
```

> Add following to config.
{: .prompt-tip }

```yaml
# The base URL of your site
baseurl: ""

# jekyll-compose settings
jekyll_compose:
  default_front_matter:
    drafts:
      description:
      image:
      category:
      tags:
      author:
    posts:
      description:
      image:
      category:
      tags:
      author:
      published: false
      sitemap: false
    pages:
      author: 
```
{: file="_config.yml" }

This will automatically populate front matter whenever you run the command ` bundle3.1 exec jekyll post "My First Post"` with following:

```liquid
---
layout: post
title: My First Post
date: {current time}
description: 
image:
category: 
tags: 
author: 
published: false
sitemap: false
---

```

I have few more tips but I have to sleep. Maybe I will describe in later post.

Au Revoir