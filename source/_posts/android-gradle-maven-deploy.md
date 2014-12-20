title: Android Studio 利用 Gradle 部署AAR
tags:
- Android
---
配置build.gradle

    apply plugin: 'maven'
    uploadArchives {
        repositories {
            mavenDeployer {
                repository(url: "http://192.168.1.30:8081/nexus/content/repositories/snapshots"){
                    authentication(userName: mavenUser, password: mavenPass)
                }
                pom.version = "1.0.0-SNAPSHOT"
                pom.artifactId = "utillibrary"
                pom.groupId = "com.nd.android.u.lib"
            }
        }
    }

gradle同步后Gradle视图中新增uploadArchives任务

打开Gradle视图双击该任务

代码中mavenUser可在Gradle主目录中新增gradle.properties配置
mavenUser = xxxx
mavenPass = xxx
