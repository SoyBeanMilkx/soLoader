# soLoader

## 项目概述
本项目实现了一个自定义的动态链接器(linker)，能够加载和运行 Android 平台的共享库(.so 文件)。通过此项目，你可以绕过系统默认的 linker，实现自定义的库加载逻辑。

## 功能特性
✅ 自定义 ELF 文件加载器

✅ 支持 Android 平台的 SO 文件加载

✅ 实现基本的重定位功能

✅ 支持符号查找和解析


## 使用示例
```c
#include "elf_loader.h"

int (*yuuki_test_func) (int, int) = nullptr;

int main(int argc, char* argv[]) {

    if (argc < 2) {
        printf("Usage: %s <so_file_path>\n", argv[0]);
        return 1;
    }

    LOGI("Starting custom linker for: %s", argv[1]);

    // 检查文件是否存在
    if (access(argv[1], F_OK)!= 0) {
        LOGE("File does not exist: %s", argv[1]);
        return 1;
    }

    if (access(argv[1], R_OK) != 0) {
        LOGE("File is not readable: %s", argv[1]);
        return 1;
    }

    ElfLoader loader;
    if (loader.LoadLibrary(argv[1])) {
        printf("Successfully loaded %s\n", argv[1]);

        void* test_func = loader.GetSymbol("yuuki_test");
        if (test_func) {
            printf("Found yuuki_test function at %p\n", test_func);
            yuuki_test_func = (int (*)(int, int)) test_func;

            // 测试函数调用
            printf("Testing function call: 1 + 1 = %d\n", yuuki_test_func(1, 1));
            printf("Testing function call: 5 + 3 = %d\n", yuuki_test_func(5, 3));
        } else {
            printf("Failed to find yuuki_test function\n");
        }

        return 0;
    } else {
        printf("Failed to load %s\n", argv[1]);
        return 1;
    }
}

```


## 实现原理

本项目主要实现了以下核心功能：

1. **ELF 文件解析**：解析 ELF 文件头、程序头、节区头等结构

2. **内存映射**：将 SO 文件按需映射到内存

3. **重定位处理**：处理各种重定位类型（R_ARM_JUMP_SLOT, R_ARM_GLOB_DAT 等）

4. **符号解析**：实现符号查找和解析逻辑

5. **依赖加载**：递归加载依赖的 SO 文件


## 应用场景

- 插件化架构实现

- 热修复技术

- SO 文件保护与加固

- 动态加载研究

## 注意事项

1. 本项目仅供学习和研究使用

2. 某些 Android 版本可能有兼容性问题

3. 复杂的 SO 文件可能需要额外的重定位支持

4. 生产环境使用需充分测试
