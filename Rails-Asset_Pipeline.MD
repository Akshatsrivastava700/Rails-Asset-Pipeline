# ASSET PIPELINE - RAILS(6.1)

* ## What is Asset Pipeline?

	The asset pipeline provides a framework to concatenate and minify or compress JavaScript and CSS assets. It also adds the ability to write these assets in other languages and pre-processors such as CoffeeScript, Sass, and ERB. It allows assets in your application to be automatically combined with assets from other gems.

	* **Main Features :**
		
		* The first feature of the pipeline is to *concatenate assets*, which can reduce the number of requests that a browser makes to render a web page.

			Sprockets concatenates all JavaScript files into one master .js file and all CSS files into one master .css file.
		
		* The second feature of the asset pipeline is asset minification or compression. For CSS files, this is done by removing whitespace and comments. For JavaScript, more complex processes can be applied. You can choose from a set of built in options or specify your own.

		* The third feature of the asset pipeline is it allows coding assets via a higher-level language, with precompilation down to the actual assets.
	
	* **Fingerprinting :**

		* Fingerprinting is a technique that makes the name of a file dependent on the contents of the file. When the file contents change, the filename is also changed.

		* Cache Busting :
			When a filename is unique and based on its content, HTTP headers can be set to encourage caches everywhere (whether at CDNs, at ISPs, in networking equipment, or in web browsers) to keep their own copy of the content. When the content is updated, the fingerprint will change. This will cause the remote clients to request a new copy of the content.

