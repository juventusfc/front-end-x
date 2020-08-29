# 工具链

工具链是将我们在开发过程中用到的工具链接在一起，让我们开发更顺畅。从开发的各个阶段来看，各阶段工具可分为：

- init 阶段的工具

  - yeoman
  - create-react-app
  - vue-cli
  - angular-cli

- dev 阶段的工具

  - Server

    - build

      - webpack
      - babel
      - vue-compiler-sfc
      - jsx
      - postcss

    - watch

      - fsevent： 不支持 window
      - chokidar： 支持 windows

    - mock
    - http

      - ws
        - webpack-dev-server
      - wireshark

  - Client

    - debugger

      - vscode debugger
      - chrome devtool

    - sourcemap

- test 阶段的工具

  - jest
  - mocha

- publish 阶段的工具

  - lint
  - jenkins
