---
title: 小熊猫包管理器 (oma) 用户指南
toc: true
---

小熊猫包管理器（英文名 oma）是为安同 OS 编写的包管理器，基于 libapt-pkg 构建，目标是取代 APT。oma 将 APT 的各种子功能集于一身，并且拥有许多新特性，如更清晰的界面、多线程下载、镜像选择及回滚功能等。因此，oma 的使用体验相对于 APT 要好很多。

oma 是安同 OS 的默认包管理器，因此日常系统更新、软件管理等操作均需要通过 oma 进行。


## oma 的功能

oma 是包管理器，因此具备传统包管理器的功能，如安装、更新和卸载软件包。除此之外，oma 还有一些针对安同 OS 的特色功能。oma 的功能如下：

- 搜索、安装和卸载软件包
- 更新软件包及系统
- 列出软件包的文件
- 根据文件搜索软件包
- 查看软件包的反向依赖关系
- 管理安同 OS 的测试源 (Topic)
- 切换软件仓库的镜像源
- 回滚 oma 历史操作
- 终端图形 (TUI) 界面

## 运行 oma

oma 属于系统管理工具，因此任何软件包、测试源和镜像相关的操作均需要 root 权限。不过，oma 集成了提权框架 (PolicyKit)，因此在桌面环境下运行 oma 的命令无需使用 `sudo` 工具获得 root 权限。

