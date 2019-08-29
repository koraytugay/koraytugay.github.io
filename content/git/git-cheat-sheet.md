# Git Cheat Sheet
{:.no_toc}

* TOC
{:toc}

## Print current username and email
```bash
git config user.name
git config user.email
```

## Update email for current project
```bash
git config user.email "koray@tugay.biz"
```

## Update email globally
```bash
git config --global user.email "koray@tugay.biz"
```

## Reset Local to Remote
```bash
git checkout master
git reset --hard origin/master
```