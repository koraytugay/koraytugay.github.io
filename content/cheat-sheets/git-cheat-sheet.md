---
layout: default
title:  "Git Cheat Sheet"
---

# Git Cheat Sheet
```bash
# Print current username and email
git config user.name
git config user.email

# Update email for current project
git config user.email "koray@tugay.biz"

# Update email globally
git config --global user.email "koray@tugay.biz"

# Reset Local to Remote
git checkout master
git reset --hard origin/master
```