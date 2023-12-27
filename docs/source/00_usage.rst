快速使用
===========

.. _installation:

安装picker
-------------

源码安装: 

依赖

1. cmake (>=3.11)
2. gcc (支持c++20,至少为10, 最好为11及以上),
3. python3 (>=3.8)
4. verilator ( **==** 4.218)  
5. `verible-verilog-format <https://github.com/chipsalliance/verible/releases/tag/v0.0-3428-gcfcbb82b>`_ (>=0.0-3428-gcfcbb82b)
6. swig (>=4.2.0, 目前为master分支， 仅在需要python支持时使用）


.. code-block:: console

   git clone https://gitee.com/yaozhicheng/mcv.git
   cd mcv
   export BUILD_XSPCOMM_SWIG=python # 仅在需要python支持时使用
   make
   sudo make install

测试是否安装成功
-----------------

执行picker命令，如果出现以下信息，则安装成功

.. code-block:: console

  XDut Generate. 
  Convert DUT(*.v/*.sv) to C++ DUT libs. Notice that [file] option allow only one file.

  Usage:
  XDut Gen [OPTION...] [file]

  -f, --filelist arg            DUT .v/.sv source files, contain the top 
                                module, split by comma.
                                Or use '*.txt' file  with one RTL file path 
                                per line to specify the file list (default:"")
      --sim arg                 vcs or verilator as simulator, default is 
                                verilator (default: verilator)
  -l, --language arg            Build example project, default is cpp, 
                                choose cpp or python (default: cpp)
  -s, --source_dir arg          Template Files Dir, default is 
                                ${mcv_install_path}/../mcv/template 
                                (default: /usr/local/share/mcv/template)
  -t, --target_dir arg          Render files to target dir, default is 
                                ./mcv_out (default: ./mcv_out)
  -S, --source_module_name arg  Pick the module in DUT .v file, default is 
                                the last module in the -f marked file 
                                (default: "")
  -T, --target_module_name arg  Set the module name and file name of target 
                                DUT, default is the same as source. For 
                                example, -T top, will generate UTtop.cpp 
                                and UTtop.hpp with UTtop class (default: 
                                "")
      --internal arg            Exported internal signal config file, 
                                default is empty, means no internal pin 
                                (default: "")
  -F, --frequency arg           Set the frequency of the **only VCS** DUT, 
                                default is 100MHz, use Hz, KHz, MHz, GHz as 
                                unit (default: 100MHz)
  -w, --wave_file_name arg      Wave file name, emtpy mean don't dump wave 
                                (default: "")
  -V, --vflag arg               User defined simulator compile args, 
                                passthrough. Eg: '-v -x-assign=fast -Wall 
                                --trace' || '-C vcs -cc -f filelist.f' 
                                (default: "")
  -C, --cflag arg               User defined gcc/clang compile command, 
                                passthrough. Eg:'-O3 -std=c++17 
                                -I./include' (default: "")
  -v, --verbose                 Verbose mode
  -e, --example                 Build example project, default is OFF
  -h, --help                    Print usage

参数解释：

.. code-block:: console

   [file]: 必需。DUT 的 Verilog 或 SystemVerilog 源文件，包含顶层模块
   --filelist, -f: 可选。DUT 的 Verilog 或 SystemVerilog 源文件，逗号分隔。也可以使用 '*.txt' 文件，每行指定一个 RTL 文件路径，来指定文件列表。
   --sim: 可选。模拟器类型，可以是 vcs 或 verilator，默认是 verilator。
   --language, -l: 可选。构建示例项目的语言，可以是 cpp 或 python，默认是 cpp。
   --source_dir, -s: 可选。模板文件目录，默认是 ${mcv_install_path}/../mcv/template。
   --target_dir, -t: 可选。渲染文件的目标目录，默认是 ./mcv_out。
   --source_module_name, -S: 可选。在 DUT 的 Verilog 文件中选择模块，默认是  标记的文件中的最后一个模块。
   --target_module_name, -T: 可选。设置目标 DUT 的模块名和文件名，默认与源相同。例如，-T top 将生成 UTtop.cpp 和 UTtop.hpp，并包含 UTtop 类。
   --internal: 可选。导出的内部信号配置文件，默认为空，表示没有内部引脚。
   --frequency, -F: 可选。设置 仅 VCS DUT 的频率，默认是 100MHz，可以使用 Hz、KHz、MHz、GHz 作为单位。
   --wave_file_name, -w: 可选。波形文件名，为空表示不导出波形。
   --vflag, -V: 可选。用户定义的模拟器编译参数，透传。例如：'-v -x-assign=fast -Wall --trace' 或 '-f filelist.f'。
   --cflag, -C: 可选。用户定义的 gcc/clang 编译参数，透传。例如：'-O3 -std=c++17 -I./include'。
   --verbose, -v: 可选。详细模式，保留生成的中间文件。
   --example, -e: 可选。构建示例项目，默认是 OFF。
   --help, -h: 可选。打印使用帮助。


测试picker基本功能
---------------------

picker的基本功能是将RTL设计文件转换为C++/python/java等其他语言接口，以便于进行单元测试，下面以一个简单的例子来说明如何使用xspicker

从测试RTL到UT库生成
-------------------

1. 正常编写RTL即可，这里以一个简单的Cache为例。在 ``picker`` 项目的 ``example/Cache`` 目录下，有一个简单的Cache，即 ``Cache.v`` 这个文件。其顶层模块为 ``Cache``。
2. ``picker`` 工具会识别顶层模块，并额外生成一个 ``Cache_top.sv`` 文件，内含一个 ``Cache_top`` 作为对原顶层模块 ``in,out`` 端口的封装。该 ``systemverilog`` 文件中使用 ``DPI`` 导出对应的引脚端口。
3. 同时，会生成一个 ``Cache_top.v`` 文件，便于用于Verdi调试。（不使用Verdi时不重要）。 
4. 接下来 ``picker`` 工具会根据所给项目参数的不同，使用不同的仿真器编译项目，并生成导出了 ``DPI`` 接口的仿真动态库，调用该动态库与直接运行仿真器生成的二进制等价。
5. 后续该工具会根据所给参数不同，生成对应的 ``UT_Module`` 封装，封装主要是使用 ``xspcomm`` 的 ``XData`` 等数据类型，以及 ``XClock`` 等时钟类型，来封装对应 DUT模块。
6. 工具会将上一步生成的 ``UT_Module`` 封装编译为动态库，以便于在 ``C++/python/java`` 等语言中使用。

生成CPP工程
-------------

生成 cpp 工程，需要使用 ``-l cpp`` 参数。生成项目时，会先由仿真器生成基础DPI库，然后再基于cpp的UT封装基类完成初始化代码，并最终编译为UT动态库。调用该UT动态库的API即可实例化一个新的DUT。

使用 -e 生成基于CMake的示范项目，使用 -v 保留所有中间文件

.. code-block:: console

   mcv RTL_SOURCE.v -f DEPEND_1.v,DEPEND_2.v,/DATA/DEPENDS3.v,/DEPEND_DIR/ -w RTL.fst --sim verilator -C '-O3' -V '--vpi' -l cpp -e -i config.yaml

目录结构为

.. code-block:: console

   # 执行命令
   ./example/Cache/release-verilator.sh -l cpp -e 

   mcv_out/UT_Cache
   |-- CMakeLists.txt # CMake主函数，展示如何引入该cmake项目，以及必要的编译参数
   |-- Cache.cmake # 内含 XSPTarget 的函数定义，类似VERILATOR的cmake函数，用于生成最后的可执行文件。
   |-- Cache.v # 原始 top RTL文件
   |-- Cache_top.sv # 程序封装的 RTL 文件
   |-- Cache_top.v # 用于verdi的 RTL 文件
   |-- Makefile # 调用 cmake 的 makefile
   |-- UTCache_example # 生成的二进制
   |-- UT_Cache.cpp # 封装 DUT 的类，在此处有具体的初始化代码
   |-- UT_Cache.hpp 
   |-- UT_Cache_dpi.hpp # 封装 DUT 的 DPI 接口
   |-- dut_base.hpp # 基本的共有基类
   |-- example.cpp # 驱动示例代码
   |-- libDPICache.a # 除了VCS是动态库，其他都是静态库。静态库是非必要的
   `-- libUTCache.so # 作为UT的动态链接库

或不使用-e生成最小文件集合

.. code-block:: console

   mcv RTL_SOURCE.v

.. code-block:: console

   # 执行命令
   ./example/Cache/release-verilator.sh -l cpp -

   mcv_out/UT_Cache
   |-- UT_Cache.hpp
   |-- UT_Cache_dpi.hpp
   |-- dut_base.hpp
   |-- libDPICache.a
   `-- libUTCache.so

其中，在作为CMake工程引入时， ``XSPTarget`` 支持四个参数

1. ``RTL_MODULE_NAME``: 必需。一个字符串，表示 RTL 模块的名称。
#. ``EXECUTABLE_NAME``: 必需。一个字符串，表示要生成的可执行文件的名称。
#. ``CUSTOM_LIBS``: 可选。一个或多个自定义库的名称，这些库将被链接到目标中。
#. ``CUSTOM_LINK_OPTIONS``: 可选。一个或多个自定义链接选项，这些选项将被用于链接目标。


生成Python工程
---------------

生成 python 工程，需要使用 ``-l python`` 参数。生成python时，截至生成UT动态库时的流程与cpp一致。然后python再额外基于DPI和UT封装基类头文件，使用Swig生成封装层（python+cpp），最终编译为python-UT动态库作为兼容层。最后在Python中使用cpp基类的提供的API完成DUT的初始化代码。调用该类即可实例化。

与cpp生成工程的命令基本无区别。需要注意的是，调用动态库的路径是相对的，所以需要在调用python脚本时，将动态库的路径加入到环境变量中。其次，由于VCS的限制，使用VCS生成的结果时，需要使用 ``LD_PRELOAD`` 环境变量，预先加载VCS的动态库。

生成的python工程目录结构为

.. code-block:: console
   
   tree mcv_out/UT_Cache 
   mcv_out/UT_Cache
   |-- _UT_Cache.so # 生成的python-UT兼容层动态库，python通过该动态库调用与cpp一致的UT封装动态库
   |-- __init__.py # python lib init
   |-- libDPICache.a # DPI库
   |-- libUTCache.so # UT封装动态库
   |-- libUT_Cache.py # python-UT的实例化代码
   `-- xspcomm # 基础的基类封装，用于Python实现XData、XClock等基础功能
      |-- __init__.py
      |-- _pyxspcomm.so -> _pyxspcomm.so.0.0.1
      |-- _pyxspcomm.so.0.0.1
      |-- info.py
      `-- pyxspcomm.py