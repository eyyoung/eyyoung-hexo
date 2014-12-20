title: Gradle 多版本打包手记
---
  1. 利用默认的目录结构无需配置不同版本的工程目录结构
只需在src\main的同级目录下新建渠道名称

  2. 依赖的模块在构建时如果存在多版本，应该指明使用什么版本或者模块里头配置defaultPublishConfig属性

方案一：

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:21.0.0'
        flavorOneCompile project(path: ':testmoduleone', configuration: 'flavorOneDebug')
    //    flavorOneCompile project(path: ':testmoduleone', configuration: 'flavorOneRelease')
        flavorTwoCompile project(path: ':testmoduleone', configuration: 'flavorTwoDebug')
    }

方案二：

    android {
        defaultPublishConfig "flavorOneDebug"
    }

运行阶段在Build Variant窗口进行选择运行何种版本
<!-- more -->
灵活使用buildConfigField
如外网内网包可配置不同版本或者定义不同常量

    android {
        ...
        buildTypes {
            def BOOLEAN = "boolean"
            def TRUE = "true"
            def FALSE = "false"
            def LOG_HTTP_REQUESTS = "LOG_HTTP_REQUESTS"
            def REPORT_CRASHES = "REPORT_CRASHES"
            def ENABLE_VIEW_SERVER = "ENABLE_VIEW_SERVER"
            def ENABLE_SHARING = "ENABLE_SHARING"
            def DEBUG_IMAGES = "DEBUG_IMAGES"

            debug {
                ...
                buildConfigField BOOLEAN, LOG_HTTP_REQUESTS, TRUE
                buildConfigField BOOLEAN, REPORT_CRASHES, FALSE
                buildConfigField BOOLEAN, ENABLE_VIEW_SERVER, TRUE
                buildConfigField BOOLEAN, ENABLE_SHARING, TRUE
                buildConfigField BOOLEAN, DEBUG_IMAGES, TRUE
            }

            client {
                ...
                buildConfigField BOOLEAN, LOG_HTTP_REQUESTS, TRUE
                buildConfigField BOOLEAN, REPORT_CRASHES, TRUE
                buildConfigField BOOLEAN, ENABLE_VIEW_SERVER, FALSE
                buildConfigField BOOLEAN, ENABLE_SHARING, FALSE
                buildConfigField BOOLEAN, DEBUG_IMAGES, FALSE
            }

            release {
                ...
                buildConfigField BOOLEAN, LOG_HTTP_REQUESTS, FALSE
                buildConfigField BOOLEAN, REPORT_CRASHES, TRUE
                buildConfigField BOOLEAN, ENABLE_VIEW_SERVER, FALSE
                buildConfigField BOOLEAN, ENABLE_SHARING, FALSE
                buildConfigField BOOLEAN, DEBUG_IMAGES, FALSE
            }
        }
    }

Github:
https://github.com/eyyoung/GradleMultiFlavor
