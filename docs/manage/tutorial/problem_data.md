# 题目数据格式

?> 本页面的内容修改自 <https://vfleaking.github.io/uoj/problem/>。

下面我们先从传统题的配置讲起，然后再依次展开介绍各类非传统题的配置方式。

## 传统题的数据格式

### 1. 数据配置文件

假设你要上传一道传统题，输入文件是 `data1.in`, `data2.in`, ... 这个样子，输出文件 `data1.out`, `data2.out`, ...。你想设时间限制为 1s，空间限制为 256MB，那么你需要在上传这些输入输出文件的同时，编写一个类似下面这样的数据配置文件 `problem.conf`：

```text
use_builtin_judger on
use_builtin_checker ncmp
n_tests 10
n_ex_tests 5
n_sample_tests 1
input_pre data
input_suf in
output_pre data
output_suf out
time_limit 1
memory_limit 256
output_limit 64
```

当然你也可以通过点击「试题配置」按钮，来在线修改该数据配置文件的内容。

- **测评类型：**`use_builtin_judger on` 这一行指定了该题使用 UOJ 内置的测评器进行测评，绝大多数情况下都只需要使用内置的测评器。后面我们将介绍如何使用自定义的测评器。
- **输入输出文件：**UOJ 会根据 `printf("%s%d.%s", input_pre, i, input_suf)` 这种方式来匹配第 i 个输入文件，你可以将 `input_pre` 和 `input_suf` 改成自己想要的值。输出文件同理。
- **额外数据：**extra test 是指额外数据，在 AC 的情况下会测额外数据，如果某个额外数据通不过会被倒扣 3 分。额外数据中，输入文件命名形如 `ex_www1.in`, `ex_www2.in`, ... 这个样子，一般来说就是 `printf("ex_%s%d.%s", input_pre, i, input_suf)`，输出文件同理。
- **样例数据：**样例必须被设置为前若干个额外数据。所以数据配置文件里有一个 `n_sample_tests` 参数，表示的是前多少组是样例。你需要把题面中的样例和大样例统统放进额外数据里。
- **答案检查器（chk）：**数据配置文件中，`use_builtin_checker ncmp` 的意思是将本题的 checker 设置为 `ncmp`。关于内置 checker 的列表，请移步 [题目管理](/manage/problem) 页面的「[试题配置](/manage/problem#试题配置)」一节。

  如果想使用自定义的 checker，请使用 Codeforces 的 [testlib](http://codeforces.com/testlib) 库编写 checker，并命名为 `chk.cpp`，然后在 problem.conf 中去掉 “`use_builtin_checker`” 这一行。

- **时空限制：**`time_limit` 控制的是一个测试点的时间限制，单位为秒。`memory_limit` 控制的是一个测试点的空间限制，单位为 MB。`output_limit` 控制的是程序的输出长度限制，单位也为 MB，`stack_limit` 控制的是程序的栈空间大小限制（默认与 `memory_limit` 相等）。注意这些限制**都不能为小数**。

在目前的 UOJ 代码架构下，想让时间限制为小数似乎不是一件难事，但一直因为各种原因拖着没写。。希望有路过的有为青年可以帮帮忙咯。

#### 2. 配置 Hack 功能

如果想让你的题目支持 Hack 功能，那么你还要额外上传点程序。

- **数据检验器（val）**：数据检验器的功能是检验你的题目的输入数据是否满足题目要求。比如你输入保证是个连通图，validator 就得检验这个图是不是连通的。但比如 “保证数据随机” “保证数据均为人手工输入” 等就无法进行检验。请使用 Codeforces 的 [testlib](https://codeforces.com/testlib) 库编写，写好后命名为 val.cpp 即可。
- **标准程序（std）**：标准程序的功能是给定输入，生成标准答案。默认时空限制与选手程序相同。Hack 时，chk 将会根据 std 的输出，判断选手的输出是否正确。

#### 3. 辅助测评程序

我们暂且将 chk、val、std 这种称为辅助测评程序。上面我们已经介绍了这些辅助测评程序各自的用途，下面我们介绍一个有用的配置。

**配置时空限制：**默认情况下，chk 和 val 的时空限制为 5s + 256MB，std 的时空限制与选手程序相同。如果你想让这些辅助测评程序拥有更宽松的时空限制，你可以在 `problem.conf` 中加入

```text
<name>_time_limit 100
<name>_memory_limit 1024
```

来将其时间限制设置为 100s，空间限制设置为 1024MB。其中，chk 对应的 `<name>` 为 `checker`，val 对应的 `<name>` 为 `validator`，std 对应的 `<name>` 为 `standard`。

#### 4. 测试点分值

默认情况下，如果测试点全部通过，那么直接给 100 分。否则，如果你有 `n` 个测试点，那么每个测试点的分值为 `floor(100/n)`。

如果你想改变其中某个测试点的分值，可以在 `problem.conf` 里加入类似 `point_score_5 20` 这样的配置（意为把第 5 个测试点的分值改为 20）。

有些题目（特别是提答题）会有给单个测试点打部分分的需求。此时请使用自定义答案检查器 chk，并使用 quitp 函数。由于一些历史原因（求不吐槽 QAQ），假设测试点满分为 a，则

```cpp
quitp(x, "haha");
```

将会给 `floor(a * round(100 * x) / 100)` 分。是不是很复杂……其实很简单，当你这个测试点想给 p 分（p 为整数）的时候，只要

```cpp
quitp(ceil(100.0 * p / a) / 100, "haha");
```

就行了，虽然这个上取整有点反直觉。

如果你的题目是一道以子任务形式评分的题目，那么你可以用如下语法划分子任务和设定分值：

```text
n_subtasks 6
subtask_end_1 5
subtask_score_1 10
subtask_end_2 10
subtask_score_2 10
subtask_end_3 15
subtask_score_3 10
subtask_end_4 20
subtask_score_4 20
subtask_end_5 25
subtask_score_5 20
subtask_end_6 40
subtask_score_6 30
```

`subtask_end_*` 的值代表了第 \* 个测试点的最后一个测试点的编号。下一个 Subtask 的第一个测试点的编号为上一个 subtask 的最后一个测试点的编号加一。最后一个 subtask 的 end 值应该与 n_tests 相等。

可以用 `subtask_type_5 <type>` 将第 5 个子任务的评分类型设置为 `packed` 或 `min`。其中 `packed` 表示错一个就零分，`min` 表示测评子任务内所有测试点并取得分的最小值。默认值为 `packed`。

可以用 `subtask_used_time_type_5 <type>` 将第 5 个子任务统计程序用时的方式设置为 `sum` 或 `max`。其中 `sum` 表示将子任务内测试点的用时加起来，`min` 表示取子任务内测试点的用时最大值。默认值为 `sum`。

可以用 `subtask_dependence_5 3` 来将第 3 个子任务添加为第 5 个子任务的依赖任务，相当于测评时第 5 个子任务会拥有第 3 个子任务的所有测试点。所以如果第 3 个子任务中有一个错了，并且第 5 个子任务的评分类型为 `packed`，那么第 5 个子任务也会自动零分。如果你想给一个子任务添加多个依赖任务，比如同时依赖 3 和 4，那么你可以用如下语法：

```text
subtask_dependence_5 many
subtask_dependence_5_1 3
subtask_dependence_5_2 4
```

#### 5. 目录结构

下面是存储一道传统题数据的文件夹所可能具有的结构：

- `www1.in`, `www2.in`, ... ：输入文件
- `www1.out`, `www2.out`, ... ：输出文件
- `ex_www1.in`, `ex_www2.in`, ... ：额外输入文件
- `ex_www1.out`, `ex_www2.out`, ... ：额外输出文件
- `problem.conf`：数据配置文件
- `chk.cpp`：答案检查器
- `val.cpp`：数据检验器
- `std.cpp`：标准程序
- `download/`：一个文件夹，该文件夹下的文件会与样例文件一起被打包成压缩包，作为该题的附件供选手下载。
- `require/`：一个文件夹，该文件夹下的文件会被复制到选手程序运行时所在的工作目录。

## 提交答案题的配置

范例：

```text
use_builtin_judger on
submit_answer on
n_tests 10
input_pre data
input_suf in
output_pre data
output_suf out
```

So easy！

不过还没完，因为一般来说你还要写个 chk。当然如果你是道 [最小割计数](http://uoj.ac/problem/85) 就不用写了，直接用 `use_builtin_checker ncmp` 就行。

假设你已经写好 chk 了，你会发现你还需要发给选手一个本地可以运行的版本，这怎么办呢？如果只是传参进来一个测试点编号，检查测试点是否正确，那么只要善用 registerTestlibCmd 就行了。如果你想让 checker 与用户交互，请使用 registerInteraction。特别地，如果你想让 checker 通过终端与用户进行交互，请使用如下代码（有点丑啊）

```cpp
	char *targv[] = {argv[0], "inf_file.out", (char*)"stdout"};
#ifdef __EMSCRIPTEN__
	setvbuf(stdin, NULL, _IONBF, 0);
#endif
	registerInteraction(3, targv);
```

那么怎么下发文件呢？你可以把编译好的 chk 放在文件夹 download 下，该文件夹下的所有文件都会被打包发给选手。

**交叉编译小技巧：**（本段写于 2016 年）由于你的本地 checker 可能需要发布到各个平台，所以你也许需要交叉编译。交叉编译的意思是在一个平台上编译出其他平台上的可执行文件。好！下面是 Ubuntu 上交叉编译小教程时间。首先不管三七二十一装一些库：（请注意 libc 和 libc++ 可能有更新的版本）

```sh
sudo apt-get install libc6-dev-i386
sudo apt-get install lib32stdc++6 lib32stdc++-4.8-dev
sudo apt-get install mingw32
```

然后就能编译啦：

```sh
g++ localchk.cpp -o checker_linux64 -O2
g++ localchk.cpp -m32 -o checker_linux32 -O2
i586-mingw32msvc-g++ localchk.cpp -o checker_win32.exe -O2
```

还没完！在上传好数据之后，还要在网页中设置「提交文件配置」选项。比如你要让选手提交两个答案文件，分别为 `lock1.out` 和 `lock2.out`，那么需要改成下面这种格式：

```json
[
  {
    "name": "output1",
    "type": "text",
    "file_name": "lock1.out"
  },
  {
    "name": "output2",
    "type": "text",
    "file_name": "lock2.out"
  }
]
```

注意这是一段 JSON 文本，不会的话可以上网搜索一下，语法非常简单。

## 交互题的配置

UOJ 内部并没有显式地支持交互式，而是提供了 require、implementer 和 token 这几个东西。

### 1. require 文件夹

除了前文所述的 `download` 这个文件夹，还有一个叫 `require` 的神奇文件夹。在测评时，这个文件夹里的所有文件均会被移动到与选手源程序同一目录下。在这个文件夹下，你可以放置交互库，供编译时使用。

另外，`require` 文件夹的内容显然不会被下发……如果你想下发一个样例交互库，可以自己放到 `download` 文件夹里。

### 2. implementer 代码

再来说 implementer 的用途。如果你在 `problem.conf` 里设置 `with_implementer on` 的话，各个语言的编译命令将变为：

- C++: `g++ code implementer.cpp code.cpp -lm -O2 -DONLINE_JUDGE`
- C: `gcc code implementer.c code.c -lm -O2 -DONLINE_JUDGE`
- Pascal: `fpc implementer.pas -o code -O2`

这样，一个交互题就大致做好了。你需要写 `implementer.cpp`、`implementer.c` 和 `implementer.pas`。当然你不想支持某个语言的话，就可以不写对应的交互库。

如果是 C/C++，正确姿势是搞一个统一的头文件放置接口，让 implementer 和选手程序都 include 这个头文件。把主函数写在 implementer 里，连起来编译时程序就是从 implementer 开始执行的了。

如果是 Pascal，正确姿势是让选手写一个 pascal unit 上来，在 implementer.pas 里 uses 一下。除此之外也要搞另外一个 pascal unit，里面存交互库的各种接口，让 implementer 和选手程序都 uses 一下。

#### 3. token 机制

下面我们来解释什么是 token。考虑一道典型的交互题，交互库跟选手函数交流得很愉快，最后给了个满分。此时交互库输出了 “`AC!!!`” 的字样，也可能输出了选手一共调用了几次接口。但是聪明的选手发现，只要自己手动输出一下 “`AC!!!`” 然后果断退出似乎就能骗过测评系统了哈哈哈。

这是因为选手函数和交互库已经融为一体成为了一个统一的程序，测评系统分不出谁是谁。这时候，token 就出来拯救世界了。

在 `problem.conf` 里配置一个奇妙的 token，比如 `token wahaha233`，然后把这个 token 写进交互库的代码里（线上交互库，不是样例交互库）。在交互库准备输出任何东西之前，先输出一下这个 token。当测评系统要给选手判分时，首先判断文件的第一行是不是 token，如果不是，直接零分。这样就避免了奇怪的选手 hack 测评系统了。这里的 token 判定是在调用 checker 之前，测评系统会把 token 去掉之后的选手输出喂给 checker。（所以一道交互题的 token 要好好保护好）

不过这样的防御手段并不是绝对的。理论上只要选手能编写一个能理解交互库的机器码的程序，试图自动模拟交互库行为，那是很难防住的。

另外，记得在 C/C++ 的交互库里给全局变量开 `static`，意为仅本文件里的代码可访问（即仅交互库可访问）。

## 简单通信题的配置

这里我们考虑最简单的通信题。有一个交互器 interactor，选手程序的输出会变成 interactor 的输入，而 interactor 的输出会变成选手程序的输入。

（有时这种题目也叫交互题。。但为了不致混淆我们这里称之为通信题。）

如果你想配置这样的题目，只需要在 `problem.conf` 中加一行

```text
interaction_mode on
```

然后编写程序 interactor.cpp，跟输入输出文件放在一起即可。interactor 也属于辅助测评程序，可以参照 “辅助测评程序” 这一小节的内容进行配置。比如，通过添加一行 `interactor_time_limit 2` 可将 interactor 的时间限制设为 2s。

UOJ 的交互器 interactor 与 Codeforces 的交互器有一点点不同。在 Codeforces 的测评逻辑里，交互器与选手程序交互后，会把一些信息输出到文件，然后再交由答案检查器 chk 作出最终评分。但是 UOJ 里的交互器相当于 interactor + chk，也就是说交互器需要直接给出最终评分。所以，当你在使用 [testlib](http://codeforces.com/testlib) 库编写交互器时，需要按照编写答案检查器的方式，在 `main` 函数里第一句写上

```c++
registerTestlibCmd(argc, argv);
```

之后你就可以通过 `ouf` 获取选手程序输出，通过 `#!c++ cout` 或者 `#!c++ printf` 向选手程序输出信息（不要忘了刷新缓冲区），通过 `quitf` 或者 `quitp` 打分。注意，**不要按照 Codeforces 的习惯**调用 `registerInteraction`。

如果你想实现更复杂一点通信，比如多个程序相互之间通信，那么你可能需要参考下一节 “意想不到的非传统题” 的内容自己编写 judger 了。

## 意想不到的非传统题

假如你有一道题不属于上述任何一种题目类型 —— 那你就得自己写测评器了！

测评器（judger）的任务是：给你选手提交的文件，输出一个测评结果。

把 problem.conf 里的 `use_builtin_judger on` 这行去掉，放一个 judger 程序的源代码放在题目数据目录下，就好了。

但怎么编写 judger 呢？首先你需要写个 Makefile，UOJ 会根据你的 Makefile 和 judger 源代码生成 judger。如果你要使用自定义答案检查器 chk，那么你也需要在 Makefile 里面指定 chk 的编译方式。比如下面这个例子：

```makefile
export INCLUDE_PATH
CXXFLAGS = -I$(INCLUDE_PATH) -O2

all: chk judger

% : %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@
```

什么？你问 judger 应该怎么写？

这部分文档我只能先【坑着】。不过这里有一点有助于你自己看懂代码的指南：

1. 你可以先从 UOJ 内置的测评器读起，在 `uoj_judger/builtin/judger/judger.cpp`；
2. 你会发现它引用了头文件 `uoj_judger.h`，大概读一读；
3. `uoj_judger.h` 调用了沙箱 `uoj_judger/run/run_program.cpp`，大概理解一下沙箱的参数和输入输出方式；
4. `uoj_judger.h` 还会用 `uoj_judger/run/compile.cpp` 来编译程序，用 `uoj_judger/run/run_interaction.cpp` 来处理程序间的通信，大概读一读；
5. 回过头来仔细理解一下 `uoj_judger.h` 的代码逻辑。

不过说实话 `uoj_judger.h` 封装得并不是很好。所以有计划重写一个封装得更好的库 `uoj_judger_v2.h`，这个库拖拖拉拉写了一半，但暂且还是先放了出来，供大家参考。期待神犇来造一造轮子！

最后，如果要使用自定义 judger 的话，就只有超级管理员能点击「**检验配置并同步数据**」了。这是因为 judger 的行为几乎不受限制，只能交给值得信赖的人编写。不过无论你的 judger 如何折腾，测评也是有 10 分钟的时间限制的。

## 样题

在 [这个仓库](https://github.com/vfleaking/uoj-sample-problems) 里存放了一些来自 UOJ 官网的题目数据，供大家参考。
