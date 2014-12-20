title: Android Studio Tips Log
categories: Android
tags:
- Android
- Android Studio
---
 - bin目录下的vmoption文件增大虚拟机常驻内存与最大大可使用内存后，卡顿现象消失
   ```bash
   -Xms512m
   -Xmx2048m
   -XX:MaxPermSize=500m
   ```
 - ButterKnife插件，简化见面注入操作
 - 由于基于Intelli J平台，所有的自负搜索都是模糊搜索。
将Keymap切换到Eclipse模式下后，快捷键可使用Eclipse习惯
如：
Ctrl+Shift+R快速打开文件（资源，类，XML，几乎所有）
Double Shift更加全面的搜索，除了上面，包括功能，如Show Color Picker
Ctrl+E最近的文件
 - 妥当利用Living Templates加快代码开发，如便捷界面输入fori后按TAB将快速生成循环迭代代码
 - 只能创建未定义变量，如先输入mFile = new File(“路径”)；如果前面未定义过mFile，再mFile处按下Alt+Enter快捷键，便可提示生成mFile全局变量
 - 快速抽取字符串资源，输入”字符串”后，使用Alt+Enter可快速生成字符串资源到string.xml并在旧位置替换成getString方法
 - 在layout文件中右键某个控键Refractor-Extract-Style可快速抽取style.Layout也可抽取并自动include
 - Eclipse习惯下的常用快捷键：
Ctrl+G 快速查找引用
Ctrl+T 进入(类、方法、接口)实现
F3 跳转（方法、变量、资源）定义
ALT+SHIFT+R 重构（方法、变量名、类名）
ALT+← 跳转到上一处
ALT+→ 跳转到下一处
ALT+D 删除正航
SHIFT+ENTER 快速新建新行
 - 移动配置文件夹磁盘位置
http://stackoverflow.com/questions/17390213/android-studio-path-error
