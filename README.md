# mp-ci-demo

![Node.js CI](https://github.com/ineo6/mp-ci-demo/workflows/Node.js%20CI/badge.svg)

使用`GitHub Actions`、`mp-ci`为`Taro`项目添加支持集成。


## GitHub Actions 配置

新建`.github/workflows/build.yaml`，也可以在`Actions`一栏，选择`Node.js`

```yaml
# This workflow will do a clean install of node dependencies, build the source code and run tests across different versions of node
# For more information see: https://help.github.com/actions/language-and-framework-guides/using-nodejs-with-github-actions

name: Node.js CI

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [10.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ matrix.node-version }}

    - name: install Dependencies
      run: npm i

    - name: build weapp
      run: npm run build:weapp

    # see Project/Settings/Secrets
    - name: generate pkp
      run: echo "$UPLOAD_PRIVATE_KEY" > private.key
      env:
        UPLOAD_PRIVATE_KEY: ${{ secrets.UPLOAD_PRIVATE_KEY }}

    - name: upload
      run: npx mp-ci upload ./ --pkp=./private.key --no-test
```
