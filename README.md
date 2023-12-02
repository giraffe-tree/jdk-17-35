# 说明

代码来源: https://github.com/openjdk/jdk
git tag: jdk-17+35 

## release 编译

1. `git clone -b jdk-17+35 git@github.com:openjdk/jdk.git`  先下载拉取下 jdk17 的分支
2. `bash configure --with-boot-jdk=/Users/didi/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home`
    1. If`configure`fails due to missing dependencies (to either the[toolchain](https://github.com/openjdk/jdk17/blob/master/doc/building.md#native-compiler-toolchain-requirements),[build tools](https://github.com/openjdk/jdk17/blob/master/doc/building.md#build-tools-requirements),[external libraries](https://github.com/openjdk/jdk17/blob/master/doc/building.md#external-library-requirements)or the[boot JDK](https://github.com/openjdk/jdk17/blob/master/doc/building.md#boot-jdk-requirements)), most of the time it prints a suggestion on how to resolve the situation on your platform. Follow the instructions, and try running`bash configure`again.
    2. 我这里使用的是 jdk16 作为 boot jdk
3.  `make images`
    1. 默认 release 版本, 不到10分钟

    ```bash
    ...
    Compiling 1 files for CLASSLIST_JAR
    Creating support/demos/image/jfc/SwingSet2/SwingSet2.jar
    Creating support/classlist.jar
    Creating support/demos/image/jfc/J2Ddemo/J2Ddemo.jar
    Creating jdk.jlink.jmod
    Creating java.base.jmod
    Creating jdk image
    Creating CDS archive for jdk image
    Creating CDS-NOCOOPS archive for jdk image
    Stopping sjavac server
    Finished building target 'images' in configuration 'macosx-x86_64-server-release'
    ```

4. `./build/*/images/jdk/bin/java -version`   完成~~~

```bash
openjdk version "17-internal" 2021-09-14
OpenJDK Runtime Environment (build 17-internal+0-adhoc.xxx.jdk)
OpenJDK 64-Bit Server VM (build 17-internal+0-adhoc.xxx.jdk, mixed mode, sharing)
```

## debug 版本编译

1. 代码已经 clone 过了, 直接用
2. configure
    1. 我本地需要 `unset _JAVA_OPTIONS`  不清楚是不是个例
    2. slowdebug 版本 `bash configure --with-boot-jdk=/Users/xxx/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home --with-debug-level=slowdebug`
    3. fastdebug 版本`bash configure --with-boot-jdk=/Users/didi/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home --with-debug-level=fastdebug`
3. make
    1. slowdebug `make images CONF=macosx-x86_64-server-slowdebug`
        1. 差不多11分钟跑完
        2. 记得之前编译 jdk12 跑了好久, 电脑风扇轰轰响
    2. fastdebug `make images CONF=macosx-x86_64-server-fastdebug`
        1. 也是差不多11分钟

```bash
$ ./build/macosx-x86_64-server-slowdebug/images/jdk/bin/java -version
openjdk version "17-internal" 2021-09-14
OpenJDK Runtime Environment (slowdebug build 17-internal+0-adhoc.didi.jdk)
OpenJDK 64-Bit Server VM (slowdebug build 17-internal+0-adhoc.xxx.jdk, mixed mode, sharing)
```

## 我遇到的问题

### 在进行 `configure` 时遇到

```bash
configure: Found potential Boot JDK using JAVA_HOME
configure: You have _JAVA_OPTIONS or JAVA_TOOL_OPTIONS set. This can mess up the build. Please use --with-boot-jdk-jvmargs instead.
configure: Java reports: "Picked up _JAVA_OPTIONS: -DTEST_HOME_DIR=/Users/xxx java version "1.8.0_291" Java(TM) SE Runtime Environment (build 1.8.0_291-b10) Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)".
configure: error: Cannot continue
/Users/xxx/Documents/env/openjdk-source/openjdk17/jdk/build/.configure-support/generated-configure.sh: line 84: 5: Bad file descriptor
configure exiting with result code 1
```

修复下

```bash
unset _JAVA_OPTIONS
```

再次 `bash configure`

```bash
configure: Found potential Boot JDK using java(c) in PATH
configure: Potential Boot JDK found at /usr is incorrect JDK version (java version "1.8.0_291" Java(TM) SE Runtime Environment (build 1.8.0_291-b10) Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)); ignoring
configure: (Your Boot JDK version must be one of: 16 17)
configure: Found potential Boot JDK using well-known locations (in /Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk)
configure: Potential Boot JDK found at /Library/Java/JavaVirtualMachines/jdk1.8.0_291.jdk/Contents/Home is incorrect JDK version (java version "1.8.0_291" Java(TM) SE Runtime Environment (build 1.8.0_291-b10) Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)); ignoring
configure: (Your Boot JDK version must be one of: 16 17)
configure: Could not find a valid Boot JDK. OpenJDK distributions are available at http://jdk.java.net/.
configure: This might be fixed by explicitly setting --with-boot-jdk
configure: error: Cannot continue
/Users/xxx/Documents/env/openjdk-source/openjdk17/jdk/build/.configure-support/generated-configure.sh: line 84: 5: Bad file descriptor
```

从  https://jdk.java.net/archive/ 下载 openjdk 16

```bash
bash configure --with-boot-jdk=/Users/xxx/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home
```

成功

```bash
A new configuration has been successfully created in
/Users/xxx/Documents/env/openjdk-source/openjdk17/jdk/build/macosx-x86_64-server-release
using configure arguments '--with-boot-jdk=/Users/xxx/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home'.

Configuration summary:
* Name:           macosx-x86_64-server-release
* Debug level:    release
* HS debug level: product
* JVM variants:   server
* JVM features:   server: 'cds compiler1 compiler2 dtrace epsilongc g1gc jfr jni-check jvmci jvmti management nmt parallelgc serialgc services shenandoahgc vm-structs zgc'
* OpenJDK target: OS: macosx, CPU architecture: x86, address length: 64
* Version string: 17-internal+0-adhoc.xxx.jdk (17-internal)

Tools summary:
* Boot JDK:       openjdk version "16.0.2" 2021-07-20 OpenJDK Runtime Environment (build 16.0.2+7-67) OpenJDK 64-Bit Server VM (build 16.0.2+7-67, mixed mode, sharing) (at /Users/xxx/Documents/env/java/jdk16/jdk-16.0.2.jdk/Contents/Home)
* Toolchain:      clang (clang/LLVM from Xcode 12.5.1)
* C Compiler:     Version 12.0.5 (at /usr/bin/clang)
* C++ Compiler:   Version 12.0.5 (at /usr/bin/clang++)

Build performance summary:
* Cores to use:   12
* Memory limit:   16384 MB
```

-------

# Welcome to the JDK!

For build instructions please see the
[online documentation](https://openjdk.java.net/groups/build/doc/building.html),
or either of these files:

- [doc/building.html](doc/building.html) (html version)
- [doc/building.md](doc/building.md) (markdown version)

See <https://openjdk.java.net/> for more information about
the OpenJDK Community and the JDK.
