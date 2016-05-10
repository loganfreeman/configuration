renderComponentToString
---
```js
app.get(/[/+A-Za-z0-9_~]*/, function (req, res) {
    var route = req.path;
    var router = new TodoRouter();
    res.header('Content-Type', 'text/html; charset=utf-8');
    res.header('Cache-Control', 'private, max-age=0, no-cache');
    res.write(htmlTemplate.head);
    router.load(route, function (path) {
        res.write(
            React.renderComponentToString(
                TodoAppView({
                    key: 'TodoApp',
                    app: {path: path}
                })
            )
        );
        res.write(htmlTemplate.tail);
        res.end();
    });
});
```
