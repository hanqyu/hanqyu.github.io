---
title: sudo apt-get upgrade vs dist-upgrade
categories: [TIL]
tags: [ubuntu, linux, shell command, production]
excerpt: 왜 같이 쓰는지 궁금해서 찾아봤다
header:
  teaser: assets/images/teasers/ubuntu-logo-640x353.jpg
---


# 맥락

기존에 ubuntu를 기반으로 deploy할때 

`sudo apt-get upgrade`

`sudo apt-get dist-upgrade`

를 혼용해서 썼었는데, 왜 같이 쓰는지 궁금해서 찾아봤다

# What I learned

apt-get upgrade는 새로운 패키지를 추가하거나 지우지 않음. 현재 있는 패키지의 upgrade가 새로운 패키지를 요구하거나 필요없는 패키지의 삭제를 요구한다면 그 작업을 실행하지 않음.

다만 apt-get dist-upgrade는 그렇지 않음. apt-get은 smart conflict resolution system이 있기 때문에 가능한거라고. 

그러므로 특별한 이슈가 아니라면 `sudo apt-get dist-upgrade`만 써도 무방할 것 같다. 

# 출처

[What is "dist-upgrade" and why does it upgrade more than "upgrade"?](https://askubuntu.com/questions/81585/what-is-dist-upgrade-and-why-does-it-upgrade-more-than-upgrade)