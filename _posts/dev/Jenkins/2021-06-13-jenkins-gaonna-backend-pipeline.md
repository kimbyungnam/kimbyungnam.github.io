---

title: "Gaonna Backend Jenkins CI 구축기"
subtitle: "Jenkins CI for Backend"
categories: dev
tags: Jenkins
---

## Introduction

Gaonna 프로젝트에서 Backend에서 사용되는 CI pipeline을 설명합니다.



## Pipeline

![pipeline](https://user-images.githubusercontent.com/32065940/121815045-3b3b0980-ccaf-11eb-80ae-9dc87bed802a.png)

위의 이미지에서 볼 수 있듯이 pipeline은 총 6개의 stage로 구성됩니다.



#### Git Checkout

```groovy
stage('Git Checkout'){
    steps{
        script{
            env.GIT_COMMIT = checkout scm
            env.GIT_TAG = sh(script: "git name-rev --tags --name-only ${env.GIT_COMMIT}", returnStdout: true)?.trim()
            sh "echo ${env.GIT_STATE} GIT_COMMIT : ${env.GIT_COMMIT}"
        }
    }
}
```

github repository에서 checkout을 수행하며 `commit 해쉬` 값을 *GIT_COMMIT* 환경 변수에 저장하고 해당 commit에 맞는 tag를 조회하여 마찬가지로 *GIT_TAG* 환경 변수에 저장합니다.



#### Gradlew Build

```groovy
stage('Gradlew Build'){
    options{
        retry(2)
    }
    steps{
        script{
            sh """
            chmod +x ./gradlew
            ./gradlew build
            """
        }
    }
    post{
        failure{
            sh "./gradle --stop"
            error "Gradlew Build Fail"
        }
    }
}
```

우리 프로젝트에서는 maven 대신 gradle을 사용하여 빌드합니다. gradle wrapper 바이너리의 `권한`을 설정해주고 build를 수행합니다. 

gradle daemon이 종종 빌드 또는 테스트 도중에 원인 모를 오류를 내며 실패하는 경우가 있습니다. 반복적인 daemon 생성으로 인한 메모리 부족 문제로 보여 retry 옵션과 post section에서 실패시 gradle daemon을 죽이고 다시 수행하도록 하여 문제를 해결했습니다.

> post section에서 다시 에러를 일으키지 않으면 에러가 무시되기 때문에 *error "Gradlew Build Fail"*으로 에러를 다시 발생 시킵니다



#### Test

``` groovy
stage('Test'){
    options{
        retry(2)
    }
    steps{
        script{
            sh "./gradlew test"
            sh "./gradlew check"
            sh "echo 'generating coverage report'"
            sh "./gradlew codeCoverageReport"
            sh "echo 'send it to codecov'"
            sh "curl -s https://codecov.io/bash | bash"
        }
    }
    post{
        failure{
            sh "./gradle --stop"
            error "Test Fail"
        }
    }
}
```

정의된 test를 수행하고 codecov report를 만들고 업로드합니다.

Codecov report를 업로드 시에는 `CODECOV_TOKEN`이 환경변수로 설정되어야 합니다. 잘못 설정시에는 아래와 같은 에러를 보게 됩니다.

> {'detail': ErrorDetail(string='Your CI provider is not compatible with tokenless uploads, please upload using your repository token to resolve this.', code='not_found')}

![codecov](https://user-images.githubusercontent.com/32065940/121815435-86eeb280-ccb1-11eb-8905-85f42ac693ef.png)

codecov 연동을 통해서 pull request시 `테스트 커버리지`를 확인할 수 있으며 정의된 기준에 충족되는지 `system check`을 수행할 수 있게 되고 이에 따라 merge를 막을 수 있습니다.

![system check](C:\Users\byungnam.kim\AppData\Roaming\Typora\typora-user-images\image-20210614014328229.png)



#### JIB Docker images

``` groovy
stage('JIB Docker images'){
    options{
        retry(2)
    }
    steps{
        script{
            docker.withRegistry(HARBOR_REPOSITORY, HARBOR_CREDENTIAL){
                sh """
                        ./gradlew jibDockerBuild
                        """
            }

        }
    }
    post{
        failure{
            sh "./gradle --stop"
            error "JIB Docker images"
        }
    }
}
```

Docker image를 생성하는 데에 있어서 `JIB`를 활용합니다.

JIB를 사용하기 위해서 `build.gradle`에 아래와 같이 plugin에 jib를 추가해야 하며 설정값을 넣어줘야 합니다.

``` groovy
plugins {
    id 'com.google.cloud.tools.jib'
}
jib {
    allowInsecureRegistries = true
    from {
        image = "harbor.gaonna.tech/base_docker_images/openjdk:11"
    }
    to {
        image = "gaonna_platform/admin:latest"
    }
    container {
        mainClass = 'com.bbolab.gaonna.admin.AdminApplication'
    }
}
```



#### Push image to Harbor

``` groovy
stage('Push image to Harbor'){
    when{
        expression{ env.GIT_STATE == 'deploy' }
    }
    steps{
        script{
            docker.withRegistry(HARBOR_REPOSITORY, HARBOR_CREDENTIAL){
                docker.image('gaonna_platform/admin:latest').push("${env.GIT_COMMIT}")
                docker.image('gaonna_platform/api:latest').push("${env.GIT_COMMIT}")
            }
        }
    }
}
```

image registry로 `private Harbor registry`를 사용합니다.

deploy 뿐만 아니라 PR에 해당하는 Job도 같은 Jenkinsfile을 사용하기 때문에 환경변수를 통해 현재 job이 deploy인지 PR인지 구분을 짓습니다.

``` groovy
environment {
    GIT_STATE = "${env.JOB_NAME == 'gaonna-backend-deploy' ? 'deploy' : 'else'}"
}
```

*withRegistry*에서 Harbor registry 주소와 사용될 계정을 적용하고 이미지들을 push합니다. registry 주소는 HARBOR_REPOSITORY이며 사용된 credential은 HARBOR_CREDENTIAL입니다. 이는 `Environment Injector` plugin을 활용하여 Job에 환경변수로 정의되있습니다.

![Environment Injector](https://user-images.githubusercontent.com/32065940/121815921-ad155200-ccb3-11eb-9f73-b66c92dfb193.png)



#### Update Manifest

``` groovy
stage('Update Manifest'){
    when{
        expression{ env.GIT_STATE == 'deploy'}
    }
    steps{
        dir('gaonna-manifest'){
            git branch: 'main', credentialsId: 'gaonna-sa', url: 'https://github.com/bbolab/gaonna-manifest'
            dir('kustomize/admin/overlays/alpha'){
                sh "kustomize edit set image admin=harbor.gaonna.tech/gaonna_platform/admin:$GIT_COMMIT"
            }
            dir('kustomize/api/overlays/alpha'){
                sh "kustomize edit set image api=harbor.gaonna.tech/gaonna_platform/api:$GIT_COMMIT"
            }
            script{
                sh "echo $env.GIT_TAG"
                if(env.GIT_TAG == 'real'){
                    dir('kustomize/admin/overlays/real'){
                        sh "kustomize edit set image admin=harbor.gaonna.tech/gaonna_platform/admin:$GIT_COMMIT"
                    }
                    dir('kustomize/api/overlays/real'){
                        sh "kustomize edit set image api=harbor.gaonna.tech/gaonna_platform/api:$GIT_COMMIT"
                    }
                }
            }
            sh """
            git add -A
            git commit -m "$GIT_COMMIT" && git push -u origin main
            """
        }
    }
}
```

Job에 설정되지 않은 manifest github repository를 업데이트 합니다.

dir() directive는 해당 디렉토리에서 작업을 수행한다는 의미로 아직 없다면 생성까지 합니다. deploy시 기본적으로 `alpha`를 업데이트하며 *real* tag가 달린 경우 `real`까지 업데이트를 수행합니다.
