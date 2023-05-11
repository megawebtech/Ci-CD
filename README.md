ДЗ Что такое DevOps. Ci/CD  Ярчак Александр

Задание 1

Что нужно сделать:

    Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
    Установите на машину с jenkins golang.
    Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
    Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта go test . и docker build ..

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.


Установлен jenkins на машину из yandex cloud
Создан проект 
Результат выполения (взят из консоли):


Started by user cctv
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/pipe1
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/pipe1/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/megawebtech/Ci-CD.git # timeout=10
Fetching upstream changes from https://github.com/megawebtech/Ci-CD.git
 > git --version # timeout=10
 > git --version # 'git version 2.17.1'
 > git fetch --tags --progress -- https://github.com/megawebtech/Ci-CD.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 223dbc3f489784448004e020f2ef224f17a7b06d (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 223dbc3f489784448004e020f2ef224f17a7b06d # timeout=10
Commit message: "Update README.md"
 > git rev-list --no-walk 223dbc3f489784448004e020f2ef224f17a7b06d # timeout=10
[pipe1] $ /bin/sh -xe /tmp/jenkins5159097263825911859.sh
+ /usr/local/go/bin/go test .
ok  	github.com/netology-code/sdvps-materials	(cached)
[pipe1] $ /bin/sh -xe /tmp/jenkins1216227815988203964.sh
+ docker build . -t ubuntu-bionic:8082/hello-world:v13
Sending build context to Docker daemon  4.125MB

Step 1/8 : FROM golang:1.16 AS builder
 ---> 972d8c0bc0fc
Step 2/8 : WORKDIR $GOPATH/src/github.com/netology-code/sdvps-materials
 ---> Using cache
 ---> 0612d687ecf1
Step 3/8 : COPY . ./
 ---> 1f1820bf41cd
Step 4/8 : RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix nocgo -o /app .
 ---> Running in d1bf6d190fb5
Removing intermediate container d1bf6d190fb5
 ---> 19567fa6013d
Step 5/8 : FROM alpine:latest
 ---> 5e2b554c1c45
Step 6/8 : RUN apk -U add ca-certificates
 ---> Using cache
 ---> f3f9098b26cc
Step 7/8 : COPY --from=builder /app /app
 ---> Using cache
 ---> a50457e1accb
Step 8/8 : CMD ["/app"]
 ---> Using cache
 ---> a5632d7b7c9e
Successfully built a5632d7b7c9e
Successfully tagged ubuntu-bionic:8082/hello-world:v13
[pipe1] $ /bin/sh -xe /tmp/jenkins16270283487883883896.sh
+ docker login ubuntu-bionic:8082 -u admin -p 45646
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /var/lib/jenkins/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
+ docker push ubuntu-bionic:8082/hello-world:v13
The push refers to repository [ubuntu-bionic:8082/hello-world]
8b239ec40835: Preparing
4ae982a4bf07: Preparing
bb01bd7e32b5: Preparing
8b239ec40835: Layer already exists
bb01bd7e32b5: Layer already exists
4ae982a4bf07: Layer already exists
v13: digest: sha256:089ad98cb41a79cd148b655aa8020774e01e46409fd2d6a958f7cb5476c9302a size: 950
+ docker logout
Removing login credentials for https://index.docker.io/v1/
Finished: SUCCESS


Задание 2

Что нужно сделать:

    Создайте новый проект pipeline.
    Перепишите сборку из задания 1 на declarative в виде кода.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.




Код pipeline:

pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/megawebtech/Ci-CD.git'}
  }
  stage('Test') {
   steps {
    sh 'go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'
   }
  }
  stage('Push') {
   steps {
    sh 'docker login ubuntu-bionic:8082 -u admin -p 45646 && docker push ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER && docker logout'   }
  }
 }
}


Результат положительный:


Started by user cctv
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/pipe3
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Git)
[Pipeline] git
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/pipe3/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/megawebtech/Ci-CD.git # timeout=10
Fetching upstream changes from https://github.com/megawebtech/Ci-CD.git
 > git --version # timeout=10
 > git --version # 'git version 2.17.1'
 > git fetch --tags --progress -- https://github.com/megawebtech/Ci-CD.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
Checking out Revision 223dbc3f489784448004e020f2ef224f17a7b06d (refs/remotes/origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 223dbc3f489784448004e020f2ef224f17a7b06d # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D master # timeout=10
 > git checkout -b master 223dbc3f489784448004e020f2ef224f17a7b06d # timeout=10
Commit message: "Update README.md"
 > git rev-list --no-walk 223dbc3f489784448004e020f2ef224f17a7b06d # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Test)
[Pipeline] sh
+ go test .
ok  	_/var/lib/jenkins/workspace/pipe3	(cached)
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] sh
+ docker build . -t ubuntu-bionic:8082/hello-world:v5
Sending build context to Docker daemon  249.9kB

Step 1/8 : FROM golang:1.16 AS builder
 ---> 972d8c0bc0fc
Step 2/8 : WORKDIR $GOPATH/src/github.com/netology-code/sdvps-materials
 ---> Using cache
 ---> 0612d687ecf1
Step 3/8 : COPY . ./
 ---> 96e7b65d9dff
Step 4/8 : RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix nocgo -o /app .
 ---> Running in 67b60e6fc845
Removing intermediate container 67b60e6fc845
 ---> b4c757d9024a
Step 5/8 : FROM alpine:latest
 ---> 5e2b554c1c45
Step 6/8 : RUN apk -U add ca-certificates
 ---> Using cache
 ---> f3f9098b26cc
Step 7/8 : COPY --from=builder /app /app
 ---> Using cache
 ---> a50457e1accb
Step 8/8 : CMD ["/app"]
 ---> Using cache
 ---> a5632d7b7c9e
Successfully built a5632d7b7c9e
Successfully tagged ubuntu-bionic:8082/hello-world:v5
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Push)
[Pipeline] sh
+ docker login ubuntu-bionic:8082 -u admin -p 45646
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /var/lib/jenkins/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
+ docker push ubuntu-bionic:8082/hello-world:v5
The push refers to repository [ubuntu-bionic:8082/hello-world]
8b239ec40835: Preparing
4ae982a4bf07: Preparing
bb01bd7e32b5: Preparing
4ae982a4bf07: Layer already exists
8b239ec40835: Layer already exists
bb01bd7e32b5: Layer already exists
v5: digest: sha256:089ad98cb41a79cd148b655aa8020774e01e46409fd2d6a958f7cb5476c9302a size: 950
+ docker logout
Removing login credentials for https://index.docker.io/v1/
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS



Задание 3

Что нужно сделать:

    Установите на машину Nexus.

Nexus установлен




    Создайте raw-hosted репозиторий.

Создан репозиторий


    Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.



    Загрузите файл в репозиторий с помощью jenkins.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.