* ## How to use Asset Pipeline :

	* **Controller Specific Assets :**

		 When you generate a scaffold or a controller, Rails also generates a Cascading Style Sheet file (or SCSS file if sass-rails is in the Gemfile) for that controller. Additionally, when generating a scaffold, Rails generates the file scaffolds.css (or scaffolds.scss if sass-rails is in the Gemfile.)

		You can also opt to include controller specific stylesheets and JavaScript files only in their respective controllers using the following:

		```{ruby}
		<%= javascript_include_tag params[:controller] %> or 
		<%= stylesheet_link_tag params[:controller] %>
		```

		When doing this, ensure you are not using the require_tree directive, as that will result in your assets being included more than once.

	* **Asset Organization :**

		Pipeline assets can be placed inside an application in one of three locations: app/assets, lib/assets or vendor/assets.

		* Search Paths :

			When a file is referenced from a manifest or a helper, Sprockets searches the three default asset locations for it.

			The default locations are: **the images, javascripts and stylesheets directories** under the app/assets folder, but these subdirectories are not special - any path under assets/* will be searched.

		* Using Index Files :

			Sprockets uses files named index (with the relevant extensions) for a special purpose.

			For example, if you have a jQuery library with many modules, which is stored in lib/assets/javascripts/library_name, the file lib/assets/javascripts/library_name/index.js serves as the manifest for all files in this library. This file could include a list of all the required files in order, or a simple require_tree directive.

	* **Coding Link Assets :**

		Sprockets does not add any new methods to access your assets - you still use the familiar javascript_include_tag and stylesheet_link_tag:

		```{ruby}
		<%= stylesheet_link_tag "application", media: "all" %>
		<%= javascript_include_tag "application" %>
		<%= stylesheet_link_tag "application", media: "all" %>
		<%= javascript_include_tag "application" %>
		```
		
		If using the turbolinks gem, which is included by default in Rails, then include the 'data-turbolinks-track' option which causes turbolinks to check if an asset has been updated and if so loads it into the page:

		```{ruby}
		<%= stylesheet_link_tag "application", media: "all", "data-turbolinks-track" => "reload" %>
		<%= javascript_include_tag "application", "data-turbolinks-track" => "reload" %>
		<%= stylesheet_link_tag "application", media: "all", "data-turbolinks-track" => "reload" %>
		<%= javascript_include_tag "application", "data-turbolinks-track" => "reload" %>
		```

		In regular views you can access images in the app/assets/images directory like this:

		```{ruby}
		<%= image_tag "rails.png" %>
		<%= image_tag "rails.png" %>
		```

		Provided that the pipeline is enabled within your application (and not disabled in the current environment context), this file is served by Sprockets. If a file exists at public/assets/rails.png it is served by the web server.

	* **Manifest Files and Directives :**

		Sprockets uses manifest files to determine which assets to include and serve. These manifest files contain directives - instructions that tell Sprockets which files to require in order to build a single CSS or JavaScript file. With these directives, Sprockets loads the files specified, processes them if necessary, concatenates them into one single file, and then compresses them (based on value of Rails.application.config.assets.js_compressor). By serving one file rather than many, the load time of pages can be greatly reduced because the browser makes fewer requests. Compression also reduces file size, enabling the browser to download them faster.
	
	* **Preprocessing :**

		The file extensions used on an asset determine what preprocessing is applied. When a controller or a scaffold is generated with the default Rails gemset, an SCSS file is generated in place of a regular CSS file. The example used before was a controller called "projects", which generated an app/assets/stylesheets/projects.scss file.

		In development mode, or if the asset pipeline is disabled, when this file is requested it is processed by the processor provided by the sass-rails gem and then sent back to the browser as CSS. When asset pipelining is enabled, this file is preprocessed and placed in the public/assets directory for serving by either the Rails app or web server.

* ## Customizing the Pipeline :

	* **CSS Compression :**
		One of the options for compressing CSS is YUI. The YUI CSS compressor provides minification.

		The following line enables YUI compression, and requires the yui-compressor gem.

		```{ruby}
		config.assets.css_compressor = :yui
		config.assets.css_compressor = :yui
		```

		The other option for compressing CSS if you have the sass-rails gem installed is

		```{ruby}
		config.assets.css_compressor = :sass
		config.assets.css_compressor = :sass
		```

	* **JavaScript Compression :**

		Possible options for JavaScript compression are **:closure, :uglifier** and **:yui**. These require the use of the closure-compiler, uglifier or yui-compressor gems, respectively.

		Take the uglifier gem,This gem wraps UglifyJS (written for NodeJS) in Ruby. It compresses your code by removing white space and comments, shortening local variable names, and performing other micro-optimizations such as changing if and else statements to ternary operators where possible.

		The following line invokes uglifier for JavaScript compression.

		```{ruby}
		config.assets.js_compressor = :uglifier
		```

	* **GZipping your assets :**

		By default, gzipped version of compiled assets will be generated, along with the non-gzipped version of assets. Gzipped assets help reduce the transmission of data over the wire. You can configure this by setting the gzip flag.

		```{ruby}
		# disable gzipped assets generation
		config.assets.gzip = false
		```

	* **Using Your Own Compressor :**

		The compressor config settings for CSS and JavaScript also take any object. This object must have a compress method that takes a string as the sole argument and it must return a string.

		```{ruby}
			class Transformer
				def compress(string)
					do_something_returning_a_string(string)
				end
			end
			class Transformer
				def compress(string)
					do_something_returning_a_string(string)
				end
			end
		```


		To enable this, pass a new object to the config option in application.rb:

		```{ruby}
		config.assets.css_compressor = Transformer.new
		```

	* **Changing the assets Path :**
		
		The public path that Sprockets uses by default is /assets.

		This can be changed to something else:

		```{ruby}
		config.assets.prefix = "/some_other_path"
		```

* ## Asset Cache Store :

	By default, Sprockets caches assets in tmp/cache/assets in development and production environments. This can be changed as follows:

	```{ruby}
	config.assets.configure do |env|
	env.cache = ActiveSupport::Cache.lookup_store(:memory_store,
													{ size: 32.megabytes })
	end
	config.assets.configure do |env|
	env.cache = ActiveSupport::Cache.lookup_store(:memory_store,
													{ size: 32.megabytes })
	end
	```

	To disable the assets cache store:

	```{ruby}
	config.assets.configure do |env|
	env.cache = ActiveSupport::Cache.lookup_store(:null_store)
	end
	```

* ## Adding Assets to Your Gems :

	Sprockets uses Processors, Transformers, Compressors, and Exporters to extend Sprockets functionality. Have a look at Extending Sprockets to learn more. Here we registered a preprocessor to add a comment to the end of text/css (.css) files.

	```{ruby}
	module AddComment
		def self.call(input)
			{ data: input[:data] + "/* Hello From my sprockets extension */" }
		end
	end
	```

	Now that you have a module that modifies the input data, it's time to register it as a preprocessor for your mime type.

	```{ruby}
	Sprockets.register_preprocessor 'text/css', AddComment
	```

## REFRENCES:

* <https://guides.rubyonrails.org/asset_pipeline.html>

* <https://gist.github.com/laurenfazah/9ac145b363fed6def0db5c2817bd2f91>

* <https://www.youtube.com/watch?v=kgEcdBGpr54>

* <https://www.youtube.com/watch?v=maTYAac1ESI>