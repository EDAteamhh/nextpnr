# nextpnr
支持ice40，ecp5，Artix-7，UltraScale+

## 1、nextpnr-ice40
* 预备安装：
  - CMake 3.3 or later
  - Modern C++11 compiler (clang-format required for development)
  - Qt5 or later (qt5-default for Ubuntu 16.04)
  - Python 3.5 or later, python3-dev
  - Boost libraries(libboost-dev libboost-filesystem-dev libboost-thread-dev libboost-program-options-dev libboost-python-dev libboost-iostreams-dev libboost-dev or libboost-all-dev for Ubuntu)
  - Eigen3 (libeigen3-dev for Ubuntu)
  
* 下载 Yosys ：
  ```
  git clone https://github.com/YosysHQ/yosys.git yosys
  cd yosys
  make -j$(nproc)
  sudo make install
  ```  
* 下载 IceStorm 工具包：
  ```
  git clone https://github.com/YosysHQ/icestorm.git icestorm
  cd icestorm
  make -j$(nproc)
  sudo make install  # 会将icestorm安装至/usr/local本地目录
  ```
* 下载nextpnr，执行下述代码会在 nextpnr 目录下生成"nextpnr-ice40"文件：
  ```
  git clone https://github.com/YosysHQ/nextpnr nextpnr
  cd nextpnr
  cmake -DARCH=ice40 -DCMAKE_INSTALL_PREFIX=/usr/local
  make -j$(nproc)
  sudo make install
  ```
* 运行例子：
  ```
  cd ice40/examples/blinky
  yosys -p 'synth_ice40 -top blinky -json blinky.json' blinky.v               # 综合，生成网表blinky.json
  nextpnr-ice40 --hx1k --json blinky.json --pcf blinky.pcf --asc blinky.asc   # 布局、布线
  icepack blinky.asc blinky.bin                                               # 生成二进制比特流文件
  ```
  如果想要图形化界面，第三行换成如下命令：
  ```
  nextpnr-ice40 --json blinky.json --pcf blinky.pcf --asc blinky.asc --gui
  ```
  然后通过界面的功能按钮实现打包，布局，布线和写出相应输出文件。
  
## 2、nextpnr-ecp5
* 下载并安装Project Trellis工具：
  ```
  git clone --recursive https://github.com/YosysHQ/prjtrellis
  cd libtrellis
  cmake -DCMAKE_INSTALL_PREFIX=/usr/local
  make -j$(nproc)
  sudo make install
  ```
* 在 nextpnr 目录下执行如下代码，生成 nextpnr-ecp5 文件：
   ```
   cmake -DARCH=ecp5 -DTRELLIS_INSTALL_PREFIX=/usr/local
   make -j$(nproc)
   sudo make install
   ```
* 运行例子：
  ```
  cd ecp5/synth
  yosys -p 'synth_ecp5 -noccu2 -nomux -nodram -json blinky.json' blinky.v              # 综合，生成网表blinky.json
  nextpnr-ecp5 --12k --json blinky.json --package CABGA381 --textcfg blinky   # 布局、布线
  ecppack blinky blinky.bin                                               # 生成二进制比特流文件
  ```
  如果想要图形化界面，第三行换成如下命令：
  ```
  nextpnr-ecp5 --12k --json blinky.json --package CABGA381 --textcfg blinky --gui
  ```
  然后通过界面的功能按钮实现打包，布局，布线和写出相应输出文件。
  


## 3、nextpnr-Artix-7
* 下载nextpnr-xilinx：git clone https://github.com/daveshah1/nextpnr-xilinx/
* 进入 nextpnr-xilinx 目录，下载子模块：
  - git submodule init 
  - git submodule update
  - 注意：没有这一步，子模块“tests” “xilinx/external/prjxray-db” “xilinx/external/nextpnr-xilinx-meta”中会缺数据文件
* 下载Project Xray：git clone https://github.com/SymbiFlow/prjxray
* 进入 prjxray 目录，构建Project Xray：
  - 安装 Vivado 2017.2 （必须是这个版本） ，创建环境变量：export XRAY_VIVADO_SETTINGS=/opt/Xilinx/Vivado/2017.2/settings64.sh
  - 下载子模块：git submodule update --init --recursive
  - 安装cmake：sudo apt-get install cmake # version 3.5.0 or later required
  - 构建工具：make build
  - 在本地安装Python环境：
  ```
  sudo apt-get install virtualenv python3 python3-pip python3-virtualenv python3-yaml
  make env
  ```
  - 运行任何其他命令之前，设置环境：`source settings/artix7.sh`
  - 下载当前的稳定版本：`./download-latest-db.sh`
  - Pick a fuzzer (or write your own) and run：
  ```
  cd fuzzers/010-clb-lutinit
  make -j$(nproc) run
  ```
  - 创建HTML文档：
  ```
  cd htmlgen
  python3 htmlgen.py
  ```
  **构建完毕**。
* 回到 nextpnr-xilinx 目录，下载：yosys (在 yosys 目录下执行`make`，需要编译通过)
* 在 nextpnr-xilinx 目录下执行下述代码，生成“nextpnr-xilinx”可执行文件，以进行打包、布局和布线：
```
cmake -DARCH=xilinx
make
```
* 执行“xilinx/examples/arty-a35”中的例子 - 由XRay生成芯片设备数据库
  - 设置环境变量：export XRAY_DIR=/path/to/prjxray
  - 执行代码：
  ```
  pypy3 xilinx/python/bbaexport.py --device xc7a35tcsg324-1 --bba xilinx/xc7a35t.bba  # 生成.bba格式的芯片数据库
  ./bbasm -l xilinx/xc7a35t.bba xilinx/xc7a35t.bin    # -l表示little endian，指明系统的字节存储顺序是little
  cd xilinx/examples/arty-a35
  ./blinky.sh
  ```
  即可生成**比特流文件**。
  




  
  
  
## 4、nextpnr-UltraScale+


