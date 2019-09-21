# 布局

布局是**appender**中用来格式化日志事件输出的方法集。
他们讲日志事件作为参数并返回一个字符串。

Log4js 的**appender**内置了几个布局,并提供了在**appender**内置布局不适用的情况下创建自己的**appender**布局的方法。

在大多数情况下，您不需要配置布局-有些**appender**不需要定义 Layouts（例如[logFaces-UDP](logFaces-UDP.md)）: 所有使用布局的**appender**都将定义一个合理的默认值。

## 配置

大多数**appender**配置都会使用一个名为`layout`的字段，这是一个对象，通常只有一个字段`type`，即下面定义的布局名称。有些布局需要额外的配置选项，这些选项应该包含在同一个对象中。

## 例子

```javascript
log4js.configure({
  appenders: { out: { type: "stdout", layout: { type: "basic" } } },
  categories: { default: { appenders: ["out"], level: "info" } }
});
```

此配置将[stdout](stdout.md)的**appender**默认`coloured`布局替换为`basic`布局。

# 内置布局

## Basic

- `type` - `basic`

基本布局将输出时间戳，级别，类别，然后输出格式化的日志事件数据。

## 例子

```javascript
log4js.configure({
  appenders: { out: { type: "stdout", layout: { type: "basic" } } },
  categories: { default: { appenders: ["out"], level: "info" } }
});
const logger = log4js.getLogger("cheese");
logger.error("Cheese is too ripe!");
```

将输出为:

```
[2017-03-30 07:57:00.113] [ERROR] cheese - Cheese is too ripe!
```

## Coloured

- `type` - `coloured` (或 `colored`)

此布局与`basic`相同，只是时间戳，级别和类别将根据日志事件的级别进行着色（如果您的终端/文件支持它-如果您在输出中看到一些奇怪的字符并且没有颜色，那么您应该切换到`basic布局`）。 使用的颜色是：

- `TRACE` - 'blue'
- `DEBUG` - 'cyan'
- `INFO` - 'green'
- `WARN` - 'yellow'
- `ERROR` - 'red'
- `FATAL` - 'magenta'

## Message Pass-Through

- `type` - `messagePassThrough`

此布局仅格式化日志事件数据，不输出时间戳，级别或类别。它通常用在使用特定格式（例如:[gelf](gelf.md)）对事件进行序列化的**appender**中。

## 例子

```javascript
log4js.configure({
  appenders: {
    out: { type: "stdout", layout: { type: "messagePassThrough" } }
  },
  categories: { default: { appenders: ["out"], level: "info" } }
});
const logger = log4js.getLogger("cheese");
const cheeseName = "gouda";
logger.error("Cheese is too ripe! Cheese was: ", cheeseName);
```

将输出:

```
Cheese is too ripe! Cheese was: gouda
```

## Dummy

- `type` - `dummy`

此布局仅输出日志事件数据中的第一个值。它是为[logstashUDP](logstashUDP.md)的 appender 添加的，我不确定除此之外还有其他用途。

## 例子

```javascript
log4js.configure({
  appenders: { out: { type: "stdout", layout: { type: "dummy" } } },
  categories: { default: { appenders: ["out"], level: "info" } }
});
const logger = log4js.getLogger("cheese");
const cheeseName = "gouda";
logger.error("Cheese is too ripe! Cheese was: ", cheeseName);
```

将输出:

```
Cheese is too ripe! Cheese was:
```

## 模式

- `type` - `pattern`
- `pattern` - `string` - 输出格式的说明符，使用如下所述的占位符
- `tokens` - `object` (可选) - 要在模式中使用的用户定义的 tokens

## 模式格式

模式字符串可以包含任何字符，但是从`%`开始的序列将被从日志事件和其他环境值中取值替换。
说明符的格式是`%[padding].[truncation][field]{[format]}`- padding 和 truncation 是可选的，格式只适用于少数标记（特别是日期）。
例如%5.10p - 将日志级别左移 5 个字符，最多 10 个字符

字段可以为以下值:

