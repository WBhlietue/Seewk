# Seewk GUI v0.0.1

Vulkan-Based C++ Graphics Rendering Framework

## 项目介绍

This project is a desktop application framework that uses C++ with Vulkan as the rendering backend to draw graphical user interfaces.

The framework adopts a programming model similar to `.NET C# WinForms`. To create an application, users define their own windowclass by inheriting from the provided `Form` base class and override the `OnLoad()` method to perform window initialization.

The application's `main` function is managed internally by the framework. Instead, users only need to implement the framework-provided entry function, which serves as the starting point of the application

## Key Features

-   Uses modern C++ modules to eliminate macro pollution and reduce compilation times.
-   High-performance and lightweight.

### Upcoming Updates

-   Text rendering and automatic layout support.
-   Customizable shaders and rendering pipelines.
-   Asset packaging system for images and other resources.
-   Multi-platform support

# Getting Started

## 技术栈

- C++20 or later  
  This project primarily utilizes the modern C++20 `Modules` feature instead of traditional header files, reducing macro pollution and improving compilation efficiency.
-   Vulkan  
     Most Windows computers from the past decade should already have `vulkan-1.dll` installed, allowing development without a separate Vulkan SDK installation. If shader editing or compilation is required, install the Vulkan SDK and use its provided `glslangValidator` tool to compile shaders.
    ### Core Libraries Used
    -   Vulkan: Provides the vulkan.hpp header file for development.
    -   glfw: Window Manager
    -   stb: Image Loading
    #### All of the libraries mentioned above can be automatically installed through vcpkg.

## Environment Setup

### Windows 10/11

-   MSVC 2022 or later
-   CMake 3.30 or later
-   Ninja (Optional)
-   vcpkg (The latest version is sufficient)

#### You can also use an automated environment setup tool. [Seewk Runner](https://github.com/WBhlietue/seewkRunner)。

When using this tool, please do not install `vcpkg` together with `MSVC` through the `Visual Studio Installer`, as this may cause unexpected errors.

This tool automatically installs CMake, Ninja, vcpkg, and other required tools. When running the project, it also automatically configures MSVC's `vcvarsall.bat` file and the `VCPKG_ROOT` environment variable.

