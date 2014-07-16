PhantomJS
=========

I crawl images in web page with PhantomJS and WebDriver. I summaries it below.

[PhantomJS](http://phantomjs.org/)

PhantomJS is a headless web test tool with WebKit engine. It can handling javascript including AJAX.

### Advantage：
* Headless
* Fast

### Disadvantage:
* Ajax is not alawys OK, especially Lazy-Load
* CSS rendering is sometimes not the same with browser

### Solution
We can use [sample code of Christian Joudrey](https://gist.github.com/cjoudrey/1341747) on GitHub. It wait some time when downloading resources. Although it does not solve problem totally, it is better than using PhantomJS only.

## PhantomJS with WebDriver

* Install [GhostDriver](https://github.com/detro/ghostdriver)
* Edit [twitter.js of Christian Joudrey](https://gist.github.com/cjoudrey/1341747)
	
	```
	var resourceWait  = 300,
	    maxRenderWait = 10000;
	 
	var page          = this, /* change to this for GhostDriver */
	    count         = 0,
	    forcedRenderTimeout,
	    renderTimeout;
	
	page.viewportSize = { width: 1280,  height : 1024 };
	
	function doRender() {
		
	}
	
	page.onResourceRequested = function (req) {
	    count += 1;
	    console.info('> ' + req.id + ' - ' + req.url);
	    clearTimeout(renderTimeout);
	};
	 
	page.onResourceReceived = function (res) {
	    if (!res.stage || res.stage === 'end') {
	        count -= 1;
	        console.info(res.id + ' ' + res.status + ' - ' + res.url);
	        if (count === 0) {
	            renderTimeout = setTimeout(doRender, resourceWait);
	        }
	    }
	};
	
	```
	
* Scala Sample Code

	```
	package phantom
	
	import org.openqa.selenium.remote.DesiredCapabilities
	import org.openqa.selenium.phantomjs.PhantomJSDriver
	import java.util.concurrent.TimeUnit
	import scala.collection.JavaConversions._
	import org.apache.commons.lang3.StringUtils
	import org.openqa.selenium.OutputType
	import java.io.File
	import org.apache.commons.io.FileUtils
	import org.openqa.selenium.Dimension
	
	object Phantom extends App {
	  val url = if (args.length >= 1) args(0) else "http://test.tw"
	  val snapShotFile = if (args.length >= 2) args(1) else "simple-phantom.jpg"
	  val dumpFile = if (args.length >= 3) args(2) else "simple-phantom.html"
	  
	  
	  val capabilities = new DesiredCapabilities()
	  // Set PhantomJS Path
	  capabilities.setCapability("phantomjs.binary.path", "PHANTOM_HOME/bin/phantomjs")
	  val driver = new PhantomJSDriver(capabilities)
	  
	  // execute modified codes of cjoudrey's twitter
	  val extra = scala.io.Source.fromFile("InitPhantom.js").getLines.mkString
	  driver.executePhantomJS(extra)
	  
	  // crawl page
	  driver.get(url)
	  
	  // find images in page
	  driver.findElementsByTagName("img").foreach(img => {
	    if (StringUtils.isNotBlank(img.getAttribute("src"))) {
	      val dim = img.getSize()
	      val pos = img.getLocation()
	      println(img.getAttribute("src"), pos.x, pos.y, dim.width, dim.height)
	    }
	  })
	  
	  // generate a snapshot image
	  val shot: File = driver.getScreenshotAs(OutputType.FILE)
	  FileUtils.copyFile(shot, new File(snapShotFile))
	  
	  // dump the result html code (contain the execution result of ajax)
	  val source = driver.getPageSource()
	  FileUtils.writeStringToFile(new File(dumpFile), source, "UTF-8")	  
	  driver.close()
	  driver.quit() 
	}
	
	```
