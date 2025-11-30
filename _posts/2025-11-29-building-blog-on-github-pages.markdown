---
layout: post
title:  "Beyond the Quickstart: My Tips for Building a Stable Technical Blog on Jekyll and GitHub Pages"
date:   2025-11-29
categories: Jekyll, GitHub Pages
---
## **The Stack: GitHub Pages and Jekyll**

**GitHub Pages** is a free static site hosting service that publishes a site directly from your GitHub repository.
**Jekyll** is the static site generator used by GitHub Pages. It converts Markdown, Liquid templates, and theme files into a complete static HTML website.

Because I’m using GitHub Pages’ **built-in Jekyll build**, it runs in **safe mode**, which means:

* only approved plugins are allowed
* gem-based themes work normally
* remote themes are supported
* no custom build pipeline is needed

This is perfect for a lightweight, low-maintenance technical blog.

## **Tip: Customizing Your Theme**

I followed the official [GitHub Pages Quickstart](https://docs.github.com/en/pages/quickstart), but I hit a few common friction points once I started customizing.

### **1. Theme Compatibility Issues**

Each Jekyll theme expects a specific file structure. Switching themes often produces errors about missing layout files or front matter, because themes define different required pages and partials.

### **2. Hidden Theme Source**

Most Jekyll themes are shipped as Ruby gems. Their internal files — layouts, includes, Sass — are **not visible in your repository**, which means you cannot directly edit things like:

* header layout
* navigation partials
* page templates

### **The Customization Fix**

To customize deeper than configuration alone allows, you simply override the theme files locally.

The process is straightforward:

1. Visit the theme’s GitHub source repository
2. Download the parts you want to customize
3. Copy them into your own repo under:

   * `_layouts/`
   * `_includes/`
   * `_sass/`

Any file placed in your repository automatically overrides the version inside the theme gem. No forking or custom builds needed.

## **Choosing Stability: Locking Down the Remote Theme**

I ended up sticking with the default **Minima** theme but switched to the dark variant. Because GitHub Pages supports remote themes, you can reference a theme repository directly in your `_config.yml`.

But here’s the architect’s trick for long-term stability:

### **Pin the remote theme to a specific commit.**

Instead of referencing the theme repo like this:

```
remote_theme: owner/repo
```

you pin it like this:

```
remote_theme: owner/repo@<commit-hash>
```

This ensures:

* no unexpected theme updates
* no surprise build breaks
* consistent layout and styling over time

This works **even without GitHub Actions**, because GitHub Pages allows remote themes — and allows pinning them — as long as they are GitHub Pages–compatible.

_Note: I will eventually give the GitHub Actions deployment a try._