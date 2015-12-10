# I/O Content Example Blog

## A simple HTML, JS and CSS blog backed by [I/O Content](http://www.icontent.com) API driven CMS

This simple blog is an example of how [I/O Content](http://www.icontent.com) can be used to manage content for websites. You can also use this example to run your blog using content in your I/O Content account.


The **[Demo](http://exampleblog.iocontent.com/index.html?key=xtun42ocqlxeu56wim5s3ccuma)** is hosted on Amazon S3 using S3's [static website](http://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html) feature.

This blog uses simple HTML, JS and CSS, backed by the I/O Content API which serves content entries in response to a query sent over an HTTP GET request using the [io-content-js](https://github.com/appsoftware/io-content-js) library. 

The only required files required to run this blog are the `index.html` file and the `bower_components` folder.

![blog deploy files](https://cdn.iocontent.com/v1.0/assets/nl7bwlc4txh2uvw3s6h4gcclgb/20151113-121206643/062b/example-blog-required-files.png)

*Note this demo is not available as a bower package, and all dependencies have been copied to the respository so you can simply copy deploy this website on any web server. This will not run from the local file system, so you will need to run via your local web server (e.g. Apache / IIS) to test.*

## How this example blog works

This example pulls content hosted in our 'blog-example' sub account. You'll need to set up you're own sub account and content types and edit the sample as appropriate. Full [documentation](https://github.com/appsoftware/io-content-docs)  for I/O Content describes how to manage content, set up content types and query the API.

The dependencies for this example are:

- [io-content-js](https://github.com/appsoftware/io-content-js) - for querying the I/O content API
- [AngularJS](https://github.com/angular) - for rendering UI / managing routes
- [gridism](https://github.com/cobyism/gridism) - for a light weight CSS grid
- [github-markdown-css](https://github.com/sindresorhus/github-markdown-css) - simple markdown / html styling

Note that I/O Content is queried via REST API, and [io-content-js](https://github.com/appsoftware/io-content-js) assists with making cross domain requests compatible with all major browsers.

AngularJS routing and templating is intentionally avoided to keep the file structure of this example as basic as possible. If you would like an example using AngularJS routing and disquss comments, our official blog is [also available on GitHub](https://github.com/appsoftware/io-content-blog).

### API Query

The core content fetch code is as follows:

**1. Set up the content client**


	var contentClient = new ContentClient();
	
	contentClient.contentClientBaseParameters.subAccountKey = 'nl7bwlc4txh2uvw3s6h4gcclgb';
	contentClient.contentClientBaseParameters.contentType = 'blog-article';


**2. Request a list of content entries and load the first into view**


	// Load a list of blog articles to allow user to navigate
								
	var blog = this;
	
	var apiCallBack = function (responseJson) {
	
		var contentApiResponse = JSON.parse(responseJson); // [] of content entries
		
		blog.articleNavList = contentApiResponse.data;
		
		var loadKey = contentKey != null ? contentKey : blog.articleNavList[0].key;
		
		$scope.blog.loadSingleBlogArticle(loadKey);
		
		$scope.$apply();
	}
	
	// Query specifies get content entries in descending order (by custom publicPublishDate field)
	// limit to the 20 most recent entries, and restrict the properties (select) to key and title, as
	// we don't need the full content entry for a navigation list
	
	var query = 'orderByDescending=publicPublishDate&pageNumber=1&pageSize=20&select=key+title';
	
	contentClient.get(query, apiCallBack);


### Content Assets

Content Assets managed in I/O Content are loaded from our CDN using plain HTTPS urls. Where requesting images, our CDN offers 'on the fly' image resizing, specified here using the `?maxHeight=80` query string argument. Resized images are cached in the CDN for fast load times.

Other file types are also served over the CDN in the same manner.


	<div>
		<img src="https://cdn.iocontent.com/v1.0/assets/nfm6dwvsmrd6uukgj3rzdugerc/20151209-161905637/rh2v/iocontent-blocks-logo-1454-x-7069.png?maxHeight=80" alt="logo" />
	</div>

Which retrieves the following image:

![io content logo](https://cdn.iocontent.com/v1.0/assets/nfm6dwvsmrd6uukgj3rzdugerc/20151209-161905637/rh2v/iocontent-blocks-logo-1454-x-7069.png?maxHeight=80)
