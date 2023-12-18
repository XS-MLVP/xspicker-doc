快速使用
===========

.. _installation:

安装xspicker
-------------

源码安装:
编译依赖: cmake(>=3.11), gcc(支持c++20), python3(>=3.8), verilator(==4.218), 

.. code-block:: console

   $git clone https://gitee.com/yaozhicheng/mcv.git
   $cd mcv
   $make
   $sudo make install

测试是否安装成功
-----------------

执行xsp命令，如果出现以下信息，则安装成功

.. code-block:: console

 XDut Generate.
 Convert DUT(*.v) to C++ DUT libs.
 Usage:
  XDut Gen [OPTION...]

  -f, --file arg                DUT .v/.sv source file, contain the top
                                module
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
  -i, --internal arg            Exported internal signal config file,
                                default is empty, means no internal pin
                                (default: "")
  -F, --frequency arg           Set the frequency of the **only VCS** DUT,
                                default is 100MHz, use Hz, KHz, MHz, GHz as
                                unit (default: 100MHz)
  -w, --wave_file_name arg      Wave file name, emtpy mean don't dump wave
                                (default: "")
  -V, --vflag arg               User defined simulator compile args, split
                                by comma. Eg: -v
                                -x-assign=fast,-Wall,--trace || -C vcs,-cc
                                -i filelist.txt  (default: "")
  -C, --cflag arg               User defined gcc/clang compile command,
                                split by comma.
                                Eg:-O3,-std=c++17,-I./include
                                ||-O3,-std=c++17,-I./include (default: "")
  -v, --verbose                 Verbose mode
  -e, --example                 Build example project, default is OFF
  -h, --help                    Print usage


测试xspicker基本功能
---------------------

xspicker的基本功能是将RTL设计文件转换为C++/python/java等其他语言接口，以便于进行单元测试，下面以一个简单的例子来说明如何使用xspicker

编写测试RTL
-------------

以下是一个简单的RTL设计，包含一个简单的寄存器，一个简单的计数器，一个简单的状态机，以及一个简单的状态机控制逻辑

.. code-block:: verilog

   module simple_dut(
       input wire clk,
       input wire rst_n,
       input wire [7:0] data_in,
       output wire [7:0] data_out
   );
   
   reg [7:0] reg_data;
   reg [7:0] reg_data_next;
   reg [7:0] reg_data_next_next;
   reg [7:0] reg_data_next_next_next;
   reg [7:0] reg_data_next_next_next_next;
   reg [7:0] reg_data_next_next_next_next_next;
   reg [7:0] reg_data_next_next_next_next_next_next;
   reg [7:0] reg_data_next_next_next_next_next_next_next;
   reg [7:0] reg_data_next_next_next_next_next_next_next_next;
   reg [7:0] reg_data_next_next_next_next_next_next_next_next_next;
   reg [7:0] reg_data_next_nex

生成CPP工程
-------------

TBD

生成Python工程
---------------

TBD

生成CPP工程
-------------

TBD
