# SBT and Maven
## Maven
### 相關資料

* [What is Maven](http://maven.apache.org/what-is-maven.html)
* [Sonatype Books](http://www.sonatype.com/resources/books)
* [【認識 Gradle】（1）講古的時間 Apache Ant](http://www.codedata.com.tw/java/understanding-gradle-1-ant/)
* [【認識 Gradle】（2）講古的時間 Apache Maven](http://www.codedata.com.tw/java/understanding-gradle-2-maven/)


### Maven 核心概念 Build Life Cycle(Phase) and Plugins(Goal)
* Maven 定義專案 build 的步驟 (Phase)
* Maven 使用 plugin 來完成相關的工作 (Goal)
* 使用 pom.xml 來設定相依性，以及每個 phase 需要使用那些 plugin 的功能
* [Maven Core Concepts](http://books.sonatype.com/mvnex-book/reference/simple-project-sect-simple-core.html)


![Core Concepts](http://books.sonatype.com/mvnex-book/reference/figs/web/simple-project_lifecyclebinding.png)


* Maven 的指令，有帶 `:` 的，就是使用 plugin 內建的功能，Ex: `mvn eclipse:eclipse`。沒有的話，就是要執行 Build Phase，ex: `mvn package`


### 如何產出最簡單的專案並結合 Scala
* 使用 maven 指令產出專案基本目錄與檔案
	1. command: 
	
		```
		mvn archetype:generate
		```
	2. choose archetype: 397
	3. choose archetype version number: 6
	4. project groupId: com.bridgewell.demo (maven repository groupId)
	5. project artifactId: single_demo (proejct folder) (maven repository artifactId)
	6. project version: 1.0-SNAPSHOT (proejct version) (maven respository version)
	7. project package: com.bridgewell.demo.maven.single


* 目錄與檔案結構

```
.
|____single_demo
  |____pom.xml
  |____src
    |____main
    | |____java
    |   |____com
    |     |____bridgewell
    |       |____demo
    |         |____maven
    |           |____single
    |             |____App.java
    |____test
      |____java
        |____com
          |____bridgewell
            |____demo
              |____maven
                |____single
                  |____AppTest.java

```

* Maven POM File
pom.xml 中的 `<packaging>jar</packaging>` 是指專案要打包成那種型態，一般是 `jar`。如果是要寫網站，則是 `war`。如果是母/子專案，則是 `pom`


```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.bridgewell.demo</groupId>
  <artifactId>single_demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>single_demo</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```
	

* 新加 Scala Source code 目錄

```
mkdir -p src/main/scala
mkdir -p src/test/scala

```

```
.
|____pom.xml
|____src
  |____main
  | |____java
  | | |____com
  | |   |____bridgewell
  | |     |____demo
  | |       |____maven
  | |         |____single
  | |           |____App.java
  | |____scala
  |____test
    |____java
    | |____com
    |   |____bridgewell
    |     |____demo
    |       |____maven
    |         |____single
    |           |____AppTest.java
    |____scala

```
### 常用的 plugin 整理
* maven-compiler-plugin
* scala-maven-plugin
* maven-dependency-plugin
* maven-assembly-plugin
* maven-scala-plugin

已加到 pom.xml，到時候用 copy&paste 就好

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.bridgewell.demo</groupId>
  <artifactId>single_demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>single_demo</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <jdk.version>1.7</jdk.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    
    <dependency>
      <groupId>org.scala-lang</groupId>
      <artifactId>scala-library</artifactId>
      <version>2.10.4</version>
    </dependency>

  </dependencies>

  <build>
    <plugins>
      <!-- Java Compiler -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>${jdk.version}</source>
          <target>${jdk.version}</target>
          <encoding>${project.build.sourceEncoding}</encoding>
        </configuration>
      </plugin>

      <!-- Scala Compiler -->
      <plugin>
        <groupId>net.alchim31.maven</groupId>
        <artifactId>scala-maven-plugin</artifactId>
        <version>3.1.6</version>
        <executions>
          <execution>
            <id>scala-compile-first</id>
            <phase>process-resources</phase>
            <goals>
              <goal>add-source</goal>
              <goal>compile</goal>
            </goals>
          </execution>
          <execution>
            <id>scala-test-compile</id>
            <phase>process-test-resources</phase>
            <goals>
              <goal>testCompile</goal>
            </goals>
          </execution>
        </executions>
        <!-- 可再指定要用那個 scala 版本，不設定，會預設找 org.scala-lang 版本
        <configuration>
          <scalaVersion>2.10.4</scalaVersion>
        </configuration> -->
      </plugin>

      <!-- copy dependency / 將有用到的 dependency library copy 到指定的目錄，方便 deploy -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <version>2.8</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
              <outputDirectory>${project.build.directory}/lib</outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>

      <!-- assembly / 將有用到的 dependency library 與專案的程式，全部打包成一個 jar 檔-->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>2.4</version>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

  <reporting>
    <plugins>
      <!-- scala doc -->
      <plugin>
        <groupId>org.scala-tools</groupId>
        <artifactId>maven-scala-plugin</artifactId>
      </plugin>
    </plugins>
  </reporting>

</project>

```

### 常用指令
* `mvn compile` 編譯程式
* `mvn clean` 清除編譯好的檔案
* `mvn test` Unit Test
* `mvn package` 打包專案
* `mvn install` 將專案的 jar 安裝到 local respository
* `mvn scala:doc` Scala Doc
* `mvn scala:console` 進入 Scala Interactive Interpreter (REPL)。類似 `sbt console`
* `mvn eclipse:eclipse` 產生出 Eclipse 的專案檔。可以用 Eclipse import 專案。(註)

__註：Eclipse 有 m2e 的 plugin，可以直接 import maven 專案，但 m2e 在專案開發時，不是很好用。所以我都轉成 eclipse 專案檔，再 import__


## SBT
### 相關資料
* [SBT Doc](http://www.scala-sbt.org/release/docs/index.html)
* [Configure Full Example](http://www.scala-sbt.org/release/docs/Examples/Full-Configuration-Example.html)
* [SBT plugins](http://www.scala-sbt.org/release/docs/Community/Community-Plugins.html)
* [SBT Project API Doc](http://www.scala-sbt.org/release/api/index.html#sbt.Project)
* [Scala with Java](http://www.scala-sbt.org/release/docs/Detailed-Topics/Java-Sources.html)
* [SBT Key Definition](http://www.scala-sbt.org/release/sxr/sbt/Keys.scala.html) (**重要!!!!**)

### SBT 的核心
#就是搞定 [SBT Key](http://www.scala-sbt.org/release/sxr/sbt/Keys.scala.html) 設定#

所有寫的程式 (Build.scala) 或是設定 (build.sbt) 都是在設定 sbt key。

* SBT 使用 Task 的方式，來做 Build Life Cycle. ex: clean, compile, package
* SBT 也有 plugin。只要將 plugin 的設定，加到專案的 sbt key 中，就會生效，不加，就算有 plugin，也不會啟用。
* SBT 可以自定 Task 及要相依在那個 Task 上。雖然 SBT 的 plugin 不多，但可以透過這樣子的方式來彌補。


### SBT 與 Maven 的差異
* SBT 及 Maven 都有 Build Life Cycle 的概念。
	* SBT 是用 Task 的方式，類似 Ant
	* Maven 則定義成 Phase
* SBT 及 Maven 都有 plugin 可用
	* SBT 的 plugin 比較像是輔助的角色，就算不用 plugin，也可自定 Task 來完成相關的工作
	* Maven 則全都用 plugin 來執行工作，雖然在 pom.xml 寫的 plugin 不多，但 maven 本身已內建很多 plugin. 可用 `mvn help:effective-pom` 查. 

### 目錄與檔案結構
* build.sbt 與 Build.scala 至少要有一個。
* plugins.sbt 有使用 plugin 時再加。
* build.properties 可有可無。

```
.
|____single_demo
  |____build.sbt
  |____project
  | |____build.properties
  | |____Build.scala
  | |____plugins.sbt
  |____src
    |____main
    | |____java
    | | |____com
    | |   |____bridgewell
    | |     |____demo
    | |       |____sbt
    | |         |____single
    | |           |____App.java
    | |____resources
    | |____scala
    |   |____com
    |     |____bridgewell
    |       |____demo
    |         |____sbt
    |           |____single
    |             |____MyApp.scala
    |____test
      |____java
      |____resources
      |____scala

```

### 常用的設定
* build.sbt

	```	
	import sbt._
	import Process._
	import Keys._
	import AssemblyKeys._
	
	name := "single-demo"
	
	version := "1.0"
	
	scalaVersion := "2.10.4"
	
	retrieveManaged := true  // 將 dependency libraries 匯出
	
	libraryDependencies += "mysql" % "mysql-connector-java" % "5.1.30" % "compile" // demo test
	
	javacOptions ++= Seq("-source", "1.8")
	
	compileOrder in Compile := CompileOrder.JavaThenScala
	
	compileOrder in Test := CompileOrder.Mixed
	
	assemblySettings  // 使用 assembly plugin，將 dependency library 及程式，打包成一個 .jar
	
	```

* plugins.sbt


	```
	addSbtPlugin("com.earldouglas" % "xsbt-web-plugin" % "0.9.0") // 寫 web 會用到
	
	addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "2.5.0") // 轉 eclipse
	
	addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.11.2")  // 打包 jar
	
		
	```

### 多專案相依

* 目錄結構

	```
	multi_demo
	|____build.sbt
	|____core
	| |____build.sbt
	| |____project
	| |____src
	|   |____main
	|   | |____java
	|   | |____resources
	|   | |____scala
	|   |   |____com
	|   |     |____bridgewell
	|   |       |____demo
	|   |         |____sbt
	|   |           |____multi
	|   |             |____MyTest.scala
	|   |____test
	|     |____java
	|     |____resources
	|     |____scala
	|
	|____project
	| |____build.properties
	| |____plugins.sbt
	|
	|____web
	  |____build.sbt
	  |____project
	  |____src
	    |____main
	    | |____java
	    | |____resources
	    | |____scala
	    | | |____com
	    | |   |____bridgewell
	    | |     |____demo
	    | |       |____sbt
	    | |         |____multi
	    | |           |____Hello.scala
	    | |____webapp
	    |   |____WEB-INF
	    |     |____scalate
	    |     | |____layouts
	    |     |   |____default.ssp
	    |     |____test.ssp
	    |     |____web.xml
	    |____test
	      |____java
	      |____resources
	      |____scala
	
	```

* 母專案設定檔 (multi_demo)
	1. build.sbt

		```
		import sbt._
		import Process._
		import Keys._
		
		name := "multi-demo"
		
		version := "1.0"
		
		scalaVersion := "2.10.4"
		
		lazy val root = project.in(file(".")).aggregate(core, web)
		
		lazy val core = project.in(file("core"))
		
		lazy val web = project.in(file("web")).dependsOn(core) // web depends on core

		
		```

	2. plugins.sbt

		```
		addSbtPlugin("com.earldouglas" % "xsbt-web-plugin" % "0.9.0") // 寫 web 會用到
		
		addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "2.5.0") // 轉 eclipse
		
		addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.11.2")  // 打包 jar
		
		
		```
	
	3. build.properties

		```
		sbt.version=0.13.2
		
		```


		
* 子專案 (core) 設定檔
	1. build.sbt
	
		```
		import sbt._
		import Process._
		import Keys._
		import AssemblyKeys._
		
		name := "core"
		
		version := "1.0"
		
		libraryDependencies += "mysql" % "mysql-connector-java" % "5.1.30" % "compile" // demo test
		
		assemblySettings  // 使用 assembly plugin，將 dependency library 及程式，打包成一個 .jar
		
		retrieveManaged := true  // 將 dependency libraries 匯出
		
		compileOrder in Compile := CompileOrder.JavaThenScala
		
		compileOrder in Test := CompileOrder.Mixed
		
		```
	
* 子專案 (web) 設定檔
	1. build.sbt
	
		```
		import sbt._
		import Keys._
		import com.earldouglas.xsbtwebplugin.WebPlugin.webSettings
		
		name := "web"
		
		version := "1.0"
		
		libraryDependencies ++= Seq(
		  "javax.servlet" % "javax.servlet-api" % "3.0.1" % "provided",
		  // "org.eclipse.jetty" % "jetty-webapp" % "9.1.0.v20131115" % "container",
		  // "org.eclipse.jetty" % "jetty-plus"   % "9.1.0.v20131115" % "container",
		  "org.apache.tomcat.embed" % "tomcat-embed-core" % "7.0.22" % "container",
		  "org.apache.tomcat.embed" % "tomcat-embed-logging-juli" % "7.0.22" % "container",
		  "org.apache.tomcat.embed" % "tomcat-embed-jasper" % "7.0.22" % "container",
		  "org.fusesource.scalate" % "scalate-core_2.10" % "1.6.1" % "compile",
		  "mysql" % "mysql-connector-java" % "5.1.30" % "compile"
		)
		
		compileOrder in Compile := CompileOrder.JavaThenScala
		
		compileOrder in Test := CompileOrder.Mixed
		
		webSettings  // web plugin
		
		val mydeploy = taskKey[Unit]("Deploy Remote")
		
		mydeploy := {
		  import scala.sys.process._
		  println("start deploy")
		  // val fileName = "%s/scala-%s/%s_%s-%s.war".format(target.value, scalaBinaryVersion.value, name.value, scalaBinaryVersion.value, version.value)
		  val fileName = (Keys.`package` in Runtime).value.getPath() // 要加 Runtime 這個 scope
		  println(fileName)
		  val admin = "kigi"
		  val pass = "1234"
		  val host = "pc501"
		  val port = 8080
		  val cmdLine = "curl --upload-file %s http://%s:%s@%s:%d/manager/text/deploy?path=/mytest&update=true".format(fileName, admin, pass, host, port)
		  println(cmdLine)
		  val cmd = stringToProcess(cmdLine).!!
		  println(cmd)
		  println()
		}
		
		```
	
	
	2. 執行自定的 task `sbt mydeploy`
	
### 專案共用的設定

每一個專案都要寫類似的設定值，有沒有寫一次，其他就共用的方式，日後維護也方便？

* SBT Scope
	* [詳細說明](http://www.scala-sbt.org/0.13.2/docs/Getting-Started/Scopes.html)
	* __基本上，每個專案的設定都是獨立的，也就是說，在母專案的設定值並不會帶到子專案__

* 如果要共用的話
	* 在母專案寫 Build.scala
	
	```
	import sbt._
	import Keys._
	
	object BuildSettings {
	  val buildSettings = Seq (
	    organization := "com.bridgewell.aggregator",
	    organizationName := "BridgeWell Inc.",
	    organizationHomepage := Some(new java.net.URL("http://www.bridgewell.com.tw")),
	    version := "1.0",
	    scalaVersion := "2.10.4",
	    retrieveManaged := true,
	    compileOrder in Compile := CompileOrder.JavaThenScala,
	    compileOrder in Test := CompileOrder.Mixed
	  )
	}
	
	object Dependencies {
	  val jettyVersion = "9.1.0.v20131115"
	  val tomcatVersion = "7.0.22"
	  val scalateVersion = "1.6.1"
	  val luceneVersion = "4.8.1"
	
	  val cassandraCore = "com.datastax.cassandra" % "cassandra-driver-core" % "2.0.1" % "compile"
	  val jsoup = "org.jsoup" % "jsoup" % "1.7.3" % "compile"
	
	  val commonsCodecc = "commons-codec" % "commons-codec" % "1.9"
	  val commonsLang = "org.apache.commons" % "commons-lang3" % "3.3.1" % "compile"
	  val commonsConfig = "commons-configuration" % "commons-configuration" % "1.10" % "compile"
	  
	  val log4j = "log4j" % "log4j" % "1.2.17" % "compile"
	  val servlet = "javax.servlet" % "javax.servlet-api" % "3.0.1" % "provided"
	  
	  val jettyWeb = "org.eclipse.jetty" % "jetty-webapp" % jettyVersion % "container"
	  val jettyPlus = "org.eclipse.jetty" % "jetty-plus"   % jettyVersion % "container"
	
	  val tomcatCore = "org.apache.tomcat.embed" % "tomcat-embed-core" % tomcatVersion % "container"
	  val tomcatLog = "org.apache.tomcat.embed" % "tomcat-embed-logging-juli" % tomcatVersion % "container"
	  val tomcatJasper = "org.apache.tomcat.embed" % "tomcat-embed-jasper" % tomcatVersion % "container"
	
	  val scalate = "org.fusesource.scalate" % "scalate-core_2.10" % scalateVersion % "compile"
	  val scalatePlugin = "org.fusesource.scalate" % "sbt-scalate-plugin_2.10" % scalateVersion
	
	  val mysql = "mysql" % "mysql-connector-java" % "5.1.30" % "compile"
	
	  val luceneCore = "org.apache.lucene" % "lucene-core" % "4.8.1" % "compile"
	}
	
	```
	
	* 子專案的 build.sbt 加入母專案的設定
	
	```
	name := "core"
	
	BuildSettings.buildSettings
	
	libraryDependencies ++= Seq (
		Dependencies.commonsLang,
		Dependencies.cassandraCore,
		Dependencies.mysql
	)

	```
   * 驗証方式：查設定值
   
   ```
   sbt version
   sbt retrieveManaged
   sbt organizationName
   ```	

### Global Plugins
在 `~./sbt/version_number/plugins/build.sbt` 加自已一定會用到的 plugins. 
ex:

```
addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "2.5.0") // 轉 eclipse

addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.11.2")  // 打包 jar

```

### SBT Profile (Configuration)
在 deployment 時，因為主機的環境不同，會有不同的設定檔。在開發專案時，會將不同主機的設定檔也加入專案中，在 deploy 時，會依專案的 profile 設定，使用相對應的設定案，打包成 `jar` or `war`
以下是使用 SBT 的 Configuration 的特性，做成有 profile 的效果。

```
import sbt._
import com.earldouglas.xsbtwebplugin.WebPlugin

name := "web"

BuildSettings.buildSettings

WebPlugin.webSettings

libraryDependencies ++= Seq (
  Dependencies.commonsLang,
  Dependencies.cassandraCore,
  Dependencies.mysql,
  Dependencies.scalate,
  Dependencies.servlet,
  Dependencies.jettyWeb,
  Dependencies.jettyPlus
)

def prepareTask(profile: String): Def.Initialize[Task[Unit]] = Def.task {
  println("start copy profile: " + profile)
  IO.copyFile(sourceDirectory.value / "main" / "webapp" / "WEB-INF" / "config" / "system.properties", baseDirectory.value / "profile" / profile / "webapp" / "WEB-INF" / "config" / "system.properties.tmp", true)
  IO.copyFile(baseDirectory.value / "profile" / profile / "webapp" / "WEB-INF" / "config" / "system.properties", sourceDirectory.value / "main" / "webapp" / "WEB-INF" / "config" / "system.properties", true)
  println("end copy profile: " + profile)
}

def postTask(profile: String): Def.Initialize[Task[Unit]] = Def.task {
  IO.copyFile(baseDirectory.value / "profile" / profile / "webapp" / "WEB-INF" / "config" / "system.properties.tmp", sourceDirectory.value / "main" / "webapp" / "WEB-INF" / "config" / "system.properties", true)
  IO.delete(baseDirectory.value / "profile" / profile / "webapp" / "WEB-INF" / "config" / "system.properties.tmp")
}

def deployTask(host: String, account: String, pass: String, port: Int = 8080): Def.Initialize[Task[File]] = Def.task {
  val file = (Keys.`package` in Runtime).value
  val cmdLine = "curl --upload-file %s http://%s:%s@%s:%d/manager/text/deploy?path=/web&update=true".format(file.getPath(), account, pass, host, port)
  println(cmdLine)
  //val cmd = scala.sys.process.stringToProcess(cmdLine).!!
  //println(cmd)
  file
}

val prepare = taskKey[Unit]("prepare profile configs")

val webdeploy = taskKey[Unit]("web remote deployment")

val putremote = taskKey[File]("put war file to remote server")

val PC501 = config("pc501")

prepare in PC501 <<= prepareTask("pc501")

putremote in PC501 <<= deployTask("pc501", "kigi", "1234", 8080).dependsOn(prepare in PC501)

webdeploy in PC501 <<= postTask("pc501").dependsOn(putremote in PC501).dependsOn(update).dependsOn(clean)

```

執行指令：`sbt pc501:webdeploy`

## 總結

了解 SBT 及 Maven 核心的原理後，其他的就上網找文件或者 sample 來做。

Maven 設定 plugin 是件很煩雜工作，不會用是很正常的。所以後來才有 [Gradle](http://www.codedata.com.tw/tag/gradle/) 及其他類似的工具出現。

__建議寫好的設定檔要留著，到時候就用大絕招(copy&paste)就好__


## 注意事項
* 不要在 Task 直接呼叫另一個 Task (ex: `Task.value`)。因為 SBT 不會依照你寫的程式順序來執行，反而會在執行 Task 前，就先執行被呼叫的 Task，行為類似 `dependsOn`. 
* `dependsOn` 不要用 `dependsOn(a, b)` ；因為 depend 的順序會被 SBT 排序，不會是你預期的順序，改用 `dependsOn(a).dependsOn(b)` 這樣子，會先執行 `b` 再執行 `a`. 
* 下完 `clean` 後，記得下 `update`，以免 eclipse 專案或下次 build 會少檔案。
