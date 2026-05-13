---
title: "Building a Dev Environment on Parrot OS"
description: "My journey setting up an Astro 5.0 portfolio, troubleshooting Linux permissions, and mastering image optimization."
pubDate: "May 13 2026"
heroImage: '../../assets/blog-placeholder-3.jpg'
---

## 🚀 Project Overview
This project documents my transition to a professional development workflow. Using **Parrot OS**, I initialized a modern web architecture to host my portfolio and technical blog.

## 🛠 The Tech Stack
- **OS:** Parrot OS (Security Edition)
- **Framework:** Astro 5.0
- **Package Manager:** NPM / Node.js
- **Editor:** VS Code via Terminal

## 🧠 Key Knowledge Gained
### 1. Modern Toolchain Initialization
I learned that tools like `create-astro@5.0.6` are temporary installers. When NPM asks to "install a package," it's pulling the latest blueprint to build the project structure.

### 2. Content Collections & Schemas
I explored Astro's Type-Safe environment. By using `config.ts`, I learned how to define a "schema" that ensures every blog post has a title, date, and a valid image before it's ever published.

## 🚧 Challenges & Solutions

**The Problem:** Terminal "Code" Command Not Found.
**The Fix:** I learned that on Parrot OS, VS Code isn't always linked to the `code` alias. I bypassed this by using the GUI "Open Folder" method while I work on stabilizing my `.bashrc` aliases.

**The Problem:** The "Image Source" Error.
**The Fix:** I hit a common snag where Astro rejected a simple string path for images. I discovered two solutions: moving images to the `public/` folder for direct linking, or using the `../../assets/` relative path to allow Astro's engine to optimize the files.

**The Problem:** Broken Local Preview (Ctrl+C).
**The Fix:** I learned that `Ctrl+C` kills the development server. To get the site back, I use `npm run dev`, which restarts the engine and provides the `localhost:4321` link.

## 🏁 Final Result
I now have a live-reloading development environment where I can write Markdown, save the file, and see the changes instantly in the browser. 

## 🔗 Links
- [GitHub Repository](https://github.com/yourusername/my-portfolio)
- [Local Project Folder](~/my-portfolio)

