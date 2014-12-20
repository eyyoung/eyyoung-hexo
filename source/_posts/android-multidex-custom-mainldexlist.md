title: MultiDex 手动指定 maindexlist
---
https://developer.android.com/tools/building/multidex.html

官方在build tool 21.1.0后正式推出分包打包解决方法数量超标65535问题。

dex分包原理：

http://android-developers.blogspot.com/2011/07/custom-class-loading-in-dalvik.html

原理既在编译期将dex分散在多个中，运行期，解压出其他dex并让classloader尝试加载

主要步骤：

增加依赖

    compile 'com.android.support:multidex:1.0.0'

配置开关

    defaultConfig {
        multiDexEnabled true
    }

相比在该版本推出前，打包过程多了几个步骤，

<!-- more -->

收集所有类（package）
压缩所有类（shrink）
创建mainDexList（建立主dex列表，减小在Application初始化前由于找不到类导致崩溃的可能性）
项目中遇到的问题是在主工程的Application调用了其他aar中的初始化操作，

build tool未检测出来该初始化操作所需要的类，导致初始化无法找到该类。

解决方案：

在主公程build/intermediates/multi-dex中找出maindexlist，

拷贝出来。

自行增加没有被包含进去的类。

增加自定义maindexlist代码。让dex工具强行使用我们生成的maindexlist

    afterEvaluate {
        tasks.matching {
            it.name.startsWith('dex')
        }.each { dx -&gt;
            if (dx.additionalParameters == null) {
                dx.additionalParameters = []
            }

            // optional
            dx.additionalParameters += "--main-dex-list=$projectDir/maindexlist.txt".toString() // enable the main-dex-list
            dx.additionalParameters += "--minimal-main-dex" // enable the main-dex-list
        }
    }
