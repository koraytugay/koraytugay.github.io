# Git Cheat Sheet
{:.no_toc}

* TOC
{:toc}

## Print current username and email
```plaintext
git config user.name
git config user.email
```

## Update email for current project
```plaintext
git config user.email "koray@tugay.biz"
```

## Update email globally
```plaintext
git config --global user.email "koray@tugay.biz"
```

## Reset Local to Remote
```plaintext
git checkout master
git reset --hard origin/master
```