---
title: 使用CheckStyle检查编码规范
layout: post
category: tool
splitor: <!--more-->
---

编码规范指的是编写代码的样式规则。不同的程序员，往往有不一样的风格。单单是抛出使用空格键还是TAB键的问题，就足以让程序员演化成针锋相对的两个派别。

为什么要在项目中统一编码规范？好的编程规范有助于写出易于阅读、质量更高、错误更少、更易于维护的程序。

一般代码编写规范包含代码结构，格式，命名、javadoc还有编码的最佳实践等内存。

<!--more-->

在自己的实际项目中发现，我们刚开始在团队内通过口头约定采用[google java code style](https://google.github.io/styleguide/javaguide.html)编码规范。在这个规范里，缩进是使用2个空格的，追着团队新成员的加入，慢慢的在同一个文件中同时出现了2个空格和4个空格缩进，格式整个乱套了。此外对变量的命名也出现了a,b,c作为变量命名的情况。

为了保证编码规范的执行，首先 我们需要在IDE中配置编码规范配置，



## IDE中配置code style 

### 在intellij中安装code style 配置

下载 [intellij-java-google-style.xml](/public/img/attach/checkstyle/intellij-java-google-style.xml)，拷贝到 `~/Library/Preferences/IntelliJIdea15/codestyles`下.
打开IntelliJ，File->Setting->Editor->Code Style ,选择 Google_Style作为项目的代码样式模板。

![在intellij中配置code style](/public/img/attach/checkstyle/codestyle_intellij_config.jpg)

### 在Eclipse中安装code style 配置

下载 [eclipse-java-google-style.xml](/public/img/attach/checkstyle/eclipse-java-google-style.xml)，在eclipse中打开Window/Preferences界面，选择Java/Code Style/Formatter，进行导入。

## 使用CheckStyle工具检查编码规范

CheckStyle能够帮助程序员检查代码是否符合制定的规范。通过将CheckStyle的检查引入到项目构建中，可以强制让项目中的所有的开发者遵循制定规范，而不是仅仅停留在口头约定上。如果发现代码违反了标准，阻止构建成功

CheckStyle有针对不同IDE和maven的各种插件，方便开发者随时随地对代码进行静态检查。

### 在intellij 中安装CheckStyle-IDE插件

`File->Setting->Plugins`中安装CheckStyle-IDE插件

![安装CheckStyle-IDE插件](/public/img/attach/checkstyle/checkstyle_intellij_plugin.jpg)

File->Setting->Other Settings->Check Style中配置 配置自定义Configuaration，使用[google_check.xml](/public/img/attach/checkstyle/google_checks.xml).

 `File->Setting->Editor->Inspections` 配置CheckStyle中选择是否进行实时检查。

![CheckStyle实时检查](/public/img/attach/checkstyle/checkstyle_intellij_inspection.jpg)


### 在Eclipse 中安装 CheckStyle 插件

从[插件下载](http://eclipse-cs.sourceforge.net/#!/)

 `Preferences --> Checkstyle` 配置check configuration

![eclipse checkstyle plugin configuration](/public/img/attach/checkstyle/eclipse_checkstyle_plugin.jpg)

右击操作对当前文件进行代码检查
![右击操作对当前文件进行代码检查](/public/img/attach/checkstyle/eclipse_checkstyle_use.png)

### 使用maven执行检查

#### maven 配置

在项目构建的时候加入对代码格式的检查，发现有违反的，直接让构建失败。

```XML
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-checkstyle-plugin</artifactId>
                <version>2.17</version>
                <configuration>
                    <configLocation>google_checks.xml</configLocation>
                    <encoding>UTF-8</encoding>
                    <failOnViolation>true</failOnViolation>
                    <linkXRef>false</linkXRef>
                    <includeTestSourceDirectory>true</includeTestSourceDirectory>
                    <maxAllowedViolations>0</maxAllowedViolations>
                    <violationSeverity>warning</violationSeverity>
                </configuration>
                <executions>
                    <execution>
                        <id>checkstyle</id>
                        <phase>validate</phase>
                        <goals>
                            <goal>check</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>com.puppycrawl.tools</groupId>
                        <artifactId>checkstyle</artifactId>
                        <version>6.19</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
    <reporting>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-checkstyle-plugin</artifactId>
                <version>2.17</version>
                <reportSets>
                    <reportSet>
                        <reports>
                            <report>checkstyle</report>
                        </reports>
                    </reportSet>
                </reportSets>
            </plugin>
        </plugins>
    </reporting>
```

### goal

Goal |    Report or Not | Description
---- | -------------- | -----------
checkstyle:check  |  No |  检查代码，并可能导致构建失败
checkstyle:checkstyle |  Yes | 检查代码并生成代码检查结果报表
checkstyle:checkstyle-aggregate | Yes | 对多模块的项目代码检查，并生产检查汇总的结果报表


## 童子军原则

美国的童子军，有一条规则 `Leave the campground cleaner than you found it.` 翻译为 要让离开时的营地比进入时更加干净。 这也是我们对待代码的态度。

## 下一步 findbugs
