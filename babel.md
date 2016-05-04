babel 6 presets
---
```shell
npm install babel-preset-es2015 babel-preset-react
babel --presets es2015,react --watch src/ --out-dir lib/
```
.babelrc
```
{
  “presets”: [“es2015”]
}
```
browsify
---
```shell
npm install --save react react-dom babelify babel-preset-react
browserify -t [ babelify ] main.js -o bundle.js
```
webpack
---
```shell
npm install --save react react-dom babel-preset-react babel-loader babel-core
webpack main.js bundle.js --module-bind 'js=babel-loader'
```
Nashorn
---
`Nashorn` is a lightweight high-performance JavaScript runtime that runs within the JVM. `React` should run out of the box in Java 8+.
```java
import java.io.IOException;
import java.io.InputStream;
import java.io.FileReader;

import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class ReactRender
{
  public static void main(String[] args) throws ScriptException, IOException {
    ScriptEngine nashorn = new ScriptEngineManager().getEngineByName("nashorn");

    // These files can be downloaded as a part of the starter kit
    // from https://facebook.github.io/react
    nashorn.eval(new FileReader("path/to/react.js"));
    nashorn.eval(new FileReader("path/to/react-dom-server.js"));

    System.out.println(nashorn.eval(
      "ReactDOMServer.renderToString(" +
        "React.createElement('div', null, 'Hello World!')" +
      ");"
    ));
  }
}
```
