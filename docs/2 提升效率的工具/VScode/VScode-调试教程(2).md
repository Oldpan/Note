

VSCode是真正的生产力工具，尤其是前一阵子推出的`remote-SSH`功能，让远程轻量调试服务器代码效率有了质的飞越。不过本文不谈VSCode的`remote-ssh`功能。今天主要继续聊一下VSCode的对C++代码的debug功能。

之前的文章中，[利用VScode和cmake编译构建C++工程代码
](https://oldpan.me/archives/use-vscode-cmake-tools-build-project)和[如何对Pytorch进行“深入”的DEBUG](https://oldpan.me/archives/how-to-debug-pytorch-deeper)这两篇文章已经或简单或深入地讲解了VSCode的debug特性，而本文则对此进行补充，聊一些需要注意的地方。

![嘿嘿嘿](https://image.oldpan.me/%E5%98%BF%E5%98%BF%E5%98%BF.jpg)


## 不是每次都需要tasks.json

如果我们仅仅是想要借助VSCode的debug窗口，去debug我们已经生成的可执行文件，那我们**完全**不需要`tasks.json`，这个文件是提供编译时的帮助文件，设置好`tasks.json`参数后通过执行其设置好的gcc命令等等去编译。如果我们已经有编译好的，就不需要这个东西了。

我们只需要`launch.json`文件，以下是我配置好的一个example：


```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "path/to/bin",
            "args": [
                "--model-repository=/test_model_repository_debug/centernet-trt-ensemble",
                "--http-port=8007",
                "--cuda-memory-pool-byte-size=0:134217728"
            ],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [{"name":"LD_LIBRARY_PATH", "value":"${LD_LIBRARY_PATH}:/usr/local/cuda/lib64:/home/re2/obj/so"},
                            {"name":"CUDA_VISIBLE_DEVICES","value": "4"}],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "miDebuggerPath": "/usr/bin/gdb",
            "logging": { "engineLogging": true }
        }
    ]
}
```
以下分析一下我们常用的几个地方：
- 去掉`preLaunchTask": "g++"`，因为我们不需要`tasks.json`
-  `"request": "launch"`一般都是`launch`，如果需要捕获进程进行attach则设置为`attach`，可以看[如何对Pytorch进行“深入”的DEBUG](https://oldpan.me/archives/how-to-debug-pytorch-deeper)
- `"program": "path/to/bin"`编译出来的可执行文件地址
- ` "args": [
            ],`命令行参数，具体怎么写看上头的
- ` "environment": [{"name":"CUDA_VISIBLE_DEVICES","value": "4"}],`环境变量，如果我们的可执行文件需要设置环境变量则修改这个，修改格式具体看上头的例子

其他的不常用，就不介绍了，还想要了解的看官方文档 https://code.visualstudio.com/docs/editor/debugging 。

![debugging_hero](http://image.oldpan.me/debugging_hero.png)


