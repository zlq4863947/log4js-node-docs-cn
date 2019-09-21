# Console Appender

该 appender 使用 nodejs 的控制台对象来写入日志事件。
如果您使用的是 browserify 或类似的东西，它也可以在浏览器中使用。
请注意，向控制台写入大量输出可能会使您的应用程序使用大量内存。
如果遇到此问题，请尝试切换到[stdout](stdout.md) appender。

# 配置

- `type` - `console`
- `layout` - `object` (可选, 默认 colouredLayout) - 参见[布局]](layouts.md)

请注意，无论事件的级别如何，所有日志事件均使用`console.log`输出（因此不会使用`console.error`记录`ERROR`事件）。

# 例子

```javascript
log4js.configure({
  appenders: { console: { type: "console" } },
  categories: { default: { appenders: ["console"], level: "info" } }
});
```
