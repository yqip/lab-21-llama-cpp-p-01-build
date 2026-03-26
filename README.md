# 目的

llama.cpp项目验证

# 平台

win10+vs2022+Git+Miniforge

# 一、Git Clone

1. 进入本地文件夹，右键，选Git Bash，下面操作全部在Git Bash终端中操作

2. **测试 SSH 连接** ：

   ```
   ssh -T git@github.com
   ```

   成功时会显示类似：  
   `Hi username! You've successfully authenticated, but GitHub does not provide shell access.`

3. 强制将所有 GitHub 的 HTTP(S) 链接转为 SSH：

   ```
   git config --global url."git@github.com:".insteadOf "https://github.com/"
   #验证
   git config --global --list
   ```

   输出应包含：`url.git@github.com:.insteadof=https://github.com/`

4. 实施Clone

   ```
   git clone --recurse-submodules git@github.com:yqip/llama.cpp.git
   ```

   

5. 验证

   ```
   #进入项目目录
   cd llama.cpp
   #检查所有子模块状态（最关键）
   git submodule status
   #验证子模块是否也用了 SSH
   cd ggml/src/kompute
   git remote -v
   cd ../../..
   ```

   * **成功标志**：列出多行内容，每行开头是**空格**（例如 ` 4565194... ggml/src/kompute`）。

   * **失败标志**：

     *   开头是 `-`：表示未初始化。
     *   开头是 `+`：表示版本不匹配（通常没事）。
     *   **没有任何输出**：说明当前目录没识别到子模块配置（可能是你没进对目录，或者 `.gitmodules` 文件缺失）。

     ```
     # 补全可能遗漏的深层子模块
     git submodule update --init --recursive
     ```

# 二、编译（win10+VS2022)

1. 组件确认

   打开 **Visual Studio Installer**，找到已安装的 VS2022，点击 **“修改”**，确保勾选了以下组件（你之前的描述中已经包含大部分，请再次确认）：

   - **工作负载 (Workloads)**:

     - ✅ **使用 C++ 的桌面开发** (Desktop development with C++)

       -   ✅ **C++ CMake tools for Windows**

       -   ✅ **Git for Windows** (可选，如果你已经在外面装了 Git)

       -   ✅ **Clang Compiler for Windows** (推荐，用于更好的兼容性和部分加速指令集支持)

       -   ✅ **MSBuild support for LLVM toolset** (如果使用 Clang)

2. 管理员身份打开【x64 Native Tools Command Prompt for VS 2022】，作为指令执行终端

3. 终端进入项目目录：...\llama.cpp

4. 执行 CMake 编译命令,基础编译 (仅 CPU，最快上手)

   这是最简单的测试方式，使用默认的多线程指令集优化。

   ```cmd
   cmake -B build -G "Visual Studio 17 2022" -A x64 -DCMAKE_BUILD_TYPE=Release
   ```

   *   `-G "Visual Studio 17 2022"`: 指定生成 VS2022 的解决方案文件。
   *   `-A x64`: 指定架构为 64 位 (如果是 ARM 电脑改为 `ARM64`)。

5. 输出结果提交AI确认或指导修复

6. 继续生成核心工具，关闭网络可以加快速度

   ```
   cmake --build build --config Release -j 8
   ```

7. 输出结果提交AI确认或指导修复

8. 核心工具.exe文件位于：...\llama.cpp\build\bin\Release\，验证

   ```
   .\build\bin\Release\llama-cli.exe -h
   ```

   输出关于llama-cli的帮助信息

# 三、搭建环境

1. 自定义环境名：【lab-21-llama-cpp-p-01-build】

2. 继续采用之前的终端新建环境

   ```
   #先激活base
   conda activate
   #创建环境
   mamba create -n lab-21-llama-cpp-p-01-build python=3.11 -y
   #查看环境
   mamba env list
   #激活环境
   conda activate lab-21-llama-cpp-p-01-build
   ```

# 四、下载模型与对话

1. 浏览器：魔搭社区 (ModelScope)

   ```
   https://modelscope.cn/models
   ```

2. 搜索模型名 + "GGUF"（例如 `Qwen2.5-7B-Instruct-GGUF`）

   qwen2.5-7b-instruct-q4_k_m.gguf，

   - **适用场景：** 大多数现代家用电脑（拥有 8GB - 16GB 内存/显存）。
   - **理由：** `Q4_K_M` (4-bit 量化) 是目前公认的“甜点”配置。它在保持模型智力损失极小（几乎感觉不到）的同时，将体积压缩到了原来的 1/3 左右，推理速度最快。
   - **预计占用内存：** 约 **4.5 GB - 5 GB**。

3. 根据modelscope网站下载页的指令方式下载：基于之前激活环境的终端窗口

   ```
   #安装魔搭工具
   pip install modelscope
   #根据下载页提示下载相关文件
   modelscope download --model Qwen/Qwen2.5-7B-Instruct-GGUF qwen2.5-7b-instruct-q4_k_m.gguf --local_dir ./dir
   ```

   文件位置：.\llama.cpp\dir

4. 打开模型并测试

   ```
   build\bin\release\llama-cli.exe -m dir\qwen2.5-7b-instruct-q4_k_m.gguf -cnv -i -if --color -p " "
   ```



> ````
> > 写一个Python程序，功能：输出前100个质数，数与数间用","分隔
> 当然！以下是一个Python程序，用于输出前100个质数，并且每个质数之间用逗号分隔：
> 
> ```python
> def is_prime(n):
>     """判断一个数是否是质数"""
>     if n <= 1:
>         return False
>     if n <= 3:
>         return True
>     if n % 2 == 0 or n % 3 == 0:
>         return False
>     i = 5
>     while i * i <= n:
>         if n % i == 0 or n % (i + 2) == 0:
>             return False
>         i += 6
>     return True
> 
> def get_primes(count):
>     """返回前count个质数"""
>     primes = []
>     num = 2
>     while len(primes) < count:
>         if is_prime(num):
>             primes.append(num)
>         num += 1
>     return primes
> 
> # 输出前100个质数，数与数间用","分隔
> primes = get_primes(100)
> print(",".join(map(str, primes)))
> ```
> 
> 你可以将这段代码保存到一个文件中，例如 `prime_numbers.py`，然后在命令行中运行 `python prime_numbers.py` 来查看输出结果。
> 
> 运行这段代码会输出前100个质数，并且每个质数之间用逗号分隔。希望这对你有帮助！如果有其他问题，请随时告诉我。
> 
> >
> ````
>
> 



# V-HIS

V20260326：1st



