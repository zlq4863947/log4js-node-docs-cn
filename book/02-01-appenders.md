# Log4js - Appenders

Appender 将日志事件序列化为某种形式的输出。他们可以写文件，发送电子邮件，通过网络发送数据。 所有的 appender 都有一个`type`，用于确定日志的输出方式。

例如:

```javascript
const log4js = require("log4js");
log4js.configure({
  appenders: {
    out: { type: "stdout" },
    app: { type: "file", filename: "application.log" }
  },
  categories: {
    default: { appenders: ["out", "app"], level: "debug" }
  }
});
```

这定义了两个名为`out`和`app`的 appender。 `out`使用写入标准输出的[stdout](stdout.md)的 appender。`app`使用配置为写入`application.log`[文件](file.md)的 appender。

## 核心 Appenders

log4js 包含以下 appender。有些需要额外的依赖，这些依赖关系未包含在 log4js 中（例如[smtp](smtp.md)的 appender 需要[nodemailer](https://www.npmjs.org/packages/nodemailer)），这些将在该 appender 的文档中指出。如果不使用这些 appender，则不需要额外的依赖项。

- [categoryFilter](categoryFilter.md)
- [console](console.md)
- [dateFile](dateFile.md)
- [file](file.md)
- [fileSync](fileSync.md)
- [logLevelFilter](logLevelFilter.md)
- [multiFile](multiFile.md)
- [multiprocess](multiprocess.md)
- [recording](recording.md)
- [stderr](stderr.md)
- [stdout](stdout.md)
- [tcp](tcp.md)
- [tcp-server](tcp-server.md)

## 可选 Appenders

log4js 支持以下 appender，但从 v3 版本开始不再与 log4js 核心一起分发。

- [gelf](https://github.com/log4js-node/gelf)
- [hipchat](https://github.com/log4js-node/hipchat)
- [logFaces-HTTP](https://github.com/log4js-node/logFaces-HTTP)
- [logFaces-UDP](https://github.com/log4js-node/logFaces-UDP)
- [loggly](https://github.com/log4js-node/loggly)
- [logstashHTTP](https://github.com/log4js-node/logstashHTTP)
- [logstashUDP](https://github.com/log4js-node/logstashUDP)
- [mailgun](https://github.com/log4js-node/mailgun)
- [rabbitmq](https://github.com/log4js-node/rabbitmq)
- [redis](https://github.com/log4js-node/redis)
- [slack](https://github.com/log4js-node/slack)
- [smtp](https://github.com/log4js-node/smtp)

例如，如果您以前使用过 gelf 的 appender（`type：'gelf'`），则应在依赖项中添加`@log4js-node/gelf`并将类型更改为`type: '@log4js-node/gelf'`。

## 其他 Appenders

Log4js 可以从核心 appender 外部加载 appender。如果找不到匹配的 appender，则将`type`配置值用作 require 路径。
例如，以下配置将尝试从模块'cheese/appender'中加载 appender，并将该 appender 的其余配置传递给该模块：

```javascript
log4js.configure({
  appenders: { gouda: { type: "cheese/appender", flavour: "tasty" } },
  categories: { default: { appenders: ["gouda"], level: "debug" } }
});
```

log4js 根据类型值检查以下位置（按此顺序）是否有 appender:

1. 核心 appenders: `require('./appenders/' + type)`
2. node_modules: `require(type)`
3. 相对于应用程序的主文件: `require(path.dirname(require.main.filename) + '/' + type)`
4. 相对于进程的当前工作目录: `require(process.cwd() + '/' + type)`

如果要编写自己的 appender，请先阅读[文档](writing-appenders.md)。

## 高级配置

如果您有自己的自定义 appender 或者正在使用 webpack（或其他捆绑程序），您可能会发现更容易通过在配置的 appender 模块中，而不是从 node.js 加载 require 路径。

这里有一个例子:

```javascript
const myAppenderModule = {
  configure: (config, layouts, findAppender, levels) => {
    /* ...你的appender配置... */
  }
};
log4js.configure({
  appenders: { custom: { type: myAppenderModule } },
  categories: { default: { appenders: ["custom"], level: "debug" } }
});
```