If you are unfamiliar with installing MSVC, you can follow the installation steps provided in the `README` file of [Seewk Runner](https://github.com/WBhlietue/seewkRunner).

## Installation and Build

This tutorial uses the commands provided by the [Seewk Runner](https://github.com/WBhlietue/seewkRunner) tool. You can also directly use commands such as `cmake` and `ninja` to configure, build, and compile the project.

1. Clone the Repository

```bat
git clone https://github.com/WBhlietue/Seewk.git
```

2. Navigate to the project root directory and build the project.

```bat
seewk make
```

3. Build and run the project.

```bat
seewk start
```

## Using the Framework

- Create a `main.cpp` file in the `src` directory and open it.
In this file, import the core library of the framework.

#### src/main.cpp

```cpp
//The header file used here is only for testing purposes. It will be converted into a module and merged into the `seewk` module in the future.

#include <core/main/seewkObject.hpp>
#include <core/core.h>
import seewk;
```

- Define two functions as the entry point and exit point of the application.

#### src/main.cpp

```cpp
void OnStart() // Called when the program starts
{
}

void OnOver() // Called when the program exits.
{
}
```

- Create a custom class that inherits from the `Form` base class and override the `OnLoad()` function.

#### src/main.cpp

```cpp
class Form1 : public seewk::main::Form
{
  void OnLoad() override
  {
  }
}
```

Each `Form` represents a window. You can create multiple subclasses that inherit from `Form` to implement multiple windows. However, this feature currently has some bugs and is not recommended for use at the moment.

- Now add a rectangle to the `Form1` window using `CreateObject()`.

#### src/main.cpp

```cpp
  void OnLoad() override
  {
    CreateObject();
  }
```

You can also use method chaining to initialize the rectangle directly. This will draw a 500x500 blue square in the center of the window.

#### src/main.cpp

```cpp
  void OnLoad() override
  {
    CreateObject()
      .SetPosition(Vec2(0, 0))
      .SetSize(Vec2(500, 500))
      .SetColor(Color(0, 0, 1, 1));
  }
```

You can also store the reference in a variable for initialization.

#### src/main.cpp

```cpp
  void OnLoad() override
  {
    SeewkObject& obj = CreateObject();
    obj.SetPosition(Vec2(0, 0));
    obj.SetSize(Vec2(500, 500));
    obj.SetColor(Color(0, 1, 1, 1));
  }
```

- If you need to use this object outside of the current scope, you can store it as a pointer in a class member variable.

#### src/main.cpp

```cpp
class Form1 : public seewk::main::Form
{
  SeewkObject *obj = nullptr;
  void OnLoad() override
  {
    obj = &CreateObject()
      .SetPosition(Vec2(0, 0))
      .SetSize(Vec2(500, 500))
      .SetColor(Color(0, 0, 1, 1));
  }
}
```

- To use textures, override the `PreLoad()` function in the subclass inherited from `Form`. Load the texture in this function and store it as a member variable. Then use the UI object's `SetTexture()` function to apply the texture.

#### src/main.cpp

```cpp
class Form1 : public seewk::main::Form
{
  SeewkObject *obj = nullptr;
  int texture = -1;
  void PreLoad() override
  {
    texture = GetTexture("assets/moon.png");
  }
  void OnLoad() override
  {
    obj = &CreateObject()
      .SetPosition(Vec2(0, 0))
      .SetSize(Vec2(500, 500))
      .SetColor(Color(0, 0, 1, 1))
      .SetTexture(texture);
  }
}
```

- If you need a loop, override the `OnLoop()` function in the subclass inherited from `Form`. This function is called once per frame.

#### src/main.cpp

```cpp
class Form1 : public seewk::main::Form
{
  void OnLoop(float deltaTime) override
  { // 每帧调用一次，1 / deltaTime 的计算结果为目前的帧数
  }
}
```

- Then, you need to create this window in the previously implemented `OnStart()` function.

#### src/main.cpp

```cpp
void OnStart()
{
    seewk::WindowManager::CreateWindow<Form1>();
}
```

- Then run `seewk start` to automatically build and launch the window.

## 进阶用法

- UI The object also provides the following functions.

```cpp
Vec2 GetPosition(); // 获得物件的位置
SeewkObject &SetPosition(Vec2 position); // 更改物件的位置
Vec2 GetSize(); // 获得物件的大小
SeewkObject &SetSize(Vec2 size) ; // 更改物件的大小
Color GetColor(); // 获得物件的颜色
SeewkObject &SetColor(Color c); // 更改物件的颜色
SeewkObject &SetTexture(int textureId); // 更改物件的贴图
int GetBorderRadius(); // 获得物件的圆角值
SeewkObject &SetBorderRadius(int r); // 更改物件的圆角值

// 一下函数为为物件设置可互动的功能
SeewkObject &SetMouseEnter(std::function<void()> f); // 当鼠标进入范围时调用
SeewkObject &SetMouseExit(std::function<void()> f); // 当鼠标出范围时调用
SeewkObject &SetMouseStay(std::function<void()> f); // 当鼠标在范围时一直调用
SeewkObject &SetMouseUp(std::function<void(int)> f); // 当松开鼠标按键时调用，参数为鼠标键的编号
SeewkObject &SetMouseDown(std::function<void(int)> f); // 当按下鼠标按键时调用，参数为鼠标键的编号
SeewkObject &SetClick(std::function<void(int)> f); // 当点击鼠标按键时调用，参数为鼠标键的编号
SeewkObject &SetDrag(std::function<void(int)> f); // 当使用鼠标拖拽时调用，参数为鼠标键的编号
```

- The functions above can be used to implement the following example. This example provides the complete code for a textured rectangle with a dynamically changing corner radius that can be clicked and dragged. It can be copied and run directly.

#### src/main.cpp

```cpp
#include <core/main/seewkObject.hpp>
#include <core/core.h>
import seewk;

class Form1 : public seewk::main::Form
{
  public:
  int border = 30;
  int borderSpeed = 1;
  SeewkObject *obj = nullptr;
  Vec2 objPosOffset;
  int texture = -1;

  void PreLoad() override
  {
    texture = GetTexture("assets/moon.png");
  }

  void OnLoad() override
  {
    obj = &CreateObject()
      .SetPosition(Vec2(0, 0))
      .SetSize(Vec2(500, 500))
      .SetColor(Color(1, 1, 1, 1))
      .SetBorderRadius(border)
      .SetClick([this](int button) { std::cout << "click\n";})
      .SetMouseDown([this](int button) 
        { this->objPosOffset = GetMousePosition() - this->obj->GetPosition(); })
      .SetDrag([this](int button)
        {
          Vec2 mousePosition = GetMousePosition();
          this->obj->SetPosition(mousePosition - this->objPosOffset); 
        })
      .SetTexture(texture);
  }
  void OnLoop(float deltaTime) override
  {
    border += borderSpeed;
    if (border < 10 || border > 100)
    {
      borderSpeed *= -1;
    }
    obj->SetBorderRadius(border);
  }
};

void OnStart()
{
  seewk::WindowManager::CreateWindow<Form1>();
}

void OnOver()
{
  std::cout << "over\n";
}
```