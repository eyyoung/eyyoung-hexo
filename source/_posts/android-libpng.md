title: 解决libpng warning
---
新版本Build Tool在编译旧项目时出现

    libpng warning: iCCP: Not recognizing known sRGB profile that has been edited

警告。

解决方法：

安装ImageMagick，并将安装路劲配置到系统环境变量
http://www.imagemagick.org/script/binary-releases.php#windows
新建后缀名为.ps1的PowerShell脚本

    Get-ChildItem . *.png -Recurse| ForEach-Object -Process{
        if($_ -is [System.IO.FileInfo]){
           convert $_.FullName -strip $_.FullName;
           Write-Host($_.FullName);
        }
    }

保存到工程目录，右键点击脚本文件使用PowerShell运行
等待完成。
如果脚本无法运行，在PowerShell中使用set-executionpolicy remotesigned设置成允许未签名脚本执行
PowerShell签名策略：

    help about_signing
