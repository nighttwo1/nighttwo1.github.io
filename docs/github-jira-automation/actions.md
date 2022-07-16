---
layout: default
title: Github Action 배포하여 사용하기
parent: Github - JIRA Automation
nav_order: 1
tags:
    - custom github action
    - release jira action
---

# Github Action 배포하여 사용하기
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---


## Reminder & Goal
이전 Custom Github Action으로 리팩토링하기 페이지에서 `private으로 github action을 작성하고 이를 사용`하는 방식을 배웠다.

이번에는 `github action을 별도의 repository에 작성하고 이를 배포하여 사용하는 방법`을 배워보려고 한다.


# Github Action for releasing Jira Version
Jira 버전을 배포하기 위한 Github Action을 작성해보려고 한다.

Jira 프로젝트명과 버전명을 알고 있는 상태에서 버전을 배포하려면 프로젝트의 버전들을 조회하여 이중 버전명과 일치하는 버전의 id값을 이용해 상태를 업데이트 해야한다. Jira 프로젝트의 버전 상태를 업데이트하기위한 자세한 방법은 [링크](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-versions/#api-rest-api-3-version-id-put)를 참고하자.

**별도로 Github Action을 작성하는 이유는?**
- 두 단계로 이루어진 action(버전 찾기, 버전 상태 업데이트하기)을 간소화하고 싶어서이다.
- market place에 해당 기능을 제공하는 app이 없어서 직접 만들어보기로 했다.

## Step 1. Github Action Repository 생성

Jira version을 배포해주는 action이라서 [jira-version-release-action](https://github.com/avikus-ai/jira-version-release-action)으로 지었다.

**주의) `Public` Repository로 생성하자.**
- 배포해서 사용하려면 public으로 생성해야한다.

## Step 2. Github Action을 작성한다.

앞선 Custom Github Action으로 리팩토링하기 페이지에서 우리는 Github Action을 작성하는 방법을 배웠다.

- (이번에도 그대로 작성하면 된다.)
    
    ```yaml
    name: "jira release action"
    description: "release jira version by updating version"
    
    inputs:
      version_name:
        description: "jira version name"
        required: true
      project_key:
        description: "project key"
        required: true
      jira_token:
        description: "jira auth token"
        required: true
      domain_name:
        description: "your domain"
        required: true
      release_date:
        description: "release date"
        required: false
    
    outputs:
      status:
        description: "update status"
    
    runs:
      using: "node16"
      main: "index.js"
    ```
    

이제 몇가지를 수정해주자.

- main의 경로를 `index.js`에서 `dist/index.js`로 변경해준다.

    ```yaml
    name: "jira release action"
    description: "release jira version by updating version"
    ... 
    runs:
      using: "node16"
      main: "dist/index.js"
    ```
    
- package json에 `"type":"module"`을 추가해준다.
    
    ```json
    {
      "name": "jira-version-release-action",
      "version": "1.0.0",
      ...
      "type": "module"
    }
    ```
    
- code와 module을 compile하자
    
    ```bash
    npm i -g @vercel/ncc
    ```
    
    ```bash
    ncc build index.js --license licenses.txt
    ```
    
    위의 과정을 완료하면 dist 폴더가 생성되고 그 안에 컴파일된 index.js 파일이 보일 것이다.
    

## Step 3. Readme는 까먹지 말고 작성하자.

- Action을 만들었으면 최소한 아래의 4가지는 작성하자.
    - Action Description
    - Input
    - Output
    - Example usage
    
- Github docs의 readme 예시
        
    ```markdown
    # Hello world javascript action
    
    This action prints "Hello World" or "Hello" + the name of a person to greet to the log.
    
    ## Inputs
    
    ## `who-to-greet`
    
    **Required** The name of the person to greet. Default `"World"`.
    
    ## Outputs
    
    ## `time`
    
    The time we greeted you.
    
    ## Example usage
    
    uses: actions/hello-world-javascript-action@v1.1
    with:
        who-to-greet: 'Mona the Octocat'
    ```
        
    

## Step 4. 작성한 Action을 배포를 해보자

작성한 코드를 Github에 올리자.

- 컴파일전의 index.js파일은 올리지 않아도된다.

```bash
git add action.yml dist/index.js node_modules/* package.json package-lock.json README.md
git commit -m "Use vercel/ncc"
git tag -a -m "My first action release" v0.0.1
git push --follow-tags
```

Github Repository에 들어가면 release 페이지가 있다. 그곳에서 작성한 tag를 배포하자.

- 위에 market place 등록 옵션이 있다. 체크하고 배포하면 market에 등록된다.

## Step 5. 만든 Action을 사용해보자
```yaml
name: Jira Release

on:
  push:
    tags:
      - "v*"

jobs:
  version-release:
    name: version-release
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: release jira version
        uses: avikus-ai/jira-version-release-action@v0.0.1
        with:
          version_name: "github-jira-version-manage-test:v0.0.1-rc1"
          project_key: "***"
          jira_token: "***"
          domain_name: "***"
```

## 결과
Jira 프로젝트의 release 탭을 확인하면 다음과 같이 버전이 release된 것을 확인할 수 있다.
<img width="1135" alt="image" src="https://user-images.githubusercontent.com/43779571/179349210-ad350b7d-43a8-4602-be33-0a1521d0a160.png">


## 느낀점
- 버전 release는 Jira에서 제공하는 기본 api으로 action을 별도로 생성하지 않고 사용해도 좋다. 하지만 project를 관리하고, github의 tag를 관리하여 자동화하는 복잡한 과정이 추가된다면 다음과 같이 직접 action을 만들어 사용하는 것도 고려해볼 수 있을 것 같다.
- Javascript action을 작성하여 배포해두면 다른 action에서 해당 action을 불러와서 사용할 때 관련 모듈을 설치할 필요가 없다는 건 장점으로 느껴졌다.