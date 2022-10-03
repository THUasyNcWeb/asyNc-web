# Git 使用规范

> 本规范应用于 2022-2023秋季学期软件工程课堂 asyNc 仓库

## 统一约定

1. 默认主分支为 `master`。
  
2. 初步设定以下子模块：
  
  - `asyNc-frontend`
  - `asyNc-backend`
  - `asyNc-crawler`
  - ...

3. `master` 与长期分支合并时需使用 `--no-ff` 参数，即不得使用 `fast-forward` 合并策略。

    每个分支应实现单一特性或其工作，分支以功能命名，例如 `identifyUserInfo`；

4. 提交时需严格遵守 **Git Commit 规范**。

5. 谨慎使用 `git add .` 以避免提交不必要的文件，如有需要请及时更新 `.gitignore`。
  
## Git Commit 规范

模板：

```shell
<gitmoji> <type>[(scope)]: <description>
<BLANK LINE>
[body]
<BLANK LINE>
[footer]
```

主题不得超过 `50` 个字符，正文每行不得超过 `100` 个字符；

每次 `commit` 应只做一件事情；

\<gitmoji\>:
- 参考 [gitmoji](https://gitmoji.dev/)
- 注意以下 gitmoji 不能使用：
  - :camera_flash:
  - :adhesive_bandage:
  - :monocle_face:
  - :test_tube:
  - :stethoscope:
  - :bricks:
  - :technologist:
  - :thread:

\<type\>:
- fix: 修复一个 bug
- feat: 引入一个新的功能特性
- build: 影响构建系统或外部依赖项的更改 (例如作用于 npm)
- docs: 仅修改了文档
- ci: 对 CI 配置文件和脚本的更改
- perf: 优化相关，提高性能/体验
- refactor: 既不改变功能又不修复 bug 的代码改变
- style: 改变不影响代码含义的内容 (空格，格式化，分号.......)
- test: 添加/修改测试文件
- chore: 改变构建流程，或者添加依赖库、工具等
- revert: 回滚到上一个版本

[(Scope)]:
- 用来提供附加信息，例如 (parse) 等

\<description\>:
- 使用祈使句，使用现代时
- 第一个字母不得大写
- 不要在最后加 . (句号)

[body]:
- 使用祈使句，使用现代时
- 应包括
  - 对该提交更改的动机(motivation)
  - 与以前的行为的对比

[footer]:
- BREAKING CHANGE: ...
- 应包含关闭的 issue 的编号

参考资料:
- [angular/CONTRIBUTING.md](https://github.com/angular/angular/blob/22b96b96902e1a42ee8c5e807720424abad3082a/CONTRIBUTING.md#-commit-message-guidelines)
- [Git Commit Message Conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#)