![oma 调用 PolicyKit 获取 root 权限](https://hackmd.io/_uploads/BJccv8K_A.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 通过 PolicyKit 询问当前用户的密码，来获得 root 权限
</p>

> [!NOTE]注意

> 如果没有使用桌面环境的终端模拟器，则需要通过 sudo 来获得 root 权限：
> ```
> sudo oma
> ```

## oma 的操作模式

为了适应不同习惯的用户，oma 提供两种操作模式：传统模式和 TUI 模式。两种操作模式的特征如下：

- **传统模式**: 类似于 APT 的使用逻辑。安装、更新、卸载等操作都通过在 Shell 中执行命令来完成。
- **TUI 模式**：在终端中给予用户类似图形界面的使用体验，因此对于不熟悉终端的人比较友好。安装、更新和卸载等操作相对于传统的执行命令模式，使用更加方便。

![image](https://hackmd.io/_uploads/rJKH1IK_R.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
传统模式，类似 APT 的操作逻辑；图中安装了 KDE 的视频播放器 Haruna
</p>

![image](https://hackmd.io/_uploads/H13ZlUKu0.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
TUI 模式，提供了类似桌面程序的界面；图中选中了 KDE 的图像管理器 DigiKam 准备安装
</p>

和 APT 一样，oma 是一个命令行程序。因此使用 oma 更新系统、安装安同 OS 软件仓库中的软件前，请先打开终端。

> [!NOTE] 提示
>
> 在安同 OS 桌面版环境下，可以按 <kbd>Ctrl</kbd> + <kbd>Alt</kbd> + <kbd>T</kbd> 快捷键打开 Konsole 终端。

## 传统模式操作指南

本节内容讲述如何使用 oma 的传统操作模式完成软件管理、系统维护等操作。

> [!Important]
> 下文中将不再强调 oma 在 SSH 和桌面终端模拟器运行的区别。如果您在 SSH 及 tty 环境，请自行使用 `sudo` 工具执行 oma。

### 搜索软件包

在不清楚要安装的软件包名称的情况下，可以利用 oma 的搜索功能找到需要安装的软件包名称。

要通过 oma 搜索软件包，请执行如下命令：

```shell
oma search 搜索关键字
```

#### 示例

例如，某用户想在系统里安装《我的世界》启动器 Prism Launcher，但是不清楚 Prism Launcher 在安同 OS 中的软件包名。因此，这位用户就可以执行如下命令得到结果：

```shell
oma search Prism
```

![image](https://hackmd.io/_uploads/Hysi9IFdC.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 根据如上命令返回的搜索结果
</p>

> [!NOTE] 注意
> oma 的当前版本不能处理包含多个单词的搜索关键字。例如，`oma search Prism Launcher` 就不会返回任何结果。

### 安装软件包

要通过 oma 安装软件包，请执行如下命令：

```shell
oma install 软件包1 [软件包2...]
```

> [!note]
> 上述命令中，方括号中的参数 `[软件包2]...` 指的是后续的参数是可选的。例如，安装软件包至少需要提供一个包名，因此执行 `oma install 软件包1` 就会让 oma 执行安装 “软件包1” 的操作；`oma install 软件包1 软件包2` 就会让 oma 安装两个软件包。
> 
> 注意，在提供多个参数时不需要加上方括号。

执行安装命令后，oma 会检查包名是否正确，并计算依赖关系。依赖关系计算完毕后，oma 会弹出确认界面。

为了满足您要安装的软件包的依赖关系，oma 会根据计算出的依赖关系安装其他软件包，或是卸载一些软件包。在确认界面，您会看到 oma 对软件包的变动，以及存储空间变化情况。

> [!NOTE] 注意
> 
> 请仔细验证确认界面的软件包变动情况，尤其是在安装软件包时卸载过多的软件包的情况。
> 
> 如果您观察到了不正常的情况，请按下 <kbd>Ctrl</kbd> - <kbd>C</kbd> 终止操作。


![oma 执行安装操作之前弹出的确认界面](https://hackmd.io/_uploads/Sk8lTUFdA.png)
<p style="font-size: 1.0rem;text-align: center;">
oma 执行安装操作之前弹出的确认界面
</p>

在确认界面中，可以使用光标键滚动内容。如果您满意 oma 对软件包的更改，请按 <kbd>Q</kbd> 退出确认界面，执行安装操作。

如果不满意 oma 对软件包的更改（如注意到卸载了非常多的软件），请按下 <kbd>Ctrl</kbd> + <kbd>C</kbd> 取消操作。

#### 示例

继续使用 “搜索软件包” 一节中的例子：这位用户通过搜索得知 Prism Launcher 的包名是 `prismlauncher`，因此这位用户就可以执行如下命令：

```shell
oma install prismlauncher
```

![image](https://hackmd.io/_uploads/Hy6LkPYuA.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
安装 <code>prismlauncher</code> 的确认界面，可以看到依赖关系已经满足（只需要安装自身）
</p>

按下 <kbd>Q</kbd>，oma 就会继续安装操作：

![image](https://hackmd.io/_uploads/Hy3yevKdR.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
Prism Launcher 安装完毕
</p>

Prism Launcher 随后会安装到系统中。接下来，这位用户就可以在 KDE 菜单中找到 Prism Launcher。

#### 可选选项

安装软件包时可以指定安装选项。指定选项时的命令格式如下：

```shell
oma install 选项1 [选项2] 软件包1 [软件包2...]
```

指定选项会影响 oma 的行为。对于安装操作，可以指定以下选项：

- `--yes` 或 `-y` ：不显示确认界面，直接安装。
- `--reinstall`：重新安装已经安装过的软件包。适用于无意中修改了软件包内容的情况。
- `--no-install-recommends`：不安装推荐的软件包。有些软件会推荐安装其他的软件，以增强该软件的使用体验。使用这个选项来禁用推荐。

本指南只列出一些常用的选项。其他的安装选项可以通过如下命令获得：

```shell
oma install --help
```

### 卸载软件包

如果在系统中遇到不想要的软件，可以通过 oma 将其卸载。但是多数软件包都是系统的组成部分，您无法卸载它们。

要通过 oma 卸载软件包，请执行如下命令：

```shell
oma remove 软件包1 [软件包2...]
```


执行安装命令后，oma 会检查包名是否正确，并计算依赖关系。依赖关系计算完毕后，oma 会弹出确认界面。

为了满足您要安装的软件包的依赖关系，oma 会根据计算出的依赖关系安装其他软件包，或是卸载一些软件包。在确认界面，您会看到 oma 对软件包的变动，以及存储空间变化情况。

![image](https://hackmd.io/_uploads/BJoOuURtC.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 执行卸载之前弹出的确认界面
</p>

在确认界面中，可以使用光标键滚动内容。如果您满意 oma 对软件包的更改，请按 <kbd>Q</kbd> 退出确认界面，执行安装操作。

如果不满意 oma 对软件包的更改（如注意到卸载了非常多的软件），请按下 <kbd>Ctrl</kbd> + <kbd>C</kbd> 取消操作。

> [!NOTE]

> 在卸载软件包时，需要尤其注意：
> - 有些软件包是系统的重要组成部分，您无法卸载它们。
> - 如果您卸载软件时遇到大量被连带卸载的软件包，说明要卸载的包的确是系统的组成部分。请立即按下 <kbd>Ctrl</kbd> + <kbd>C</kbd> 停止操作。继续卸载这些软件包会导致系统无法启动，或桌面环境无法显示。

#### 示例

继续使用 “搜索软件包” 一节中的例子：这位用户通过搜索得知 Prism Launcher 的包名是 `prismlauncher`，因此这位用户就可以执行如下命令：

```shell
oma remove prismlauncher
```

![image](https://hackmd.io/_uploads/Sk4o7UAKR.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
卸载 <code>prismlauncher</code> 的确认界面，可以看到依赖关系已经满足（只需要安装自身）
</p>

按下 <kbd>Q</kbd>，oma 就会继续安装操作：

![1722864698828](https://hackmd.io/_uploads/SJC4BUAKR.png)


<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
Prism Launcher 卸载完毕
</p>

Prism Launcher 随后会从系统中卸载。


TBD

### 更新系统或软件包

如果您系统中的软件包版本过旧，需要更新，可以通过 oma 更新它们。

要通过 oma 更新软件包，请执行如下命令：

```shell
oma upgrade
```

由于 oma 每次更新的内容是整个系统，因此不论指定多少个软件包，其余的软件包都会随着一起更新。也就是说您无需指定要更新的软件包。

为了满足您要更新的软件包的依赖关系，oma 会根据计算出的依赖关系安装其他软件包，或是卸载一些软件包。在确认界面，您会看到 oma 对软件包的变动，以及存储空间变化情况。

![1722950484538](https://hackmd.io/_uploads/Sy2dmsy5R.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 执行更新操作之前弹出的确认界面
</p>

在确认界面中，可以使用光标键滚动内容。如果您满意 oma 对软件包的更改，请按 <kbd>Q</kbd> 退出确认界面，执行更新操作。

如果不满意 oma 对软件包的更改（如注意到卸载了非常多的软件），请按下 <kbd>Ctrl</kbd> + <kbd>C</kbd> 取消操作。

#### 示例

继续使用 “搜索软件包” 一节中的例子：这位用户通过搜索得知 Prism Launcher 的包名是 `prismlauncher`，因此这位用户就可以执行如下命令：

```shell
oma upgrade prismlauncher
```

![image](https://hackmd.io/_uploads/BknjEjk5R.png)


<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
更新 <code>prismlauncher</code> 的确认界面，可以看到依赖关系已经满足（只需要安装自身）
</p>

按下 <kbd>Q</kbd>，oma 就会继续更新操作：

![oma-upgrade-prismlauncher-finish](https://hackmd.io/_uploads/rJNqPs19C.png)


<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
Prism Launcher 更新完毕
</p>

Prism Launcher 随后会在系统中进行更新。接下来，这位用户就可以在 KDE 菜单中找到更新版本的 Prism Launcher。

### 管理测试源 (Topics)

#### 什么是测试源？

测试源 (Topics) 是安同 OS 的特色功能，旨在为用户提供尝鲜、测试和反馈的机会及平台。

如果要引入新的软件包，或更新已有软件包的版本，就需要将它们先提交至测试源中，供开发者及感兴趣的用户测试，测试通过后方可发布稳定版软件仓库，提供给广大用户使用。

例如，KDE 桌面环境发布了 KDE Plasma 的 6.0 版本，是一次大更新，因此开发者需要将 KDE 6.0 的更新先发布至测试源，让感兴趣的用户加入测试。在不断地收集反馈、调整后，KDE 6.0 更新才能取代稳定版仓库中的 KDE 5.15。

#### 通过菜单管理和加入测试源

您可以轻松地通过 oma 浏览安同 OS 当前开放的测试源，就像浏览菜单一样方便，不再需要复杂的命令。

启动测试源菜单的命令如下：

```shell
oma topics
```
执行这条命令之后，就进入测试源的菜单界面：

![image](https://hackmd.io/_uploads/BJ40yxZ5A.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 的测试源菜单界面
</p>

在这个界面中，您可以通过 <kbd>↑</kbd> <kbd>↓</kbd> 方向键浏览开放的测试源。
在找到希望加入的测试源后，
可以按 <kbd>Space</kbd> 或 <kbd>Enter</kbd> 键选中/取消选中测试源。

如果遇到测试源数目过多，无法快速找到想加入的测试源的情况，您可以在浏览界面中直接输入关键词，快速搜索要加入的测试源：

 ![image](https://hackmd.io/_uploads/ryIFrebcC.png)
 <p style="font-size: 1.5rem;font-style: italic;text-align: center;">
在测试源菜单中快速搜索测试源
</p>

在选中所有想加入的测试源之后，按 <kbd>Esc</kbd> 键就会将当前选中的测试源应用到系统中。oma 会自动将软件更新到测试源中的版本，并同时更新系统。

> [!Note]
> 如果测试源中包含的软件包并未安装在系统中，您需要在应用更改后手动安装它们。安装流程可参考之前的 “安装软件包” 部分。手动安装的软件包将处于测试源中的版本。
>

![1723037352363](https://hackmd.io/_uploads/ry0aLlWqR.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
加入测试源之后，软件将会更新至测试源中的版本；同时也会更新系统
</p>


![1723039490687](https://hackmd.io/_uploads/ByZfJb-5C.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
加入测试源之后，软件将会更新至测试源中的版本；同时也会更新系统
</p>

通过菜单管理测试源非常方便，但有时可能只需操作一个测试源。oma 除了提供菜单式的管理界面之外，还提供直接加入测试源的命令。

#### 通过命令管理和加入测试源

通过命令加入测试源的方式较少使用，您通常会在社区发布的用户公告中看到它们。为了方便用户，公告一般会提供一键加入测试源的命令供用户快速进入测试。

由于命令加入方式需要您知道测试源的具体名称，因此除非在公告中看到，否则不推荐您通过命令加入或退出测试源。

要通过命令加入测试源，您需要写如下命令：

```shell
oma topics --opt-in 测试源名称
```

![1723039058928](https://hackmd.io/_uploads/ryWFaxZc0.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
加入测试源的确认界面
</p>

> [!NOTE] 注意
> 
> 您可以按 <kbd>q</kbd> 继续执行操作。如果您观察到了不正常的情况，请按下 <kbd>Ctrl</kbd> - <kbd>C</kbd> 取消操作。
 

![1723039490687](https://hackmd.io/_uploads/ByZfJb-5C.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
加入测试源之后，软件将会更新至测试源中的版本；同时也会更新系统
</p>


#### 退出测试源

要通过命令加入测试源，您需要写如下命令：

```shell
oma topics --opt-out 测试源名称
```

![1723122459726](https://hackmd.io/_uploads/Hy6B7BMcC.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
退出测试源的确认界面
</p>

> [!NOTE] 注意
> 
> 您可以按 <kbd>q</kbd> 继续执行操作。如果您观察到了不正常的情况，请按下 <kbd>Ctrl</kbd> - <kbd>C</kbd> 取消操作。

![image](https://hackmd.io/_uploads/Hyy_4SMcA.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
退出测试源的界面
</p>

在退出测试源后，测试源内的软件包将会被回滚到稳定源中的版本。

### 切换镜像源

您在下载软件包时，如果遇到网速较慢、无法正常下载的情况，您可以通过 oma 切换到速度较快的镜像源。切换镜像源的命令如下：

```shell
oma mirrors
```

![image](https://hackmd.io/_uploads/HkdAAc75R.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 的镜像选择界面（上下键选择）
</p>

![image](https://hackmd.io/_uploads/H1K7Qs7q0.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 的镜像选择界面（直接输入）
</p>


oma中默认的镜像源为 AOSC main repository(hosted by Aperture Science Limited)

在这个界面中，您可以通过 <kbd>↑</kbd> <kbd>↓</kbd> 方向键浏览镜像源。为了更方便使用，您也可以直接输入要切换的镜像源。


在找到希望使用的镜像源后，
可以按 <kbd>Space</kbd> 或 <kbd>Enter</kbd> 键选中/取消选中测试源。

在选中将要使用的镜像源之后，按 <kbd>Esc</kbd> 键就会应用该镜像源。

![image](https://hackmd.io/_uploads/BkO2ljQ9C.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
选中 Tsinghua Open Source Mirror 镜像源
</p>

![image](https://hackmd.io/_uploads/SJhNWjQc0.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
应用Tsinghua Open Source Mirror 镜像源
</p>

至此，后续的软件包的下载时将在更改后的镜像源下载。



### 回滚之前的改动

在您误操作之后，或者想返回之前的操作时，您可以通过oma进行回滚。回滚的命令如下：

```shell
oma history
```



![1723213323287](https://hackmd.io/_uploads/ry8XIsX90.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
回滚记录选择界面
</p>

在这个界面中，您可以通过 <kbd>↑</kbd> <kbd>↓</kbd> 方向键选择要回滚的记录，在选中之后可以通过 <kbd>Enter</kbd> 键对其选中。


![1723213381505](https://hackmd.io/_uploads/H1_IIiQ9C.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
回滚输出界面
</p>

在这个界面中，您可以通过 <kbd>q</kbd> 键或 <kbd>Ctrl</kbd> - <kbd>c</kbd> 键退出。


![image](https://hackmd.io/_uploads/BkqL9oXcR.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
回滚结束界面
</p>

至此，完成了回滚操作。




### 列出软件包内含的文件

您可以通过 oma 列出软件包所含的文件。如需查看软件包内含的文件列表，请执行如下命令：

```shell
oma files 软件包名称
```

oma 会随后打开一个浏览界面。您可以在这个界面中使用光标键、翻页键 (<kbd>PgUp</kbd> / <kbd>PgDn</kbd>) 滚动屏幕上的内容。按 <kbd>Q</kbd> 键退出浏览。

如，要查看软件包 `glibc` 的内容，可以执行：

```
oma files glibc
```

oma 随即打开浏览界面，列出 `glibc` 软件包的所有文件。


![image](https://hackmd.io/_uploads/BJpCI8GcA.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
通过搜索文件名查找软件包
</p>


### 通过文件搜索软件包

如果遇到系统提示找不到文件的情况，您可以提供关键字，通过 oma 的按文件搜索功能找到需要的软件包。要按文件名搜索软件包，请执行如下命令：

```shell
oma provides 文件名关键字
```

#### 示例

假设用户编译软件时，编译器提示找不到头文件 `<nlohmann/json.h>`，该用户就可以通过 oma 搜索报告缺少的文件名，找到对应的软件包。

![image](https://hackmd.io/_uploads/SJSi1Uz5R.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
通过搜索文件名查找软件包
</p>

由此可见，对应的软件包名是 nlohmann-json，用户就可以运行 oma install nlohmann-json 补上缺失的头文件，继续编译。

## TUI 模式

oma 提供一个类似桌面程序的 TUI 界面，操作简单，对不熟悉终端的用户比较友好。但是目前 TUI 模式下只能管理软件包，没有测试源和镜像管理的功能。

### 进入 TUI 模式

要使用 TUI 模式，请执行如下两个命令之一：

```shell
oma
```

或

```shell
oma tui
```

输入密码后即可进入 oma 的 TUI 模式。

![image](https://hackmd.io/_uploads/Byp27DK_0.png)

<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
oma 的 TUI 模式
</p>

可以看到，TUI 模式下拥有搜索框和结果列表。

光标默认在搜索框处，可以立即键入搜索关键字，找到要安装、卸载或更新的软件包。搜索结果会显示在搜索框下面的列表中。

### TUI 模式下的按键说明

在 TUI 模式下，需要使用以下按键：

- <kbd>␣</kbd>（空格键）：用于在搜索结果列表中选中或取消选中软件包。
- <kbd>F1</kbd> ：查看 oma 的待办事项（准备安装、更新或卸载的软件包）。
- <kbd>Esc</kbd> ： 立即应用修改。
- <kbd>/</kbd> ：跳转至搜索框，搜索软件包。
- <kbd>Tab ↹</kbd> ：在搜索结果列表和搜索框之间切换。
- <kbd>Ctrl</kbd> + <kbd>C</kbd> ：不应用修改，退出 oma。

### TUI 模式下安装、卸载、更新软件包


![1723214774781](https://hackmd.io/_uploads/HJ4T3im90.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
小熊猫包管理器 TUI 界面
</p>

在搜索框中输入要操作（安装、卸载、更新）的软件包名，出现搜索结果后，您可以按 <kbd>↑</kbd> <kbd>↓</kbd> 键选中包名，之后按 <kbd>␣</kbd> 键将其添加到右边的代办事项框中。

待办事项框中存放的是您在搜索结果列表中选中的软件包。如果软件包已经安装在系统中，则待办事项中对应的软件包会以 “-” 号开头，代表需要卸载。反之，“+” 号就代表软件未安装或版本较旧，需要下载或更新。

![1723215001416](https://hackmd.io/_uploads/Sypp2iXcC.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
小熊猫包管理器选中代办事项界面
</p>

若要从待办事项中删除一条操作，您可以按下 <kbd>Tab</kbd> 键切换到待办事项列表，用光标键移动到要移除的操作上，然后按下 <kbd>Space</kbd> 或 <kbd>Enter</kbd> 将其移除。

在您选择了所有想要安装或卸载的软件包后，就可以通过按 <kbd>Esc</kbd> 键进入确认界面，再次确认待办事项中的操作：

![1723215090526](https://hackmd.io/_uploads/ryzbpsXcC.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
小熊猫包管理器选中代办事项的确认界面
</p>

和之前的确认界面一样，在这里您可以按 <kbd>Q</kbd> 键结束审阅，继续操作。如有异议，按 <kbd>Ctrl</kbd> - <kbd>c</kbd> 键退出。

![1723215134685](https://hackmd.io/_uploads/SylN6jQ90.png)
<p style="font-size: 1.5rem;font-style: italic;text-align: center;">
待办事项中的操作执行完成后
</p>

### TUI 模式下更新系统

在更新系统时，进入TUI界面，直接按 <kbd>Esc</kbd> 键便可对系统进行更新。
