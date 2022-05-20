---

title: "Gaonna Backend Jenkins CI 구축기"
subtitle: "Jenkins CI for Backend"
categories: dev
tags: Jenkins
---

## Introduction

해당 포스트는 Gaonna 프로젝트에서 Jenkins Backend job을 구성한 방법 및 내용을 다룹니다.



### project

![project](https://user-images.githubusercontent.com/32065940/121816456-ae944980-ccb6-11eb-9b8b-d5603b39e014.png)

jenkins에서 job을 생성할 때 여러가지 유형의 project를 고를 수 있습니다. 그 중에서 pipeline 프로젝트로 구성을 했습니다

> 가장 처음 구성했을 때에는 `Multibranch Pipeline` 프로젝트를 활용하여 구성했습니다. 장점으로는 pr 처리를 위한 설정이 매우 간단하고 branch에 대한 고민이 알아서 잘 설정된다는 점입니다. 다만 plugin 별로 지원하는 프로젝트 종류가 다르게 되는데 job 별로 환경 변수를 설정할 수 있는 plugin(`Environment Injector`는 지원 안함)을 찾지 못해 pipeline 프로젝트로 변경하게 되었습니다.



## pipeline project

![pipeline](https://user-images.githubusercontent.com/32065940/121816583-6de90000-ccb7-11eb-8006-318fcf3bce62.png)

pipeline project는 위와 같이 4가지의 phase가 존재합니다.



### General

프로젝트에서 사용할 Github repository를 설정하고 `Environment Injector`를 활용하여 환경변수를 설정합니다.

![General](https://user-images.githubusercontent.com/32065940/121816676-f49ddd00-ccb7-11eb-9915-d17c66631c7b.png)

> 프로젝트 수준에서 설정을 하기 때문에 다른 프로젝트에는 영향을 끼치지 않습니다.



### Build Triggers

이 부분에서는 deploy와 PR 프로젝트 각각 설정이 달라지게 됩니다.

#### deploy

`Generic Webhook Trigger`를 사용합니다. github repository 설정에서 webhook를 만들고 token을 일치시킵니다.

#### PR

`Github Pull Request Builder`를 사용합니다. Trigger를 만드는 방법으로 cron 또는 github webhook을 사용할 수 있으며 즉각적인 CI를 위해서 webhook을 사용했습니다.

> [x] Use github hooks for build triggering

![PR](https://user-images.githubusercontent.com/32065940/121816801-a3dab400-ccb8-11eb-813e-e139204a3017.png)

> 주의할 점은 계정에 대한 접근을 whitelist 기반으로 관리한다는 점입니다. whitelist에 없는 계정에 의한 PR은 triggering 되지 않습니다.
>
> 계정을 일일히 하나씩 추가하지 않고 organization으로 추가하였습니다.



### Pipeline

pipeline을 직접 서술할 수 있지만 github repository에서 Jenkinsfile을 가져오게 설정했습니다.

refspec과 branch를 잘 설정하는 것이 중요하다. Trigger 된 PR commit만을 가져오도록 설정했습니다. 이를 위해서 `Github Pull Request Builder`에서 자동으로 설정하는 환경변수(*ghprbActualCommit*)를 활용했습니다.

![pipeline](https://user-images.githubusercontent.com/32065940/121817015-d46f1d80-ccb9-11eb-9b0f-db44fcded55d.png)

> refspec과 branch 설정을 잘못하면 checkout 자체가 안될 가능성이 크다. 어쩌면 job을 trigger 시킨 commit이 아닌 생뚱맞은 commit을 checkout 하게 될 수도 있다. 이와 같은 경우 job이 이전 commit을 빌드하고 job 자체가 실패하지도 않아 모든 것이 잘 되는구나 착각하기 쉽상이다.

