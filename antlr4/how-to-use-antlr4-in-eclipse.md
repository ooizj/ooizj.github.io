# 在eclipse里面使用antlr4

### 1，安装antlr插件

在eclipse marketplace找到antlr插件安装

![](../.gitbook/assets/image%20%283%29.png)

### 2，添加antlr-rumtime依赖和新增maven插件

添加antlr4-runtime依赖

```markup
<dependency>
            <groupId>org.antlr</groupId>
            <artifactId>antlr4-runtime</artifactId>
            <version>4.8-1</version>
        </dependency>
```

将antlr插件生成的源代码加入classpath

{% hint style="info" %}
或者单独把antlr生成的代码放到其它工程（直接用eclipse新增antlr project），然后引用它
{% endhint %}

```markup
<plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>build-helper-maven-plugin</artifactId>
                <version>3.2.0</version>
                <executions>
                    <execution>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>add-source</goal>
                        </goals>
                        <configuration>
                            <sources>
				<source>${project.build.directory}/generated-sources/</source>
                            </sources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
            <groupId>org.eclipse.m2e</groupId>
            <artifactId>lifecycle-mapping</artifactId>
            <version>1.0.0</version>
            <configuration>
                <lifecycleMappingMetadata>
                      <pluginExecutions>
                        <pluginExecution>
                          <pluginExecutionFilter>
                            <groupId>org.codehaus.mojo</groupId>
                            <artifactId>build-helper-maven-plugin</artifactId>
                            <versionRange>[1.0,)</versionRange>
                            <goals>
                              <goal>parse-version</goal>
                              <goal>add-source</goal>
                              <goal>maven-version</goal>
                              <goal>add-resource</goal>
                              <goal>add-test-resource</goal>
                              <goal>add-test-source</goal>
                            </goals>
                          </pluginExecutionFilter>
                          <action>
                            <execute>
                              <runOnConfiguration>true</runOnConfiguration>
                              <runOnIncremental>true</runOnIncremental>
                            </execute>
                          </action>
                        </pluginExecution>
                    </pluginExecutions>
                </lifecycleMappingMetadata>
            </configuration>
        </plugin>
```

### 3，配置antlr插件

打开Preferences-&gt;ANTLR4-&gt;Tool

找到需要的antlr版本的jar包下载下来，并添加到ANTLR Tool

修改Options下的Directory“./target/generated-sources/”

{% hint style="success" %}
下载结尾为“complete”的jar，这个jar包含了antlr-runtime和antlr-tool

下载的jar包最好放到“C:\Users\xx\(用户名\)\AppData\Local\Temp”
{% endhint %}

![](../.gitbook/assets/image%20%286%29.png)

{% hint style="info" %}
如果工程中的ANTLR4-&gt;Tool没有改变，可以尝试点击右上角“Configure Project Specific Settings...”来配置试试
{% endhint %}

### 4，使用antlr生成代码

对目标“.g4”文件点击右键-&gt;Run As-&gt;External Tools Configurations...

![](../.gitbook/assets/image%20%284%29.png)

设置antlr tool运行参数为“-package me.ooi.t -no-listener -visitor -encoding UTF-8”（根据需要调整）

![](../.gitbook/assets/image%20%281%29.png)

生成源代码

![](../.gitbook/assets/image%20%285%29.png)

生成代码如下

![](../.gitbook/assets/image%20%282%29.png)

{% hint style="info" %}
如果Lexer和Parser是分开的，则要对Lexer进行操作，然后对Parser进行操作（如果是IDEA则没有这么麻烦）
{% endhint %}

### 5，查看语法树

使用eclipse查看语法树，Window-&gt;Show View，

![](../.gitbook/assets/image%20%288%29.png)

![](../.gitbook/assets/image%20%289%29.png)

![](../.gitbook/assets/image%20%287%29.png)

{% hint style="info" %}
eclipse的Parse Tree不怎么好用，建议使用IDEA的
{% endhint %}

