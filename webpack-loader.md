url loader
---
```js
var loaderUtils = require("loader-utils");
var mime = require("mime");
module.exports = function(content) {
	this.cacheable && this.cacheable();
	var query = loaderUtils.parseQuery(this.query);
	var limit = (this.options && this.options.url && this.options.url.dataUrlLimit) || 0;
	if(query.limit) {
		limit = parseInt(query.limit, 10);
	}
	var mimetype = query.mimetype || query.minetype || mime.lookup(this.resourcePath);
	if(limit <= 0 || content.length < limit) {
		return "module.exports = " + JSON.stringify("data:" + (mimetype ? mimetype + ";" : "") + "base64," + content.toString("base64"));
	} else {
		var fileLoader = require("file-loader");
		return fileLoader.call(this, content);
	}
}
module.exports.raw = true;
```
file loader
---
```js
var loaderUtils = require("loader-utils");

module.exports = function(content) {
	this.cacheable && this.cacheable();
	if(!this.emitFile) throw new Error("emitFile is required from module system");
	var query = loaderUtils.parseQuery(this.query);
	var url = loaderUtils.interpolateName(this, query.name || "[hash].[ext]", {
		context: query.context || this.options.context,
		content: content,
		regExp: query.regExp
	});
	this.emitFile(url, content);
	return "module.exports = __webpack_public_path__ + " + JSON.stringify(url) + ";";
}
module.exports.raw = true;
```
json loader
---
```js
module.exports = function(source) {
	this.cacheable && this.cacheable();
	var value = typeof source === "string" ? JSON.parse(source) : source;
	this.value = [value];
	return "module.exports = " + JSON.stringify(value, undefined, "\t") + ";";
}
```
xml loader 
---
```js
var loaderUtils = require('loader-utils');
var parseString = require('xml2js').parseString;
var processors = require('xml2js/lib/processors');

module.exports = function(text) {
  this.cacheable && this.cacheable();
  var options = loaderUtils.parseQuery(this.query);

  Object.keys(options).forEach(function(key) {
    if (key.indexOf('Processors') > -1) {
      var array = options[key];
      for (var i = 0, len = array.length; i < len; i++) {
        array[i] = processors[array[i]];
      }
    }
  });

  var self = this;
  parseString(text, options, function(err, result) {
    self.callback(err, !err && "module.exports = " + JSON.stringify(result));
  });
};
```
markdown loader
---
```js
// marked renderer hack
marked.Renderer.prototype.code = function renderCode(code, lang) {
  const out = this.options.highlight(code, lang);
  const classMap = this.options.langPrefix + lang;

  if (!lang) {
    return `<pre><code>${out}\n</code></pre>`;
  }
  return `<pre class="${classMap}"><code class="${classMap}">${out}\n</code></pre>\n`;
};
```
webpack markdown loader
```js
  markdownLoader: {
    langPrefix: 'language-',
    highlight(code, lang) {
      const language = !lang || lang === 'html' ? 'markup' : lang;
      const Prism = global.Prism || reqPrism;

      if (!Prism.languages[language]) {
        require(`prismjs/components/prism-${language}.js`);
      }
      return Prism.highlight(code, Prism.languages[language]);
    }
  },
```
