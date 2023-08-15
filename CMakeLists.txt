#THIS FILE IS AUTO GENERATED FROM THE TEMPLATE! DO NOT CHANGE!
# 设置交叉编译的系统名称为 Generic，系统版本为 1
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
# 指定最低的 CMake 版本要求为 3.20
cmake_minimum_required(VERSION 3.20)

# specify cross compilers and tools
# 指定交叉编译的工具
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# project settings
# 项目设置
project(CLionSTM32Demo C CXX ASM)
# CMake 中的一个命令，用于设置 C++ 编译的标准版本。在这个特定的命令中，它将 C++ 标准设置为 C++17。
set(CMAKE_CXX_STANDARD 17)
#是 CMake 中的一个命令，用于设置 C 编译的标准版本。在这个特定的命令中，它将 C 标准设置为 C11。
set(CMAKE_C_STANDARD 11)

#Uncomment for hardware floating point
#add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
#add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
#add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)

#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)
# 添加 ARM Cortex-M3 处理器相关的编译选项
add_compile_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
# 添加编译选项，优化代码大小和资源使用
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)


# uncomment to mitigate c++17 absolute addresses warnings
# 设置 C++ 编译选项，以减轻 C++17 的绝对地址警告
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-register")

# 根据 CMake 构建类型设置编译选项
if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release") #这是一个条件判断语句，检查 CMake 构建类型是否为 "Release"（发布）模式。
    message(STATUS "Maximum optimization for speed") #如果构建类型是 "Release"，则会输出一条状态消息，指示当前选择了最大化的速度优化。
    add_compile_options(-Ofast) #在 "Release" 模式下，通过 add_compile_options 命令添加编译选项 -Ofast，这是 GCC 编译器的一个选项，用于启用最大级别的优化，以提高代码运行速度。它通常会应用各种优化策略，包括内联函数、循环展开等。
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo") # 如果构建类型不是 "Release"，则检查是否为 "RelWithDebInfo"（带有调试信息的发布）模式。
    message(STATUS "Maximum optimization for speed, debug info included") #如果构建类型是 "RelWithDebInfo"，则输出一条状态消息，指示当前选择了最大化的速度优化，并且还包含了调试信息。
    add_compile_options(-Ofast -g) # 在 "RelWithDebInfo" 模式下，除了启用最大化的速度优化外，还添加了 -g 选项，该选项会生成用于调试的符号信息，以便在调试时可以查看变量和代码执行的详细信息。
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel") # 如果构建类型不是 "RelWithDebInfo"，则检查是否为 "MinSizeRel"（最小化大小发布）模式。
    message(STATUS "Maximum optimization for size") # 如果构建类型是 "MinSizeRel"，则输出一条状态消息，指示当前选择了最大化的大小优化。
    add_compile_options(-Os) # 在 "MinSizeRel" 模式下，添加编译选项 -Os，这是 GCC 编译器的一个选项，用于启用最大化的大小优化，以减小生成的可执行文件的大小。
else () # 如果前面的条件都不满足，即构建类型不是 "Release"、"RelWithDebInfo" 或 "MinSizeRel"，则执行下面的代码块。
    message(STATUS "Minimal optimization, debug info included") # 在其他构建类型下，输出一条状态消息，指示当前选择了最小化的优化，并且还包含了调试信息。
    add_compile_options(-Og -g) # 在其他构建类型下，添加编译选项 -Og -g，这是 GCC 编译器的选项，用于启用适用于调试的优化级别，并生成调试符号信息。
endif ()
# 添加宏定义，指定使用 HAL 驱动和 STM32F103xB 芯片
add_definitions(-DUSE_HAL_DRIVER -DSTM32F103xB -DUSE_STDPERIPH_DRIVER -DSTM32F10X_HD)

# 链接源文件 # 配置头文件搜索路径
include_directories( user/inc STM32F10x_FWLib/inc hardware/inc)
# 文件 # 设置源文件列表
file(GLOB_RECURSE SOURCES "startup/*.*" "user/*.*" STM32F10x_FWLib/*.* "hardware/*.*")

# 设置链接脚本路径
set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F103C8Tx_FLASH.ld )
# 添加链接选项，包括链接脚本和其他选项
add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})

add_link_options(-specs=nano.specs -specs=nosys.specs -u _printf_float )
# 创建可执行文件目标，包括源文件和链接脚本
add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})
# 数学函数需要用到
target_link_libraries(${PROJECT_NAME}.elf  m)
#target_compile_options(${PROJECT_NAME}.elf  PRIVATE --specs=nosys.specs)
# 设置输出的 HEX 和 BIN 文件路径
set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)


# 添加自定义的构建命令，用于生成 HEX 和 BIN 文件
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
