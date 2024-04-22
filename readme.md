此处使用 vscode 搭建 vulkan 开发环境

#### c/c++

###### c/c++环境

下载MinGW 64bit

配置扩展如下

![image-20240422142829428](E:\markdown_txt\pic\image-20240422142829428.png)

`.vscode` 创建文件 `c_cpp_properties.json`

修改"compilerPath" 项为g++路径

```json
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "windowsSdkVersion": "10.0.17763.0",
            "compilerPath": "E:\\MinGW\\mingw64\\bin\\g++.exe",
            "cStandard": "c17",
            "cppStandard": "c++20",
            "intelliSenseMode": "windows-gcc-x64",
            "compilerArgs": [
                "-Wall"
            ]
        }
    ],
    "version": 4
}
```

生成调试文件，此时tasks.json应该已经正常生成

![image-20240422143339412](E:\markdown_txt\pic\image-20240422143339412.png)

运行成功

![image-20240422143516235](E:\markdown_txt\pic\image-20240422143516235.png)



###### c/c++格式化

这里使用的是clang-format插件

![image-20240422143011286](E:\markdown_txt\pic\image-20240422143011286.png)

![image-20240422143029971](E:\markdown_txt\pic\image-20240422143029971.png)

![image-20240422143047224](E:\markdown_txt\pic\image-20240422143047224.png)

将 Executable 修改为 clang-format.exe 的路径，Style修改为 .clang-format 的路径

#### vulkan

###### vulkanSDK安装

开发Vulkan应用程序所需的最重要的组件就是SDK。它包括核心头文件、标准的Validation layers及调试工具集、和驱动Loader

[vulkanSDK](https://vulkan.lunarg.com/sdk/home#windows)

安装完成后可以在安装目录下运行vkcube.exe验证是否支持vulkan

![image-20240422143708024](E:\markdown_txt\pic\image-20240422143708024.png)

同时将include 中的 全部内容 复制到 include 中

![image-20240422145503701](E:\markdown_txt\pic\image-20240422145503701.png)

将 Lib 中的 vulkan-1.lib 复制到 lib 内

![image-20240422150435257](E:\markdown_txt\pic\image-20240422150435257.png)

###### GLFW

[GLFW](https://www.glfw.org/download.html)

此处使用的是64bit的库

解压后将include和lib中的文件复制到对应的 include 和 lib 内

![image-20240422144928875](E:\markdown_txt\pic\image-20240422144928875.png)

###### GLM

[GLM](https://github.com/g-truc/glm)

同理，将glm文件夹复制到include 内

<img src="E:\markdown_txt\pic\image-20240422145717041.png" alt="image-20240422145717041" style="zoom: 50%;" />

###### 修改 c_cpp_properties.json 和 tasks.json

修改 `c_cpp_properties.json` , 添加 `includePath`  :  `"${workspaceFolder}/include/**"`

```json
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**",
                "${workspaceFolder}/include/**", // 各种库的include
                "${workspaceFolder}/src/include/**" // 自己的include
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "windowsSdkVersion": "10.0.17763.0",
            "compilerPath": "E:\\MinGW\\mingw64\\bin\\g++.exe",
            "cStandard": "c17",
            "cppStandard": "c++20",
            "intelliSenseMode": "windows-gcc-x64",
            "compilerArgs": [
                "-Wall"
            ]
        }
    ],
    "version": 4
}
```

修改 `tasks.json`

添加 include 目录以及库目录，同时链接两个库，修改生成的可执行文件的path

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "E:\\MinGW\\mingw64\\bin\\g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${workspaceFolder}\\out\\${fileBasenameNoExtension}.exe", // 生成的exe path
                "-Wall",
                "-I",
                "${workspaceFolder}\\include", // 各种库的 include path
                "-I",
                "${workspaceFolder}\\src\\include", // 自己的 include path
                "-L",
                "${workspaceFolder}\\lib", // lib path
                "-lglfw3dll",
                "-lvulkan-1",
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

运行如下示例代码

```c++
// https://vulkan-tutorial.com/Development_environment
#define GLFW_INCLUDE_VULKAN
#include <GLFW/glfw3.h>

#define GLM_FORCE_RADIANS
#define GLM_FORCE_DEPTH_ZERO_TO_ONE
#include <glm/mat4x4.hpp>
#include <glm/vec4.hpp>

#include <iostream>

int main() {
    glfwInit();

    glfwWindowHint(GLFW_CLIENT_API, GLFW_NO_API);
    GLFWwindow* window = glfwCreateWindow(800, 600, "Vulkan window", nullptr, nullptr);

    uint32_t extensionCount = 0;
    vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);

    std::cout << extensionCount << " extensions supported" << std::endl;

    glm::mat4 matrix;
    glm::vec4 vec;
    auto test = matrix * vec;

    while (!glfwWindowShouldClose(window)) {
        glfwPollEvents();
    }

    glfwDestroyWindow(window);

    glfwTerminate();

    return 0;
}

```



运行成功会打开空白窗口

![image-20240422153425514](E:\markdown_txt\pic\image-20240422153425514.png)

至此，c/c++ vulkan 环境就搭建好了

