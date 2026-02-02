---
layout:     post
title:      "Github Pages Personal Blog Deployment Failed Using Github Action 2"
date:       2026-02-02 15:55:00
author:     "JoyLee"
header-img: "img/github.jpg"
catalog: true
tags:
   - github action
   - github pages
   - blog
   - jekyll
   - copilot
---

# Random Thoughts (Can Skip)
Wanted to update my blog again, only to discover that Github Action deployment failed again 😭. Starting another round of troubleshooting, let me document this.

# Solution Process
## Problem Identification
First, go to the Github Pages repository and check the commit history to find the failed deployment commit.
![Find failed deployment commit](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164259553.png)
![Find failed deployment commit](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164553404.png)
Click the ❌ to view detailed information about the deployment failure.
![View deployment failure details](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164826378.png)
## Problem Analysis (With Copilot Assistance + Personal Analysis)
On the error details page, click `Explain error` in the top right corner to use Github's integrated Copilot to analyze the error cause.
![Analyze error cause](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202165412045.png)
Then try to solve it based on the solution provided.
![Copilot provides solution](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202165727896.png)
Based on Copilot's explanation and my own analysis, I identified the issue was in the `jekyll.yml` file. I used the + button at the bottom left to send the `jekyll.yml` file directly to Copilot and asked it to tell me exactly which line to modify.
<style>
   .image-row {
      display: flex;
      gap: 10px; /* spacing between images */
      justify-content: center; /* optional: horizontal alignment */
      align-items: flex-end;    /* optional: vertical alignment */
   }
   
   .image-row img {
      max-width: 100%;
      height: auto;
   }
</style>
<div class="image-row">
   <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202170343052.png" width="50%"/>
   <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202171155737.png" width="50%"/>
</div>

## Solution

**Below is the solution provided by Copilot:**

After reviewing your file, I found you already have a step to install build-essential (lines 37-38), but you may need to add more dependencies.

**Solution One: Modify Line 38 (Recommended)**  
Change line 38 to:
```yaml
run: sudo apt-get update && sudo apt-get install -y build-essential libffi-dev
```
**Complete modified section:**
```yaml
- name: Install build tools
run: sudo apt-get update && sudo apt-get install -y build-essential libffi-dev
```
---
**Solution Two: If Solution One Doesn't Work, Lock sass-embedded Version**  
Add or modify this line in your `Gemfile`:
```ruby
gem "sass-embedded", "~> 1.69.5"
```
This uses a more stable version to avoid potential issues with version 1.93.3.

---

**Suggested Operation Order:**
1. Try Solution One first (modify line 38)
2. If still failing, use Solution Two (lock version in Gemfile)
After modifying, commit the code and Github Actions will automatically rerun. The build failure should be resolved.

After following Solution One and committing the code, it succeeded immediately.
![Deployment successful](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202172745777.png)

# Reflection and Summary

I asked Copilot to summarize and analyze this issue. Here's the prompt I gave it:
```
I successfully resolved it with Solution One. Please summarize and analyze the root cause of this error.
```

**Copilot's Response:**

Happy that Solution One resolved the issue! Let me analyze the root cause of this error for you:

## Error Analysis

### 1. **Direct Cause: Missing libffi-dev Dependency**
   - The `sass-embedded` gem contains **native extensions** that require compiling C/C++ code
   - This gem depends on the **FFI (Foreign Function Interface)** library to communicate with the underlying Dart Sass compiler
   - While you already installed `build-essential` (which includes gcc, g++, make and other basic build tools), you were missing **libffi-dev** (FFI development headers and libraries)

### 2. **Why is libffi Needed?**
   ```
   Ruby (Your Jekyll code)
     ↓
   sass-embedded gem (Ruby wrapper)
     ↓ Calls via FFI
   Dart Sass Compiler (Native binary)
   ```
   - `libffi` is a library that allows different programming languages to call each other
   - `sass-embedded` uses FFI to let Ruby code call Dart Sass's native compiler
   - Without `libffi-dev`, the build process cannot link necessary libraries, causing build failure

### 3. **Key Error Messages in Logs**
   ```
   Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
   rake failed, exit code 1
   An error occurred while installing sass-embedded (1.93.3)
   ```
   This indicates the build process failed when trying to compile native extensions

### 4. **Why Didn't This Problem Occur Before?**
   - Older versions of Jekyll or sass processors may have used pure Ruby implementations
   - `sass-embedded` is a newer implementation that relies on Dart Sass (better performance but requires FFI)
   - Github Actions runner images may have been updated, removing some pre-installed development libraries

## Summary

**Core Issue**: When building Ruby gems with native extensions, you need a complete compilation toolchain:
- ✅ `build-essential`: Provides compilers (gcc/g++) and build tools (make)
- ✅ `libffi-dev`: Provides FFI library headers for cross-language calls
- ✅ Both are essential

**Lesson Learned**: When using gems with native extensions in CI/CD environments, ensure all necessary system-level dependencies are installed, not just basic build tools.

# Closing Remarks
That's the complete process of how I resolved the Github Action deployment failure. Throughout this process, Copilot deserves a big credit!  
I realize that solving problems nowadays is mainly about finding the right approach and direction. With AI acting as a technical expert to help us, the efficiency is incredibly high.