# nextpnr
支持ice40，ecp5，Artix-7，UltraScale+

## nextpnr-ice40
## nextpnr-ecp5

## nextpnr-Artix-7
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
  




  
  
  
## nextpnr-UltraScale+


