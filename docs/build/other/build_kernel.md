# 编译内核

支持架构：`x86_64` `riscv64`

`x86_64` 支持工具链：
* `3.2.0` Xuantie-900 Linux 6.6.0 版本v3.2.0
* `14` 主线 RISC-V GCC 14交叉编译器（默认）

`riscv64` 支持工具链：
* `rv` 主线 GCC 编译器

通过`TOOLCHAIN_VERSION`变量选择要使用的编译器

## 使用方法

```bash
git clone https://github.com/revyos/xuantie-kernel-docker
cd xuantie-kernel-docker
docker build --build-arg TOOLCHAIN_VERSION=14 -t xuantie900:linux14 . # 根据支持架构确定 TOOLCHAIN_VERSION 参数
docker run -it --rm -v LOCAL_PATH:/output xuantie900:linux14 bash # LOCAL_PATH 为编译好的内核存放目录
./mkkernel_x64.sh # 根据 TOOLCHAIN_VERSION 配置选择 mkkernel_xuantie.sh 或 mkkernel_riscv64.sh
```

整个过程执行完成后，构建好的内核deb包将会被拷贝到`LOCAL_PATH`指定的目录中，然后使用`dpkg`安装即可。

```bash
sudo dpkg -i [PACKAGE]
```

编译过程会产生多个结果：
* linux-headers.deb(需要编译内核模块或内核开发时安装)
* linux-image-dbg.deb(调试符号，需要调试内核时安装)
* linux-image.deb(内核，必装)
* linux-libc-dev.deb(libc接口，一般不安装，使用通用版即可)

## 注意事项

1. 如需更改编译的版本，可在进入docker环境（输入`bash`）后自由修改
2. `x86_64`环境下交叉编译出的`linux-header`包可能包含`x86_64`的头文件。如需进行内核开发或使用如`zfs`之类的DKMS模块，请在RISC-V环境下使用`TOOLCHAIN_VERSION=rv`编译。
