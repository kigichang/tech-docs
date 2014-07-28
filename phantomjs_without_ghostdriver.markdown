# Use PhantomJS to Get Images without GhostDriver

## Preface

I want to get image size from web page, especially from ajax web page. I use PhantomJS and GhostDriver. But the solution is slow because my scala program communicate PhantomJS via GhostDriver's micro-server. One image costs about 50 milli-seconds. I change to call PhantomJS directly and get results from it.


## Use Scala Process

Use Scala's Process is very simple. You just import `scala.sys.process._` and run a process with `String_Cmd !`. You can refer the [scala document](http://www.scala-lang.org/api/current/index.html#scala.sys.process.ProcessBuilder).


## Use PhantomJS to Get Image Size

I write a javascript run by PhantomJS. I output the src, width, and height of images to console. The last line is result page source code. I also assign the exit code(ex: `phantom.exit(1)`). It is used in scala later.

```
var system = require('system');

if (system.args.length < 2) {
    console.log('phanthom crawl.js url');
    phantom.exit(1);
}

var url           = system.args[1]; 
var page          = require('webpage').create();
page.viewportSize = { width: 1280, height : 2400 };

/*console.log("url: " + url);*/
 
page.open(url, function (status) {
    /*console.log(status)*/
    if (status !== "success") {
        /*console.log('Unable to load url');*/
        phantom.exit(2);
    } else {
        /*console.log("ok");*/
        var results = page.evaluate(function() {
             var list = document.images, images = [], i;
             for (i = 0; i < list.length; i++) {
                var width = list[i].width, height = list[i].height;
                if (width == height && width >= 128)
                    images.push({src: list[i].src, width: list[i].width, height: list[i].height});
             }
             return images;
        });

        console.log(results.length)
        for (k = 0; k < results.length; k++) {
            console.log(results[k].width +"," + results[k].height + "," + results[k].src);
        }
        console.log(page.content)
        phantom.exit(0);
    }
    
});
```

You can save the sample code as `crawl.js`.


## Scala call PhantomJS Process and Get Results

I can use [ProcessLogger](http://www.scala-lang.org/api/current/index.html#scala.sys.process.ProcessLogger) to get output of proces on console.

```
trait CrawlResponse
  
case class CrawlResult(product: Product) extends CrawlResponse
  
case class CrawlError(url: String, exitCode: Int) extends CrawlResponse
  
val result = new scala.collection.mutable.StringBuilder
  val error = new scala.collection.mutable.StringBuilder
  val log = ProcessLogger(line => result.append(line + "\n"), line => error.append(line + "\n"))
  
  def crawl(url: String): CrawlResponse = {
    result.clear
    error.clear
    val exitCode = (cmd + url) ! log
    
    if (exitCode == 0) {
      CrawlResult(Page(result.lines.toArray).toProduct(url))
    }
    else {
      CrawlError(url, exitCode)
    }  
  }
  
```

The `cmd` is `phantomjs_path/bin/phantomjs your_path/crawl.js `. (reserve a space after crawl.js). We run PhantomJS process with `val exitCode = (cmd + url) ! log` and get the exit code to verify if process is success. We can get the result strings with ProcessLogger.



