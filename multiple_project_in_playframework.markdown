# Multiple Projects in Playframework

## Detail Resources

[Working with sub-projects](https://www.playframework.com/documentation/2.3.x/SBTSubProjects)


##Note

* 	Suggest that creating a sub-folder in views of subproject and it can be used by `views.html.sub-folder.template-name(parameters)` like `views.html.admin.aa()`

*	Subproject is part of parent
	
	* `Application.conf` of subproject will be a part of parnet one. Value will be relpaced with subproject. 
	

*	Subproject is not part of parent

	* Subproject must have routes in path `conf` or custom setting in `Application.conf`
	

## Sample

[https://github.com/kigichang/multi-play](https://github.com/kigichang/multi-play)


