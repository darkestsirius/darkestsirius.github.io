---
title: "Vscode配置c++开发环境"
date: 2022-01-26T21:04:47+08:00
---

## 前言

笔者在为`visual studio code`配置clang-llvm相关插件时遇到诸多问题，历经折腾，才配置到令人满意的程度。考虑到可能有人会遇到同样的问题，故分享我自己的配置过程

* `Clang`是`llvm`项目针对c/c++语言开发的编译器前端，关于`llvm`项目，请自行Google。
* 因为Clang配套C++标准库`libc++`不支持Windows， Windows用户有以下几个选择：
  * 启用`WSL（Windows subsystem for Linux，即Windows上的Linux子系统）`，需要注意wsl2本质上是基于`hyper-v`的虚拟机。在Windows上安装vs code，在WSL2中安装开发工具（clang+clangd+lldb），并通过VS code远程开发功能进行开发。安装WSL2后的安装配置方案与Linux一致。***笔者推荐此方案***
  * 配合其它标准库使用，例如通过`MSYS2`。详情请见[此文](https://blog.csdn.net/tyKuGengty/article/details/120119820)
* 本文面向小白，但不会做到巨细靡遗，不会给出CMake等的详细教程，更具体的内容请自行搜索（善用搜索引擎）。也可在评论区提问，笔者尽力回答
* 本文编程环境配置依靠完整的llvm工具链，用`clangd`提供`intellisence`功能，lldb提供debug功能，以替代微软官方的c/cpp插件（话说微软提出LSP的概念结果自家插件搞得这么差，巨硬不愧是你hhh）。
* 关于`LSP(language-support-Protocal)`的概念,

## 为什么要使用llvm工具链？

* 更优秀的语法高亮，与`Dracula Refined`主题配合相得益彰
* 更优秀的性能，intellisence和代码自动补全更迅速
* Clang-Tidy提供了强大的「静态检查」支持，并对于部分代码提供「快速修复」。具体请见Clang-Tidy Checks。这里笔者主要添加了对于「Google 开源项目风格指南(有中文版，但翻译版本滞后，需注意)」「Cpp Core Guidelines」和性能、潜在的bug、移植性、现代C++的检查
* 自动添加头文件。对于建议中的项会即时自动添加头文件，导航条会自动下移（观察行号）隐藏添加的头文件
* 更加精准的「功能」、「转到定义」、「重命名符号」等功能

## 安装visual studio code

MacOS和Windows在官网下载安装包即可，linux请用自带的包管理器进行安装

注意：因为Visual-Studio-Code*不完全开源*某些linux发行版包管理器的官方源只能安装*VSCodium*或*Code-OSS*，需要从社区源进行安装

这里以Arch Linxu为例,打开终端，输入如下命令

```zsh
yay -S visual-studio-code-bin # 安装AUR中打包好的二进制版
```

或者安装*VSCodium*或*Code-OSS*

```zsh
sudo pacman -S code # 从官方源码编译vscode
sudo pacman -S vscodium-bin # 安装archlinuxcn源中的vscodium
```

更多内容参见[ArchWiki](https://wiki.archlinux.org/title/Visual_Studio_Code_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

## 安装clang-llvm工具链

### MacOS

通过包管理工具`homebrew`安装llvm工具链（这是clangd官方文档中的方案）

```zsh
brew install llvm # 需要先安装好homebrew
```

之后会提示你添加环境变量，只需在终端输入以下命令：

```zsh
echo 'export PATH="/usr/local/opt/llvm/bin:$PATH"' >> ~/.zshrc
echo 'export LDFLAGS="-L/usr/local/opt/llvm/lib"' >> ~/.zshrc
echo 'export CPPFLAGS="-I/usr/local/opt/llvm/include"' >> ~/.zshrc
```

可以看出，homebrew安装的llvm路径位于在`/usr/local/opt/llvm`目录

### Arch Linux

Arch-Linxu：

```zsh
sudo pacman -S llvm clang lld lldb libc++
```

## 验证clang是否安装成功

终端中键入

```zsh
clang --version
clangd --version
lldb --version
```

会输出相关工具的版本号

在终端中键入

```zsh
which clang
which clangd 
which lldb 
```

会输出工具的安装路径

## 安装vscode插件

刚打开VScode，会提醒你安装中文语言包，可以根据自己的需求选择是否安装

然后安装下列拓展：

* clangd
* CodeLLDB
* CMake
* CMake Tools
* cmake-format
* Code Runner（可选）
* Better C++ Syntax（可选）
* Dracula Refined（一个很经美观的主题插件，可选）
* GitLens — Git supercharged（一个非常强大的git插件，可选）
* Material Icon Theme (一个很美观的图标主题)

![0](VSCode配置C++环境最佳实践/0.png)

点击安装Codelldb插件后，该插件会自动下载一个扩展包，这个package大概率下载失败（不可描述的网络原因）

如果下载失败，vsc会弹窗，提示你从对应链接下载，可以在浏览器中通过相关链接下载(如果还是下载失败，多试几次就行)，下载完成后是一个拓展名为.vsix的文件，然后在vscode插件页面选择 `从vsix安装（install from VSIX）`即可

## 配置`settings.json`文件

打开全局的`settings.json`文件(通过`Fn+F1`或`ctrl/command+shift+p`快捷键打开控制面板，然后输入并执行 `Preferences: Open Settings(JSON)`命令)，即可打开`settings.json`配置文件。笔者倾向于将尽可能多的设置放在`settings.json`配置文件中，以便于用账号同步

我们可以看到，配置行之间以英语输入法下的逗号`,`间隔，整个配置文件之外还会有一对大花括号，我们可以将鼠标停在某行设置上，以查看这条配置的说明。

接下来贴出笔者VSCode的通用配置和各插件的配置及其注释，你可以有选择地粘贴到自己的`settings.json`中。

### Vscode通用设置

```json
// 禁用VScode远程报告
    "telemetry.enableTelemetry": false,
    // 主题
    "workbench.colorTheme": "Dracula Refined",
    // 图标主题
    "workbench.iconTheme": "vscode-icons",
    // 在没有从上一个会话恢复出信息的情况下，在启动时不打开编辑器
    "workbench.startupEditor": "none",
    // 显示视图头部的操作项
    "workbench.view.alwaysShowHeaderActions": true,
    // 删除文件确认
    "explorer.confirmDelete": false,
    // 控制是否显示缩略图cod
    "editor.minimap.enabled": true,
    // 自动保存
    "files.autoSave": "afterDelay",
    // 控制编辑器在空白字符上显示符号的方式
    "editor.renderWhitespace": "none",
    // 代码片段建议置于其他建议之上
    "editor.snippetSuggestions": "top",
    // 使用空格缩进时模拟制表符的行为，可以方便对齐
    "editor.stickyTabStops": true,
    // 建议的接受方式
    "editor.suggest.insertMode": "replace",
    // 控制排序时是否提高靠近光标的词语的优先级
    "editor.suggest.localityBonus": true,
    "editor.suggest.shareSuggestSelections": true,
    // 控制建议小部件底部的状态栏可见
    "editor.suggest.showStatusBar": true,
    // 控制在键入触发字符后是否自动显示建议
    "editor.suggestOnTriggerCharacters": true,
    // 始终预先选择第一个建议
    "editor.suggestSelection": "first",
    // 控制是否根据文档中的文字提供建议列表
    "editor.wordBasedSuggestions": true,
    // 粘贴同名文件时的重命名方式
    // smart: 在重复名称末尾智能地添加/递增数字
    "explorer.incrementalNaming": "smart",
    // 配置排除的文件和文件夹的glob模式
    // 文件资源管理器将根据此设置决定要显示或隐藏的文件和文件夹
    "files.exclude": {
        "**/.classpath": true,
        "**/.factorypath": true,
        "**/.project": true,
        "**/.settings": true
    },
    // 在会话间记住未保存的文件，以允许在退出编辑器时跳过保存提示
    // onExitAndWindowClose: 退出或窗口关闭时
    "files.hotExit": "onExitAndWindowClose",
    // Grunt 任务自动检测
    "grunt.autoDetect": "on",
    // Gulp 任务自动检测
    "gulp.autoDetect": "on",
    // 应该在何处显示单元格工具栏，或是否隐藏它
    "notebook.cellToolbarLocation": {
        // 默认: 右边
        "default": "right",
        // jupyter-notebook: 左边
        "jupyter-notebook": "left"
    },
    // 控制单元格编辑器中行号的显示
    "notebook.lineNumbers": "on",
    // 配置在搜索中排除的文件和文件夹的glob模式
    "search.exclude": {
        // "someFolder/": true,
        // "somefile": true
    },
    // 显示搜索结果所在行号
    "search.showLineNumbers": true,
    // 当搜索词为小写时，则不区分大小写进行搜索，否则区分大小写
    "search.smartCase": true,
    // 集成终端启用视觉化铃声
    "terminal.integrated.enableBell": true,
    // 集成终端编码: zh_CN.UTF-8
    "terminal.integrated.env.windows": {
        "LC_ALL": "zh_CN.UTF-8"
    },
    // 集成终端使用GPU加速
    "terminal.integrated.gpuAcceleration": "on",
    // 集成终端右击时选择光标下方的字词，并打开上下文菜单
    "terminal.integrated.rightClickBehavior": "selectWord",
    // 控制编辑器应当自动改写左引号或右引号
    "editor.autoClosingOvertype": "always",
    // 禁用自动检测文件缩进模式和缩进大小，即打开文件后自动将文件更改为 VSCode 配置的缩进格式
    "editor.detectIndentation": false,
    // 控制是否应在调试控制台中输入时接受建议; enter 还用于评估调试控制台中键入的任何内容
    "debug.console.acceptSuggestionOnEnter": "on",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    // 控制是否显示缩略图cod
    "editor.quickSuggestions": {
        // 键入注释时不允许
        "comments": false,
        // 键入其他时允许
        "other": true,
        // 键入字符串时不允许
        "strings": false
    },
    // 控制显示快速建议前的等待时间（毫秒）
    "editor.quickSuggestionsDelay": 0,
    // 在编辑器中自动显示内联建议
    "editor.inlineSuggest.enabled": true,
    // 是否在输入时显示含有参数文档和类型信息的小面板
    "editor.parameterHints.enabled": true,
    "[log]": {
        "editor.fontSize": 16
    },
    "vsicons.dontShowNewVersionMessage": true,
    "security.workspace.trust.untrustedFiles": "open",
```

### clangd

```json
// Clangd 运行参数(在终端/命令行输入 clangd --help-list-hidden 可查看更多)
    "clangd.onConfigChanged": "restart",
    // 自动检测 clangd 更新
    "clangd.checkUpdates": true,
    // clangd的snippets有很多的跳转点，不用这个就必须手动触发Intellisense了
    "editor.suggest.snippetsPreventQuickSuggestions": false,
    // clangd路径，通过终端命令 which clangd 命令得到
    "clangd.path": "/usr/local/opt/llvm/bin/clangd",
    "clangd.arguments": [
        // 全局补全(输入时弹出的建议将会提供 CMakeLists.txt 里配置的所有文件中可能的符号，会自动补充头文件)
        "--all-scopes-completion",
        // 启用 Clang-Tidy功能以提供「静态检查」
        "--clang-tidy",
        // 标记compelie_commands.json 文件的目录位置(相对于工作区，由于 CMake 生成的该文件默认在 build 文件夹中，故设置为 build)
        "--compile-commands-dir=build",
        // 建议风格 打包(重载函数只会给出一个建议）；还可以设置为detailed
        "--completion-style=bundled",
        "--enable-config",
        // 默认格式化风格: 谷歌开源项目代码指南（可用的有 LLVM, Google, Chromium, Mozilla, Webkit, Microsoft, GNU 等）
        "--fallback-style=Google",
        // 启用这项时，补全函数时，将会给参数提供占位符，键入后按 Tab 可以切换到下一占位符，乃至函数末。我选择启用
        "--function-arg-placeholders=true",
        // 输入建议中，已包含头文件的项与还未包含头文件的项会以圆点加以区分
        "--header-insertion-decorators",
        // 允许补充头文件
        "--header-insertion=iwyu",
        // 让 Clangd 生成更详细的日志
        "--log=verbose",
        // pch优化的位置(memory 或 disk，选择memory会增加内存开销，但会提升性能)
        "--pch-storage=memory",
        // 输出的 JSON 文件更美观
        "--pretty",
        // 同时开启的任务数量
        "-j=12",
        // 在后台自动分析文件（基于complie_commands)
        "--background-index",
        // 告诉clangd用那个clang进行编译，路径参考where clang++的路径
        "--query-driver=/usr/local/opt/llvm/bin/clang++"
    ],
```

除此之外，根据[clangd官方文档](https://clangd.llvm.org/config.html)，还可以添加用户（全局）配置

clangd通过 YAML 配置文件读取更详细的用户（全局）和单个项目的配置，单个项目的配置在一个位于该项目根目录的名为`.clangd`的文件中，clangd通过 YAML 配置文件读取用户（全局）配置和单个项目的配置

用户（全局）配置在如下路径的 `clangd/config.yaml`文件中

* Windows: `%USERPROFILE%\AppData\Local`
* Mac OS: `~/Library/Preferences/`
* Others: `$XDG_CONFIG_HOME, usually ~/.config`

以macOS为例：在终端中输入如下命令，在`~/Library/Preferences/`路径中创建`clangd/config.yaml`文件

```zsh
cd ~/Library/Preferences
mkdir clangd
cd clangd
touch config.yaml
```

然后用编辑该文件,增加以下配置

```yaml
Diagnostics:
  ClangTidy:
    Add: ["*"]
    Remove:
      [
        abseil*,
        fuchsia*,
        llvmlib*,
        zircon*,
        google-readability-todo,
        readability-braces-around-statements,
        hicpp-braces-around-statements,
      ]
Index:
  Background: Build
```

### LLDB

```json
  // LLDB 指令自动补全
  "lldb.commandCompletions": true,
  // LLDB 指针显示解引用内容
  "lldb.dereferencePointers": true,
  // LLDB 鼠标悬停在变量上时预览变量值
  "lldb.evaluateForHovers": true,
  // LLDB 监视表达式的默认类型
  "lldb.launch.expressions": "simple",
  // LLDB 不显示汇编代码
  "lldb.showDisassembly": "never",
  // LLDB 生成更详细的日志
  "lldb.verboseLogging": true
```

### CMake

```json
// 保存 cmake.sourceDirectory 或 CMakeLists.txt 内容时，不自动配置 CMake 项目目录
    "cmake.configureOnEdit": false,
    // 在 CMake 项目目录打开时自动对其进行配置
    "cmake.configureOnOpen": true,
    // 成功配置后，将 compile_commands.json 复制到此位置
    "cmake.copyCompileCommands": ""
```

### git

```json
// 自动从当前 Git 存储库的默认远程库提取提交
    "git.autofetch": false,
    // 同步 Git 存储库前确认
    "git.confirmSync": false,
    // 没有暂存的更改时，直接提交全部更改
    "git.enableSmartCommit": true
```

### 语法高亮设置

```json
// 控制是否对括号着色
    "editor.bracketPairColorization.enabled": true,
    // 启用括号指导线
    "editor.guides.bracketPairs": true,
    // 语义高亮
    "editor.semanticHighlighting.enabled": true,
    // 自定义语法高亮
    "editor.semanticTokenColorCustomizations": {
        "enabled": true,
        "rules": {
            // 抽象符号
            "*.abstract": {
                "fontStyle": "italic"
            },
            // 函数参数
            "parameter": "#306b72",
            // 类或者结构体
            "class": {
                "fontStyle": "bold",
                "foreground": "#729de3"
            },
            // 普通函数
            "function": {
                "foreground": "#e5b124"
            },
            // 临时变量
            "variable": "#26cdca",
            // enum的名字（enum的成员似乎并没有可以配置的）
            "enum": "#397797",
            // enum子项，需要clangd12以上
            "enumMember": "#397797",
            // 宏
            "macro": {
                "foreground": "#8f5daf",
                "fontStyle": "bold"
            },
            // 成员函数
            "method": {
                "foreground": "#e5b124",
                "fontStyle": "underline"
            },
            // clangd12之后会将宏关闭的部分标为comment
            "comment": "#505050",
            // 命名空间
            "namespace": {
                "foreground": "#00d780",
                "fontStyle": "bold"
            },
            // 只读量加粗
            "*.readonly": {
                "fontStyle": "bold"
            },
            // 只读量等效为宏
            "variable.readonly": {
                "foreground": "#8f5daf",
                "fontStyle": "bold"
            },
            // 静态量（静态变量，静态函数）
            "*.static": {
                "fontStyle": "italic"
            },
            // 成员变量，似乎需要clangd12以上
            "property": {
                "foreground": "#7ca6b7",
                "fontStyle": "underline"
            }
        }
    },
```

### 字体设置(可选)

```json
// 输出窗口
    "[Log]": {
        // 字体大小
        "editor.fontSize": 15
    },
    //设置编辑区字体
    "editor.formatOnPaste": true,
    "editor.formatOnSave": true,
    "editor.formatOnType": true,
    "editor.insertSpaces": true,
    "files.autoSave": "afterDelay",
    "workbench.settings.editor": "json",
    "extensions.ignoreRecommendations": true,
    "explorer.confirmDragAndDrop": false,
    // 字体大小
    "editor.fontSize": 14,
    // 集成终端字体大小
    "terminal.integrated.fontSize": 14,
    "editor.fontFamily": "Operator Mono",
    "editor.fontLigatures": true, // 这个控制是否启用字体连字，true启用，false不启用
    "editor.tokenColorCustomizations": {
        "textMateRules": [
            {
                "name": "italic font",
                "scope": [
                    "comment",
                    "keyword",
                    "storage",
                    "keyword.control.import",
                    "keyword.control.default",
                    "keyword.control.from",
                    "keyword.operator.new",
                    "keyword.control.export",
                    "keyword.control.flow",
                    "storage.type.class",
                    "storage.type.function",
                    "storage.type",
                    "storage.type.class",
                    "variable.language",
                    "variable.language.super",
                    "variable.language.this",
                    "meta.class",
                    "meta.var.expr",
                    "constant.language.null",
                    "support.type.primitive",
                    "entity.name.method.js",
                    "entity.other.attribute-name",
                    "punctuation.definition.comment",
                    "text.html.basic entity.other.attribute-name.html",
                    "text.html.basic entity.other.attribute-name",
                    "tag.decorator.js entity.name.tag.js",
                    "tag.decorator.js punctuation.definition.tag.js",
                    "source.js constant.other.object.key.js string.unquoted.label.js",
                ],
                "settings": {
                    "fontStyle": "italic",
                }
            },
        ]
    },
    "terminal.integrated.fontFamily": "JetBrainsMonoMedium NF" // 集成终端字体设置
```

关于上面配置的 `operator mono字体`，详见[这篇博客](https://blog.csdn.net/zgd826237710/article/details/94137781)

### code-runner(可选)

```json
"code-runner.runInTerminal": true, // 设置成false会在“输出”中输出，无法输入
    "code-runner.saveFileBeforeRun": true, // run code前保存
    "code-runner.preserveFocus": false, // 若为false，run code后光标会聚焦到终端上。如果需要频繁输入数据可设为false
    "code-runner.clearPreviousOutput": true, // 每次run code前清空属于code runner的终端消息，默认false
    "code-runner.ignoreSelection": true, // 默认为false，效果是鼠标选中一块代码后可以单独执行，但C是编译型语言，不适合这样用
    "code-runner.fileDirectoryAsCwd": true, // 将code runner终端的工作目录切换到文件目录再运行，对依赖cwd的程序产生影响；如果为false，executorMap要加cd $dir
    "code-runner.executorMap": {
        "c": "/usr/local/opt/llvm/bin/clang $fileName -o $workspaceRoot/build/debug.out -g -Wall -Wextra -pthread -fuse-ld=lld -fstandalone-debug -fcolor-diagnostics -fparse-all-comments -stdlib=libc++ -std=c11 && $workspaceRoot/build/debug.out",
        "cpp": "/usr/local/opt/llvm/bin/clang++ $fileName -o $workspaceRoot/build/debug.out -g -Wall -Wextra -pthread -fuse-ld=lld -fstandalone-debug -fcolor-diagnostics -fparse-all-comments -stdlib=libc++ -std=c++20 && $workspaceRoot/build/debug.out"
    },
```

这样设置，点击右上角的运行符号，code-runner插件会编译当前打开的***单个***cpp文件，并在工作空间根目录的`build`文件夹中生成可执行文件`debug.out`

对于多文件编译，请见下面的配置

## 建立代码目录结构

因为本文主要面向新手，所以配置的是“学习环境”，而非大型c/cpp工程的“开发环境”，文件结构相对简单，对于代码结构复杂的项目，请学习使用CMake。

实际开发环境和学习环境有什么区别呢？想想你写第一个hello world程序时是怎样写的，你只写了一个`hello world.cpp`文件，然后点击IDE的绿色三角编译运行，生成`.exe`或`.out`可执行文件，语言学习环境开始大都是这样的单文件编译运行调试，或者是涉及到简单的几个头文件和源文件的组合这样的多文件结构。而实际项目的目录结构往往非常复杂，甚至会出现多个工程项目组合在一起形成一个“解决方案”（visual studio）

我们的学习环境目录结构应该便于建立一个新的源文件，然后编译、运行和调试

笔者的解决方案：找一个合适的位置，新建一个名为code的文件夹用来存放你的代码（⚠️：无论Windows还是Linux还是osx，文件夹名称和文件夹路径尽量不要含有中文，否则你也不知道什么时候会踩雷）

然后在code文件夹中新建一个`cpp`文件夹用来保存你写的c++程序（其它编程语言同理，名称可以根据个人喜好更改，不建议含有大写字母、中文和空格）

在`VSCode`中选择`File`-`add folder to workspace...`，将`cpp`文件夹添加进来，此后我们可以通过`file`-`save workspace as...`来保存该工作区以便后序使用以及单独配置、安装插件等

在`cpp`文件夹中，可以按照自己的需求创建存放源代码的文件夹，比如你是个学生，正在学习《c++ primer》，可以新建一个`practice`文件夹，里面新建`cpp_primer`子文件夹,用于存放习题代码；刷oj，新建一个`online-judge`文件夹；用c++刷力扣，新建个`leetcode`文件夹，等等...每个文件夹都可以按照自己的需求再创建子文件夹

上述 `practice/online-judge/leetcode`文件夹中的源代码文件，编译后会生成后缀为.out的可执行文件（win上是.exe），在macOS上的llvm还会生成一个后缀为.out.dSYM的调试文件夹，如果堆放在一起，会显得非常混乱，所以在`cpp`文件夹中新建一个`build`文件夹，按照下面笔者的相关配置，每次编译/调试时会自动在`build`文件夹内生成可执行文件及调试文件夹

## 配置json文件

在`cpp`文件夹中新建名为`.vscode`的文件夹,在`.vscode`文件夹中，新建`launch.json`文件、`tasks.json`文件和`settings.json`文件

编辑文件内容为：

launch.json：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            // 配置名称，将会在启动配置的下拉菜单中显示
            "name": "LLDB: 编译和调试单文件", 
            // 配置类型，不同编程语言不同,该项由CodeLLDB插件提供
            "type": "lldb", 
            // 可以为launch（启动）或attach（附加） 
            "request": "launch", 
            // 将要进行调试的程序的路径
            "program": "${workspaceFolder}/build/debug.out",
            "args": [], 
            "stopOnEntry": false, 
            // 调试程序时的工作目录，此为工作区文件夹；改成${fileDirname}可变为文件所在目录
            "cwd": "${fileDirname}", 
            "externalConsole": false, 
            "internalConsoleOptions": "neverOpen", 
            "MIMode": "lldb", 
            "miDebuggerPath": "/usr/local/opt/llvm/bin/lldb", 
            "setupCommands": [
                { 
                    "description": "Enable pretty-printing for lldb", 
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ],
            // 指定调试前要运行的task，与tasks.json中的label对应
            "preLaunchTask": "Clang++: 编译单文件" 
        },
        {
            "name": "LLDB: 编译和调试多文件",
            "type": "lldb", 
            "request": "launch",
            "program": "${workspaceFolder}/build/debug.out",
            "args": [],
            "stopOnEntry": false,
            "cwd": "${fileDirname}",
            "externalConsole": false,
            "internalConsoleOptions": "neverOpen",
            "MIMode": "lldb",
            "miDebuggerPath": "/usr/local/opt/llvm/bin/lldb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for lldb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ],
            "preLaunchTask": "Clang++: 编译多文件"
        },
        {
            "name": "LLDB: 调试已编译的可执行文件",
            "type": "lldb",
            "request": "launch",
            "program": "${workspaceFolder}/build/debug.out",
            "args": [],
            "stopOnEntry": false,
            "cwd": "${fileDirname}",
            "internalConsoleOptions": "neverOpen",
            "environment": [],
            "externalConsole": false,
        }
    ]
}
```

tasks.json：

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            // 任务名称，与launch.json的preLaunchTask相对应
            "label": "Clang++: 编译单文件", 
            // 可以为process或shell
            "type": "shell", 
            // 要使用的编译器路径，C语言用clang
            "command": "/usr/local/opt/llvm/bin/clang++", 
            // 编译器后附的参数，反映在终端里即"clang+ arg1 arg2 arg3..."
            "args": [
                // 要编译的文件
                "${file}",
                // 指定输出文件名极其路径，os X和Linux下不加该参数则默认输出a.out，win下默认a.exe
                "-o", 
                "${workspaceFolder}/build/debug.out", 
                // 生成和调试有关的信息, 如果加上该参数，编译后不仅会生成.out可执行文件，还会生成后缀为.out.dSYM的文件夹，如果不加，调试器会忽略你设的断点
                "-g",
                // 开启额外警告
                "-Wall", 
                "-Wextra",
                // 多线程支持
                "-pthread",
                // 使用 LLVM lld 链接器而不是默认链接器
                "-fuse-ld=lld",
                // 启用 debug 信息优化
                "-fstandalone-debug",
                // 诊断信息着色
                "-fcolor-diagnostics",
                // 分析所有注释(这其实只需告诉 Clangd ，即添加到 compile_commands.json 中)
                // Clang 默认只分析 Doxygen 风格("/**", "///"开头)的注释
                "-fparse-all-comments",
                // 静态链接libc++，Linux下似乎会出错，出错的话删掉这行就行
                "-stdlib=libc++", 
                // 这里采用c++20标准，C语言改成"-std=c11"
                "-std=c++20", 
            ], // 编译的命令，其实相当于VSC帮你在终端中输了这一串东西
            "group": {
                "kind": "build",
                "isDefault": true // 不为true时command shift B快捷键就要手动选择了
            },
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "presentation": {
                "echo": true,
                // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档，即使设为never，手动点进去还是可以看到
                "reveal": "always", 
                // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
                "focus": false, 
                // 不同的文件的编译信息共享一个终端面板
                "panel": "shared" 
            },
            "detail": "Clang++: 编译单个文件"
        },
        {
            // 任务名称，与launch.json的preLaunchTask相对应
            "label": "Clang++: 编译多文件",
            // 可以为process或shell
            "type": "shell",
            // 要使用的编译器路径，C语言用clang
            "command": "/usr/local/opt/llvm/bin/clang++",
            // 编译器后附的参数，反映在终端里即"clang+ arg1 arg2 arg3..."
            "args": [
                // 要编译的文件,*是通配符，是编译多文件的关键
                "${fileDirname}/*.cpp",
                // 指定输出文件名极其路径，os X和Linux下不加该参数则默认输出a.out，win下默认a.exe
                "-o",
                "${workspaceFolder}/build/debug.out",
                // 生成和调试有关的信息, 如果加上该参数，编译后不仅会生成.out可执行文件，还会生成后缀为.out.dSYM的文件夹，如果不加，调试器会忽略你设的断点
                "-g",
                // 开启额外警告
                "-Wall",
                "-Wextra",
                // 多线程支持
                "-pthread",
                // 使用 LLVM lld 链接器而不是默认链接器
                "-fuse-ld=lld",
                // 启用 debug 信息优化
                "-fstandalone-debug",
                // 诊断信息着色
                "-fcolor-diagnostics",
                // 分析所有注释(这其实只需告诉 Clangd ，即添加到 compile_commands.json 中)
                // Clang 默认只分析 Doxygen 风格("/**", "///"开头)的注释
                "-fparse-all-comments",
                // 静态链接libc++，Linux下似乎会出错，出错的话删掉这行就行
                "-stdlib=libc++",
                // 这里采用c++20标准，C语言改成"-std=c11"
                "-std=c++20",
            ], // 编译的命令，其实相当于VSC帮你在终端中输了这一串东西
            "group": {
                "kind": "build",
                "isDefault": true // 不为true时command shift B快捷键就要手动选择了
            },
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "presentation": {
                "echo": true,
                // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档，即使设为never，手动点进去还是可以看到
                "reveal": "always",
                // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
                "focus": false,
                // 不同的文件的编译信息共享一个终端面板
                "panel": "shared"
            },
            "detail": "Clang++: 编译当前文件夹的所有文件"
        }
    ]
}
```

settings.json：

```json
{
    "editor.snippetSuggestions": "top", // （可选）snippets显示在补全列表顶端，默认是inline
    "files.defaultLanguage": "cpp" // ctrl+N新建文件后默认的语言，c语言改成c即可
}
```

然后在vscode打开的工作空间根目录(也就是`cpp`文件夹)中添加`.clang-format`文件(用于自定义格式化c++源代码)，编辑.clang-format文件内容如下（可按照自己的喜好更改）：

```yaml
# 语言: None, Cpp, Java, JavaScript, ObjC, Proto, TableGen, TextProto
Language: Cpp
# BasedOnStyle: LLVM

# 访问说明符(public、private等)的偏移
AccessModifierOffset: -4

# 开括号(开圆括号、开尖括号、开方括号)后的对齐: Align, DontAlign, AlwaysBreak(总是在开括号后换行)
AlignAfterOpenBracket: Align

# 连续赋值时，对齐所有等号
AlignConsecutiveAssignments: false

# 连续声明时，对齐所有声明的变量名
AlignConsecutiveDeclarations: false

# 右对齐逃脱换行(使用反斜杠换行)的反斜杠
AlignEscapedNewlines: Right

# 水平对齐二元和三元表达式的操作数
AlignOperands: true

# 对齐连续的尾随的注释
AlignTrailingComments: true

# 不允许函数声明的所有参数在放在下一行
AllowAllParametersOfDeclarationOnNextLine: false

# 不允许短的块放在同一行
AllowShortBlocksOnASingleLine: true

# 允许短的case标签放在同一行
AllowShortCaseLabelsOnASingleLine: true

# 允许短的函数放在同一行: None, InlineOnly(定义在类中), Empty(空函数), Inline(定义在类中，空函数), All
AllowShortFunctionsOnASingleLine: None

# 允许短的if语句保持在同一行
AllowShortIfStatementsOnASingleLine: true

# 允许短的循环保持在同一行
AllowShortLoopsOnASingleLine: true

# 总是在返回类型后换行: None, All, TopLevel(顶级函数，不包括在类中的函数), 
# AllDefinitions(所有的定义，不包括声明), TopLevelDefinitions(所有的顶级函数的定义)
AlwaysBreakAfterReturnType: None

# 总是在多行string字面量前换行
AlwaysBreakBeforeMultilineStrings: false

# 总是在template声明后换行
AlwaysBreakTemplateDeclarations: true

# false表示函数实参要么都在同一行，要么都各自一行
BinPackArguments: true

# false表示所有形参要么都在同一行，要么都各自一行
BinPackParameters: true

# 大括号换行，只有当BreakBeforeBraces设置为Custom时才有效
BraceWrapping:
  # class定义后面
  AfterClass: false
  # 控制语句后面
  AfterControlStatement: false
  # enum定义后面
  AfterEnum: false
  # 函数定义后面
  AfterFunction: false
  # 命名空间定义后面
  AfterNamespace: false
  # struct定义后面
  AfterStruct: false
  # union定义后面
  AfterUnion: false
  # extern之后
  AfterExternBlock: false
  # catch之前
  BeforeCatch: false
  # else之前
  BeforeElse: false
  # 缩进大括号
  IndentBraces: false
  # 分离空函数
  SplitEmptyFunction: false
  # 分离空语句
  SplitEmptyRecord: false
  # 分离空命名空间
  SplitEmptyNamespace: false

# 在二元运算符前换行: None(在操作符后换行), NonAssignment(在非赋值的操作符前换行), All(在操作符前换行)
BreakBeforeBinaryOperators: NonAssignment

# 在大括号前换行: Attach(始终将大括号附加到周围的上下文), Linux(除函数、命名空间和类定义，与Attach类似), 
#   Mozilla(除枚举、函数、记录定义，与Attach类似), Stroustrup(除函数定义、catch、else，与Attach类似), 
#   Allman(总是在大括号前换行), GNU(总是在大括号前换行，并对于控制语句的大括号增加额外的缩进), WebKit(在函数前换行), Custom
#   注：这里认为语句块也属于函数
BreakBeforeBraces: Custom

# 在三元运算符前换行
BreakBeforeTernaryOperators: false

# 在构造函数的初始化列表的冒号后换行
BreakConstructorInitializers: AfterColon

#BreakInheritanceList: AfterColon

BreakStringLiterals: false

# 每行字符的限制，0表示没有限制
ColumnLimit: 0

CompactNamespaces: true

# 构造函数的初始化列表要么都在同一行，要么都各自一行
ConstructorInitializerAllOnOneLineOrOnePerLine: false

# 构造函数的初始化列表的缩进宽度
ConstructorInitializerIndentWidth: 4

# 延续的行的缩进宽度
ContinuationIndentWidth: 4

# 去除C++11的列表初始化的大括号{后和}前的空格
Cpp11BracedListStyle: true

# 继承最常用的指针和引用的对齐方式
DerivePointerAlignment: false

# 固定命名空间注释
FixNamespaceComments: true

# 缩进case标签
IndentCaseLabels: false

IndentPPDirectives: None

# 缩进宽度
IndentWidth: 4

# 函数返回类型换行时，缩进函数声明或函数定义的函数名
IndentWrappedFunctionNames: false

# 保留在块开始处的空行
KeepEmptyLinesAtTheStartOfBlocks: false

# 连续空行的最大数量
MaxEmptyLinesToKeep: 1

# 命名空间的缩进: None, Inner(缩进嵌套的命名空间中的内容), All
NamespaceIndentation: None

# 指针和引用的对齐: Left, Right, Middle
PointerAlignment: Right

# 允许重新排版注释
ReflowComments: true

# 允许排序#include
SortIncludes: false

# 允许排序 using 声明
SortUsingDeclarations: false

# 在C风格类型转换后添加空格
SpaceAfterCStyleCast: false

# 在Template 关键字后面添加空格
SpaceAfterTemplateKeyword: true

# 在赋值运算符之前添加空格
SpaceBeforeAssignmentOperators: true

# SpaceBeforeCpp11BracedList: true

# SpaceBeforeCtorInitializerColon: true

# SpaceBeforeInheritanceColon: true

# 开圆括号之前添加一个空格: Never, ControlStatements, Always
SpaceBeforeParens: ControlStatements

# SpaceBeforeRangeBasedForLoopColon: true

# 在空的圆括号中添加空格
SpaceInEmptyParentheses: false

# 在尾随的评论前添加的空格数(只适用于//)
SpacesBeforeTrailingComments: 1

# 在尖括号的<后和>前添加空格
SpacesInAngles: false

# 在C风格类型转换的括号中添加空格
SpacesInCStyleCastParentheses: false

# 在容器(ObjC和JavaScript的数组和字典等)字面量中添加空格
SpacesInContainerLiterals: true

# 在圆括号的(后和)前添加空格
SpacesInParentheses: false

# 在方括号的[后和]前添加空格，lamda表达式和未指明大小的数组的声明不受影响
SpacesInSquareBrackets: false

# 标准: Cpp03, Cpp11, Auto
Standard: Cpp11

# tab宽度
TabWidth: 4

# 使用tab字符: Never, ForIndentation, ForContinuationAndIndentation, Always
UseTab: Never
```

## 测试环境

在exercise文件夹中新建hello.cpp源文件（别忘了后缀），输入以下代码：

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main(int argc, char **argv) {
    vector<string> msg{"hello", "C++", "语言", "world", "from", "VS Code"};
    for (const string &word : msg) {
      cout << word << " ";
    }
    cout << endl;
    return 0;
}
```

接下来我们考虑一件事：如何编译链接、调试代码？

上面已经配置好了code runner插件，直接按右上角的三角标志，会自动编译运行代码，并在`build`文件夹生成二进制可执行文件`debug.out`

以后针对单个cpp文件，都建议使用code runner插件（因为速度较快）

对于简单的多文件项目(比如一个文件夹内有自定义的头文件和源文件)，可以通过 `Ctrl/Command + shift + B` 快捷键（或`Terminal`->`Run build task`）运行我们定义的task，如图所示，选择`Clang++: 编译多文件`,clang会编译该文件夹下的所有cpp文件,并在`build`文件夹生成二进制可执行文件`debug.out`

![1](VSCode配置C++环境最佳实践/2.png)

接着打开侧边栏的`Run and Debug页面`（如下图所示），选择`LLDB: 调试已编译的可执行文件`,然后点击左边的绿色三角按钮（或使用快捷键`Fn + F5`），vsc会调试`build`文件夹里编译好的可执行文件。如果不调试只运行，可以使用快捷键`Ctrl+F5`

![2](VSCode配置C++环境最佳实践/1.png)

当然，对于多文件项目，更简单的方法是：在侧边栏的`Run and Debug页面`，选择`LLDB: 编译和调试多文件`，以后就可以直接使用快捷键`Fn + F5`进行编译和调试，快捷键`Ctrl + F5`编译和运行啦

对于更复杂的工程项目，则需要使用cmake

对于代码格式化，只需鼠标右键，选`format document`，vscode可能提示选择用于代码格式化段的插件，选择`clangd`即可。也可以直接按快捷键`Alt + Shift + F`格式化代码文件

> 还没完！如果我们现在编写一个`.cpp`文件，会发现连系统头文件都查找不了，原因在于我们没有告诉 Clangd 我们的编译器是什么，会有什么参数——更别提系统头文件路径了！

## 未完待续，有时间再更cmake的配置
