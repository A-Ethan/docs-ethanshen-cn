# Gitlab CICD

## 小流程

[.gitlab-ci.yaml reference](https://docs.gitlab.com/ee/ci/yaml/README.html)


### 开发环境

```
lintTest \
          |---> build ---> deployTest 自动
unitTest /
```

### 灰度/生产

```
lintTest \                  / deployPre # 自动
          |---> build ---> |
unitTest /                  \ deployPro # 手动
```

## .gitlab-ci.yaml

如果这个docs托管至gitlab，有空试一试。

> 跑容器的，接好k8s runner

```yaml
# stage 执行步骤
stages:
  - test
  - build
  - deploy

# job 使用lint来进行代码静态检查，待补充
lintTest:
  stage: test
  script: echo "Running lint tests"

# job 单元测试
unitTest:
  stage: test
  script: echo "Running unit tests"

# job 构建gitbook
buildDocs:
  stage: build
  only:
    refs:
      - master # release
    changes:
      - 'SUMMARY.md'
      - 'book.json'
      # 修改流水线
      - '.gitlab-ci.yaml'
  # 指定镜像 nodejs
  image: node:15.3.0-alpine3.12
  before_script:
    - npm install -g gitbook-cli
    - echo "Install gitbook finish"
  script: 
    - cd ./docs-huncloud-cn
    - gitbook install && gitbook build ./ ./docs
    - echo "gitbook build docs finish"
  # job 输出工件
  artifacts:
    when: on_success
    name: "$CI_JOB_NAME-$CI_COMMIT_SHORT_SHA"
    expire_in: 1 hrs
    paths: 
      - docs/

# job 部署测试
deployTest:
  stage: deploy
  only:
    refs:
      - dev
  # 指定镜像 centos
  image: centos:centos7.9.2009
  script:
    - echo "Test deploy the app finish"

# job 部署灰度
deployPre:
  stage: deploy
  only:
    refs:
      - master # release
  # 指定镜像 centos
  image: centos:centos7.9.2009
  script:
    - echo "Canary deploy the app finish"

# job 部署生产
deployPro:
  stage: deploy
  only:
    refs:
      - master # release
    changes:
      - 'SUMMARY.md'
      - 'book.json'
      # 修改流水线
      - '.gitlab-ci.yaml'
  # 手动触发
  when: manual
  # 指定镜像  centos
  image: centos:centos7.9.2009
  before_script:
    - wget http://gosspublic.alicdn.com/ossutil/1.7.0/ossutil64
    - chmod 755 ossutil64
    - echo "install ossutil64 finish"
  script:
     # 引变量
    - ./ossutil64 cp -rf ./docs/ oss://docs2-huncloud-cn \
    --endpoint=oss-cn-shanghai.aliyuncs.com \
    --access-key-id=$secrets_OSS_KEY_ID \
    --access-key-secret=$secrets_OSS_KEY_SECRET
    - echo "Pro deploy the app finish"
  dependencies: 
    # 引用 job buildDocs.artifacts
    - buildDocs
```

## 变量添加

settings -> CI/CD -> Variables