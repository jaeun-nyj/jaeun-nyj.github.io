---
layout: post
title: GitHub auto login when pull & push
comments: true
---

GitHub으로 블로그를 만들어 운영하려다보니, 내용을 수정하고 push, pull하는 일을 자주하게 됐다. 특히 블로그는 GitHub 페이지에서 직접 코드를 수정하는 것이 아니라면, git push를 해야 수정한 코드가 잘 적용 됐는지 확인할 수 있는 구조이기 때문에 특히나 push할 일이 많았던 것 같다. git pull, push를 할 때 GitHub username과 비밀번호를 입력해서 로그인을 해주어야하는데 이 과정이 굉장히 번거롭게 느껴졌다. 그래서 자동 로그인 설정을 해놓게 되었다.

git pull, push시에 자동 로그인되도록 만드는 코드는 다음과 같다.  
``` shell
git config credential.helper store
git push [github HTTPS 주소]
```

![github-auto-login](./images/github-auto-login.png)

![github-auto-login](https://user-images.githubusercontent.com/88612547/129158588-fa4ba4c3-568e-430b-8455-3164f076165d.png)

