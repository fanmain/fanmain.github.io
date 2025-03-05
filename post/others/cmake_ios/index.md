# Cmake_ios

 

参考文档：

[tvm\_phone/tvm-cmake-ios.md at master · zhaowd2001/tvm\_phone · GitHub](https://github.com/zhaowd2001/tvm_phone/blob/master/tvm-cmake-ios.md "tvm_phone/tvm-cmake-ios.md at master · zhaowd2001/tvm_phone · GitHub")

[https://blog.csdn.net/qq\_38743313/article/details/101601778/](https://blog.csdn.net/qq_38743313/article/details/101601778/ "https://blog.csdn.net/qq_38743313/article/details/101601778/")

参考了前面几个文档，发现都不是最好的解决办法，准确的说就没起作用，哈哈哈。

我的测试目录结构如下：

根目录/

        assets/test.txt   

        interface/test/somefile.h

        src/somefile.cpp

        CMakeLists.txt

        ios.toolchain.cmake

        build.sh

assets是我想直接复制到framework中去的资源

CMakeLists.txt内容如下

```cobol
cmake_minimum_required(VERSION 3.10.2) project(CMakeTestLib) enable_language(CXX) set(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib/${OUTPUT_PATH}) file(GLOB_RECURSE SOURCE_FILES ${CMAKE_SOURCE_DIR}/src/*.cpp)file(GLOB_RECURSE INCLUDE_FILES ${CMAKE_SOURCE_DIR}/interface/*.h)file(GLOB_RECURSE ASSETS_FILES ${CMAKE_SOURCE_DIR}/assets/*.txt) MESSAGE( STATUS "SOURCE_FILES: " ${SOURCE_FILES} )MESSAGE( STATUS "INCLUDE_FILES: " ${INCLUDE_FILES} )MESSAGE( STATUS "ASSETS_FILES: " ${ASSETS_FILES} ) set(RESOURCE_FILES			${CMAKE_SOURCE_DIR}/assets/note.txt	) include_directories(${PROJECT_NAME} 		${CMAKE_SOURCE_DIR}/interface)			add_library(${PROJECT_NAME}		SHARED 		${SOURCE_FILES}		${CMAKE_SOURCE_DIR}/interface		${CMAKE_SOURCE_DIR}/assets) # Debug symbols set in XCode project# set_xcode_property(${PROJECT_NAME} GCC_GENERATE_DEBUGGING_SYMBOLS YES "All") set_target_properties(${PROJECT_NAME} PROPERTIES	FRAMEWORK TRUE	FRAMEWORK_VERSION A	MACOSX_FRAMEWORK_IDENTIFIER com.test.${PROJECT_NAME}	# MACOSX_FRAMEWORK_INFO_PLIST Info.plist	# "current version" in semantic format in Mach-O binary file	VERSION 1.0.1	# "compatibility version" in semantic format in Mach-O binary file	SOVERSION 1.0.1	PUBLIC_HEADER ${CMAKE_SOURCE_DIR}/interface	RESOURCE ${CMAKE_SOURCE_DIR}/assets	#XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer") target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR}/interface)
```

注意事项：

1、无论是headers还是assets，都要先在[add\_library](https://so.csdn.net/so/search?q=add_library&spm=1001.2101.3001.7020)中添加了才有用。

2、PUBLIC\_HEADER和RESOURCE 后面都可以直接写目录，而非文件列表。

![](https://img-blog.csdnimg.cn/6b9b4d2b8df84f05bebde1cfae83a622.png)
