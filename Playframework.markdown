# Play Framework Step by Step

## Installation

### Downoad Typesafe Activator Package

Although we can use SBT to build play framework project, I suggest use Activator. Activator can generate a new project, build, run and deploy it with SBT.

* visit [playframework](http://www.playframework.com/) and download the last version.

  __Attention: The Play2 plugin for Scala 2.11 on eclipse is NOT ready now(2014/07/19)__
  
* unzip it and set path

	```
	# TypeSafe
	export ACTIVATOR_HOME="/usr/local/activator"
	export PATH="$ACTIVATOR_HOME:$PATH"
	```


## Generate New Project

I develop with Scala 2.11, so I demo steps for scala.

__Attention: You MUST have [scala 2.11 plugins for eclipse](http://scala-ide.org/download/current.html) or [Scala-IDE](http://scala-ide.org/download/sdk.html)__

* generate new project

	Move to your workspace and execute `activator new your-project-name play-scala`

* convert to eclipse project format

   Move to your project folder and exec `activator eclipse`

* import to eclipse

	`Eclipse -> [File] -> [Import...] -> [General] -> [Existing Projects into Workspace]`


## Development

We have to add `target/scala-2.11/twirl/main` to source path because play2 plugins is not ready for scala 2.11. Play framework put templates (ex: main.scala.html) in `app/views` and convert to scala source in `target/scala-2.11/twirl/main`. We have to __DIY__ when add template or change `apply` parameters. But you do not refresh project if you edit content in templates.


### Add twirl to source path

Add `target/scala-2.11/twirl/main` to source path

`Right click on your project -> [Properties] -> [Java Build Path] -> [Source] -> [Add Folder...] -> choose [target/scala-2.11/twirl/main] -> [OK]`
	

### DIY when add template or change `apply`(i.e. @(xx:xx) in template first line) parameters

* open terminal and move to your project path
* exec `activator compile` to compile project
* back to eclipse and refresh project (`Right click project -> [Refresh]`)

__Attention: you may have ERROR if you do not compile and refresh manually__

### Run project when developing

Play framework auto-refresh codes, so we can see the result immediately.

* exec `activator run` 
* browse `http://localhost:9000` (the default port is `9000`)


## My Environment

* OSX 10.9.4
* Java 7
* Scala 2.11

Environment Variables

```
#Scala
export SCALA_HOME=/usr/local/scala
export PATH=$SCALA_HOME/bin:$PATH

# TypeSafe
export ACTIVATOR_HOME="/usr/local/activator"
export PATH="$ACTIVATOR_HOME:$PATH"
``` 