- `%r` ToLocaleTimeString 格式的时间
- `%p` 日志级别
- `%c` 日志类别
- `%h` 主机名
- `%m` 日志数据
- `%d` 日期, 格式化 - 默认值为 `ISO8601`, 格式化选项: `ISO8601`、 `ISO8601_WITH_TZ_OFFSET`、`ABSOLUTE`、`DATE`或任何与[date-format](https://www.npmjs.com/package/date-format) 库兼容的字符串。例如: `%d{DATE}`、`%d{yyyy/MM/dd-hh.mm.ss}`
- `%%` % - 当您想要在输出中使用字符`％`时
- `%n` 换行
- `%z` 进程 ID（来自`process.pid`）
- `%f` 文件名的完整路径（在类别上需要`enableCallStack:true`，请参见[配置对象](api.md)）
- `%f{depth}` 路径深度,让您选择仅具有文件名（`％f{1}`）或选定数量的目录
- `%l` 行号（在类别上需要`enableCallStack:true`，请参见[配置对象](api.md)）
- `%o` 列位置（在类别上需要`enableCallStack:true`，请参见[配置对象](api.md)）
- `%s` 调用堆栈（在类别上需要`enableCallStack:true`，请参见[配置对象](api.md)）
- `%x{<tokenname>}` 将动态令牌添加到您的日志中。 令牌是在 tokens 参数中指定的。
- `%X{<tokenname>}` 从 Logger 上下文中添加值。 令牌是上下文值的 key。
- `%[` 开始一个彩色块（颜色将从日志级别获取，类似于`colouredLayout`）
- `%]` 结束一个彩色块

## Tokens

用户定义的标记可以是字符串或函数。函数将传递给 log 事件，并应返回一个字符串。 例如，您可以定义一个自定义标记，为`用户`输出日志事件的上下文值，如下所示：

```javascript
log4js.configure({
  appenders: {
    out: {
      type: "stdout",
      layout: {
        type: "pattern",
        pattern: "%d %p %c %x{user} %m%n",
        tokens: {
          user: function(logEvent) {
            return AuthLibrary.currentUser();
          }
        }
      }
    }
  },
  categories: { default: { appenders: ["out"], level: "info" } }
});
const logger = log4js.getLogger();
logger.info("doing something.");
```

将输出:

```
2017-06-01 08:32:56.283 INFO default charlie doing something.
```

您还可以使用 Logger 上下文对象存储令牌（有时称为嵌套诊断上下文或映射的诊断上下文），并在布局中使用它们。

```javascript
log4js.configure({
  appenders: {
    out: {
      type: "stdout",
      layout: {
        type: "pattern",
        pattern: "%d %p %c %X{user} %m%n"
      }
    }
  },
  categories: { default: { appenders: ["out"], level: "info" } }
});
const logger = log4js.getLogger();
logger.addContext("user", "charlie");
logger.info("doing something.");
```

将输出:

```
2017-06-01 08:32:56.283 INFO default charlie doing something.
```

请注意，您还可以将方法添加到 Logger 上下文对象，它们也将传递给 log 事件。

# 添加自己的布局

您可以在调用`log4js.configure`之前调用`log4js.addLayout(type, fn)`添加自己的布局。
`type`是您要用来在**appender**配置中引用布局的标签。fn 是一个带有单个对象参数的函数，该参数将包含布局实例的配置，并返回一个布局函数。布局函数接受一个 log 事件参数并返回一个字符串（通常，尽管您可以返回任何内容，只要**appender**知道如何处理它）。

## 自定义布局示例

此示例也可以在[examples/custom-layout.js](https://github.com/log4js-node/log4js-node/blob/master/examples/custom-layout.js)中找到

```javascript
const log4js = require("log4js");

log4js.addLayout("json", function(config) {
  return function(logEvent) {
    return JSON.stringify(logEvent) + config.separator;
  };
});

log4js.configure({
  appenders: {
    out: { type: "stdout", layout: { type: "json", separator: "," } }
  },
  categories: {
    default: { appenders: ["out"], level: "info" }
  }
});

const logger = log4js.getLogger("json-test");
logger.info("this is just a test");
logger.error("of a custom appender");
logger.warn("that outputs json");
log4js.shutdown(() => {});
```

本示例输出以下内容:

```javascript
{"startTime":"2017-06-05T22:23:08.479Z","categoryName":"json-test","data":["this is just a test"],"level":{"level":20000,"levelStr":"INFO"},"context":{}},
{"startTime":"2017-06-05T22:23:08.483Z","categoryName":"json-test","data":["of a custom appender"],"level":{"level":40000,"levelStr":"ERROR"},"context":{}},
{"startTime":"2017-06-05T22:23:08.483Z","categoryName":"json-test","data":["that outputs json"],"level":{"level":30000,"levelStr":"WARN"},"context":{}},
```
