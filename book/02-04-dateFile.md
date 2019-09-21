# Date Rolling File Appender

这是一个文件 appender，它根据可配置的时间而不是文件大小来滚动日志文件。
当使用日期文件 appender 时，您还应在应用程序终止时调用 log4js.shutdown，以确保完成所有剩余的异步写入。 尽管日期文件 appender 使用[streamroller](https://github.com/nomiddlename/streamroller) 库，但该库作为 log4js 的依赖项包含在内，因此您无需自己包含它。

## 配置

- `type` - `"dateFile"`
- `filename` - `string` - 您要将日志写入的文件路径。
- `pattern` - `string` (可选, 默认 `.yyyy-MM-dd`) - 用于确定何时滚动日志的模式。
- `layout` - (可选, 默认 basic 布局) - 参见[布局](layouts.md)

任何其他配置参数都将传递给 underlying[streamroller](https://github.com/nomiddlename/streamroller)实现（另请参见 node.js 核心文件流）:

- `encoding` - `string` (默认 "utf-8")
- `mode`- `integer` (默认 0o644 - [node.js file modes](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_file_modes))
- `flags` - `string` (默认 'a')
- `compress` - `boolean` (默认 false) - 在滚动过程中压缩备份文件（备份文件的扩展名为`.gz`）
- `alwaysIncludePattern` - `boolean` (默认 false) - 在当前日志文件和备份的名称中包括该模式。
- `daysToKeep` - `integer` (默认 0) - 如果此值大于零，则日志滚动期间将删除早于该天数的文件。
- `keepFileExt` - `boolean` (默认 false) - 旋转日志文件时保留文件扩展名 (`file.log` 变为 `file.2017-05-30.log` 而不是 `file.log.2017-05-30`).

`pattern`用于确定何时重命名当前日志文件并创建新的日志文件。
例如，文件名为`cheese.log`，默认模式为`.yyyy-mm-dd` - 启动时，将创建并写入名为`cheese.log`的文件，直到午夜后的下一次写入。发生这种情况时，`cheese.log`将重命名为`cheese.log.2017-04-30`，并创建一个新的`cheese.log`文件。AppMnter 使用[date-format](https://github.com/nomiddlename/date-format)库来解析`pattern`，并且可以使用任何有效格式。还要注意，没有定时器控制日志滚动 - 模式中的更改是在每次日志写入时确定的。如果没有发生写操作，则不会发生日志滚动。如果应用程序很少记录日志，则可能导致在特定时间段内没有写入日志文件。

注意，从 Log4JS 的 4.x 版本开始，文件 appender 可以为[file appender](file.md)选择任何选项。所以你可以按日期和大小滚动文件。

## 示例（默认每日日志滚动）

```javascript
log4js.configure({
  appenders: {
    everything: { type: "dateFile", filename: "all-the-logs.log" }
  },
  categories: {
    default: { appenders: ["everything"], level: "debug" }
  }
});
```

此示例将导致每天滚动文件。初始文件为`all-the-logs.log`，每日备份为`all-the-logs.log.2017-04-30`等。

## 每小时滚动日志（和压缩备份）的示例

```javascript
log4js.configure({
  appenders: {
    everything: {
      type: "dateFile",
      filename: "all-the-logs.log",
      pattern: ".yyyy-MM-dd-hh",
      compress: true
    }
  },
  categories: {
    default: { appenders: ["everything"], level: "debug" }
  }
});
```

这将生成一个当前日志文件（`all-the-logs.log`）。每隔一小时，此文件将被压缩并重命名为`all-the-logs.log.2017-04-30-08.gz`并创建一个新的`all-the-logs.log`。
