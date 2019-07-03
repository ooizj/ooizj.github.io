# ant demo

ant是一个可用于很方便灵活构建java application的工具

ant里面有很多“task”，可以用于执行各种不同的操作  
如：“mkdir”：创建目录；“javac”：编译成class ...

“target”是“task”容器可以包含多个“task”  
ant中至少要有一个“target”，在执行ant命令后若不指定运行哪个具体的“target”，会执行默认的“target”

## 编写ant构建文件build.xml

假如有个工程“ant\_test”，在工程根目录下新建build.xml，如下示例

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!-- ====================================================================== 
     2018年5月4日 下午3:02:25                                                        

     project    
     description

     jun.zhao                                                                
     ====================================================================== -->
<project name="test-ant-project" default="jar_ant_test" basedir=".">
    <description>
        将“ant_test”项目打成jar包
    </description>

    <!-- ====================================================================== -->
    <!-- Build environment properties                                           -->
    <!-- ====================================================================== -->
    <property name="build.sourceEncoding" value="UTF-8"/>
    <property name="build.javaSourceVersion" value="1.6"/>
    <property name="build.targetClassVersion" value="1.6"/>
    <property name="build.dir" value="build_dir"/>
    <property name="build.finalName" value="ant_test"/>
    <property name="build.srcDir.0" value="src/main/java"/>
    <property name="build.resourceDir.0" value="src/main/resources"/>
    <property name="build.class.outputDir" value="${build.dir}/classes"/>
    <property name="build.jarDir" value="${basedir}/../lib"/> <!-- 项目同级目录的“lib”目录存放了所有依赖的jar -->

    <!-- ====================================================================== -->
    <!-- Defining classpaths                                                    -->
    <!-- ====================================================================== -->
    <path id="build.classpath" >
        <fileset dir="${build.jarDir}">
            <include name="**/*.jar"/>
        </fileset>
    </path>

    <!-- ====================================================================== -->
    <!-- Cleaning up target                                                     -->
    <!-- ====================================================================== -->
    <target name="clean" description="清理构建目录">
        <delete dir="${build.dir}"></delete>
        <mkdir dir="${build.dir}"/>
    </target>

    <!-- ====================================================================== -->
    <!-- Compilation target                                                     -->
    <!-- ====================================================================== -->
    <target name="compile" depends="clean" description="编译">
        <mkdir dir="${build.class.outputDir}"/>
        <javac destdir="${build.class.outputDir}"
            source="${build.javaSourceVersion}" 
            target="${build.targetClassVersion}"
            debug="true" debuglevel="source,lines,vars"
            fork="false">
            <src>
                <pathelement location="${build.srcDir.0}"/>
            </src>
            <classpath refid="build.classpath"></classpath>
        </javac>

        <!-- 将resource下面的文件复制到编译class的目录里面 -->
        <copy todir="${build.class.outputDir}">
            <fileset dir="${build.resourceDir.0}"></fileset>
        </copy>
    </target>

    <!-- ====================================================================== -->
    <!-- Package target                                                         -->
    <!-- ====================================================================== -->
    <target name="jar_ant_test" depends="compile" description="打包">
        <jar destfile="${build.dir}/${build.finalName}.jar">
            <fileset dir="${build.class.outputDir}"></fileset>
        </jar>
    </target>

</project>
```

## 下载ant，并配置到环境变量

ant运行依赖java，所以先要保证java环境有没有问题  
ant下载地址[https://ant.apache.org/bindownload.cgi](https://ant.apache.org/bindownload.cgi)  
将ant\_home配置到环境变量里面，如：

```text
ANT_HOME=D:\soft\apache-ant-1.9.9-bin\apache-ant-1.9.9
```

## 使用ant构建项目

在命令行中，进入工程根目录，运行ant命令即可编译工程

```text
ant
```

构建指定build文件可以使用“-buildfile”参数

## 常用的“task”

### mkdir 创建目录

```markup
<mkdir dir="目录"/>
```

### delete 删除目录or文件

[更多示例](https://ant.apache.org/manual/Tasks/delete.html)

```markup
<delete dir="目录"/> <!-- 删除目录（包含子目录） -->
<delete file="文件"/>
<!-- 删除目录（包含子目录） -->
<delete includeemptydirs="true">
  <fileset dir="目录" includes="**/*"/>
</delete>
```

### javac 编译成class

```markup
<!-- 先声明一个classpath -->
<path id="build.classpath" >
    <fileset dir="jar目录">
        <include name="**/*.jar"/>
    </fileset>
</path>

<javac destdir="目标目录"
    source="源代码版本" 
    target="class版本"
    debug="true" debuglevel="source,lines,vars"
    fork="false">
    <src>        
        <pathelement location="源文件目录"/>
    </src>
    <!-- 引用那个classpath -->
    <classpath refid="build.classpath"></classpath>
</javac>
```

```text
//源文件文件结构
me/ooi/test_ant
    A.java
    B.java
//编译后的文件结构
me/ooi/test_ant
    A.class
    B.class
```

### jar 打成jar

java要先变成class，然后才能变成jar

```markup
<jar destfile="jar包路径">
    <fileset dir="classes目录"></fileset>
</jar>
```

```text
//jar文件结构
ant_test.jar
    me/ooi/test_ant
        A.class
        B.class
    META-INF
        MANIFEST.MF
```

### war 打成war

```markup
<war destfile="war包路径" 
     compress="true" 
    webxml="web.xml文件路径">
    <lib dir="所需jar（最终后被复制到WEB-INF/lib）"/>
    <classes dir="classes目录"/>
    <fileset dir="需要打成war包的目录" 
           excludes="WEB-INF/web.xml"/>
</war>
```

```text
ant_test.war
    META-INF
        META-INF.MF
    WEB-INF
        classes
            me/ooi/test_ant
                A.class
                B.class
        lib
            a.jar
            b.jar
        web.xml
        a.html
        b.html
    index.html
    hhh.jpeg
```

### ear 打成ear

```markup
<ear destfile="ear包路径" appxml="application.xml文件路径">
    <fileset dir="需要打成ear包的目录"></fileset>
</ear>
```

```text
ant_test.ear
    META-INF
        META-INF.MF
        application.xml
    ant_test.war
```

