---
layout:	post
title: "git rebase로 commit 합치기"
data: 2019-03-19 18:04
categories: etc
comments: true
---

이미 등록된 두개 이상의 commit이 있을 때, 이 commit들을 합치려면

``` git rebase -i HEAD~합칠 commit 갯수 ```

위와 같이 커맨드를 실행한다.

예를 들어 두개의 commit을 합치려면 아래 커맨드를 실행한다.

``` git rebase -i HEAD~2 ```

커맨드를 실행하면 아래와 같이 commit 수정 에디터와 합치는 commit이 표시된다.

![9983403D5C95C1E525](https://user-images.githubusercontent.com/54487021/64496585-99b8a400-d2e1-11e9-9a0c-5c839363b3e1.png)

이 때 하나를 제외한 모든 다른 합치는 모든 commit을 pick에서 squash로 바꿔준다.

![99AE743E5C95C13F0D](https://user-images.githubusercontent.com/54487021/64496586-9a513a80-d2e1-11e9-9af0-7d4da7adb4a5.png)

Ctrl + X를 누르고 저장하면 끝.

만약 git server에 이미 이전 합쳐야할 commit들이 push 되어있다면

``` git push -f origin HEAD ```

위 명령어를 통해 강제로 push하여 remote repository도 갱신해준다.
