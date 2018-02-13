libandroid-shmem
================
System V shared memory (shmget, shmat, shmdt and shmctl) emulation on Android using ashmem for use in [Termux](https://termux.com/).

The shared memory segments it creates will be automatically destroyed when the creating process destroys them or dies, which differs from the System V shared memory behaviour.

Based on previous work in https://github.com/pelya/android-shmem.

Hacking
=======
The project can be developed on Android devices using Termux. Clone the repo and run `make` in the `tests/` folder after editing the library or test cases.

这是一个给Android的共享内存库
makefile并不能直接使用
我提供了ARM64/32已经编译完成的库，有需要可以直接下载使用

System V 使用ashmem在Android上模拟共享内存
这包括shmget，shmat，shmdt和shmctl函数还有一些源库中没有的函数（来自termux）
它不使用Binder服务，也不链接到Java库
它使用Linux sendmsg/recvmsg API来转移文件处理程序

要在Android中使用，请将libandroid-shmem文件夹添加到jni/目录并添加：
LOCAL_SHARED_LIBRARIES += libandroid-shmem
到任何使用libandroid-shmem库的Android.mk文件，添加 include $(call all-subdir-makefiles) 到 jni/Android.mk 中，以便构建libandroid-shmem模块，除非你已经以其他方式明确指定。

如果你从Android code链接到这个库，您需要将以下内容添加到您的CFLAGS中：（仅供参考）
-D_LINUX_IPC_H -Dipc_perm=debian_ipc_perm

最初建立这个lib最显而易见的原因是加速了Android上的XServer的Linux GUI程序
它可以用于Linux chroot中的Xtightvnc Xserver和独立的Xserver，它可以在这里下载：
https://sourceforge.net/projects/libsdl-android/files/apk/XServer-XSDL/
当然现在这个库可以让你运行更多的东西，比如数据库等



如果要让他工作在你的chroot下的Linux系统
请参考这个命令：

env LD_PRELOAD="/path/to/libandroid-shmem_aarch64.so" linux_command

他会直接引入这个函数库到你接下来的工作中
它创建的共享内存段将在所有者进程销毁它们或死亡时自动销毁，但Xserver和它的客户端不依赖于该功能。
