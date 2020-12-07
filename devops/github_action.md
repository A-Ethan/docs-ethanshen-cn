# Github Action

## 本文档自动流水线

git -> github -> github action -> aliyun oss -> cdn*

```yaml
name: publish_build_gitbook_image
on: 
  push:
    branches:
    - main
    paths:
    - 'SUMMARY.md' # 目录
    - 'book.json' # 插件
    - '.github/workflows/publish_build_gitbook_image.yaml' # 流水线
  pull_request:
    branches:
    - main
    paths:
    - 'SUMMARY.md'
    - 'book.json'
    - '.github/workflows/publish_build_gitbook_image.yaml'
jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
    # 切代码到 runner
    - name: checkout action
      uses: actions/checkout@v2
    # 使用 node:10
    - name: use Node.js 10.x
      uses: actions/setup-node@v1
      with:
        node-version: 10.x
    # git clone
    # - uses: srt32/git-actions@v0.0.3
    #   with:
    #     # git clone 
    #     # 貌似和gitlab-ci不同，需要clone
    #     args: git clone https://github.com/A-Ethan/docs-huncloud-cn.git
    # cd 
    # - name: cd ./docs-huncloud-cn
    #   run: cd ./docs-huncloud-cn
    # - name: pwd
    #   run: pwd
    # npm install gitbook
    - name: npm install gitbook
      run: npm install -g gitbook-cli
    # gitbook install & build
    - name: gitbook install
      run: gitbook install
    - name: gitbook build
      run: gitbook build ./ ./docs
    # 设置阿里云OSS的 id/secret，存储到 github 的 secrets 中
    - name: setup aliyun oss
      uses: A-Ethan/setup-ossutil@master
      with:
        endpoint: oss-cn-shanghai.aliyuncs.com
        access-key-id: ${{ secrets.OSS_KEY_ID }}
        access-key-secret: ${{ secrets.OSS_KEY_SECRET }}
    - name: cp files to aliyun
      run: ossutil cp -rf ./docs/ oss://docs-huncloud-cn/
```

