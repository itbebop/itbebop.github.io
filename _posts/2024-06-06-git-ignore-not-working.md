---
title: "[Git] git ignore 적용이 안되는 경우 (해결)"
excerpt: ".gitignore파일에 추가해도 changes에 남아있는 경우"

categories:
  - git
tags:
  - [tag1, tag2]

permalink: /git/git-ignore-not-working/

toc: true
toc_sticky: true

date: 2024-06-06
last_modified_at: 2024-06-06
---

# .gitignore파일에 추가해도 changes에 남아있는 경우

# 해결
git 파일 추적 해제
```
> git rm -r —cached .
```

파일 모두 add
```
> git add .
```
commit / push
```
> git commit -m "fixed untracked files"
> git push
```