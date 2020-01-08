---
layout: post
date: 2019-01-01
---

목표
개인 개발자가 (비교적)간단하게 유니티 원격빌드를 해보자. (feat. Azure Pipeline)


환경
os : mac OS
vcs : github, devops Repos
build target : AOS, iOS

Flow
빌드PC -> self hosted agent
개발PC -> Git Push (Remote Repo)-> Azure Pipelines -> self hosted agent -> 빌드 -> 수집 -> 배포


본문

create project
git init
build setting, test
git push

self hosted
create pipelines
test self hosted

- Android
apk check

customize pipeline

check

apk upload to artifacts

* debug, release
* google drive (self install test)
* publish

- iOS

xcode check

xcode build setting
    apple account
    app id
    certificate
    provisioning profile

customize pipeline
    other mac os -> p12, profile install

xcode build

* debug, release
* google drive (self install test)
* xcarchive, ipa artifacts upload

* test flight

키워드
azure pipelines
self hosted
xcode
