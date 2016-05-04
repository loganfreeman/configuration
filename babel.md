babel 6 presets
---
```shell
npm install babel-preset-es2015 babel-preset-react
babel --presets es2015,react --watch src/ --out-dir lib/
```
.babelrc
```json
{
  “presets”: [“es2015”]
}
```
