# log4js-node 是什么

`log4js-node`是让[log4js 框架](https://github.com/stritti/log4js)在 **nodejs** 环境中使用的**转换库**。

在最开始创作它时，只是为了将其剥离适配于特定浏览器，并整理了一些 js 使其更好的在 nodejs 中工作。

虽然它与 **java** 的 **Log4j** 名称相似，但如果认为它将以相同方式运行，只会给您带来悲伤和困惑。

## 3.x 版本中的变化

[详细变化](v3-changes.md)

与 log4js 1.x 和 2.x（以及 0.x）之间有一些变化。如果使用出错，您可能应该阅读迁移指南。

**它是开箱即用的，支持以下功能:**

1、彩色控制台日志输出到`stdout`或`stderr`

2、文件 Appender，可以根据文件大小和日期动态的创建新的日志，并压缩保存旧的日志

3、`connect`/`express` 服务器的记录器

4、配置日志的`layout`/`patterns`

5、不同日志类别的不同日志级别(将应用程序日志的某些部分设置为`DEBUG`,其他部分仅设置为`ERROR`等)

**可选 Appender**

- SMTP
- GELF
- Loggly
- Logstash (UDP 和 HTTP)
- logFaces (UDP 和 HTTP)
- RabbitMQ
- Redis
- Hipchat
- Slack
- mailgun

## 安装

想使用 `log4js-node` 第一件要做的事是从[npm](https://www.npmjs.com/)下载并安装它。

`npm install log4js-node`

## 使用方法

- 极简版:

```js
var log4js = require("log4js");
var logger = log4js.getLogger();
logger.level = "debug";
logger.debug("Some debug messages");
```

默认情况下，**log4js**不会输出任何日志（以便在库中安全使用）。默认类别的级别设置为`OFF`。要启用日志，请设置级别（如示例中所示）。这将输出到 stdout，并带有彩色布局（感谢 masylum），因此对于上面的内容，您将看到：

```
[2010-01-17 11:43:37.987] [DEBUG] [default] - Some debug messages
```

完整的示例请参见 [example.js](https://github.com/log4js-node/log4js-node/blob/master/examples/example.js)，但这里有些片段（同样在 [examples/fromreadme.js](https://github.com/log4js-node/log4js-node/blob/master/examples/fromreadme.js) 中）

```js
const log4js = require("log4js");
log4js.configure({
  appenders: { cheese: { type: "file", filename: "cheese.log" } },
  categories: { default: { appenders: ["cheese"], level: "error" } }
});

const logger = log4js.getLogger("cheese");
logger.trace("Entering cheese testing");
logger.debug("Got cheese.");
logger.info("Cheese is Comté.");
logger.warn("Cheese is quite smelly.");
logger.error("Cheese is too ripe!");
logger.fatal("Cheese was breeding ground for listeria.");
```

输出(在 cheese.log 中):

```
[2010-01-17 11:43:37.987] [ERROR] cheese - Cheese is too ripe!
[2010-01-17 11:43:37.990] [FATAL] cheese - Cheese was breeding ground for listeria.
```

## 给第三方库制作者的说明

如果您正在编写一个库，并且希望包含对 log4js 的支持，而又不想给用户带来麻烦，请查看[log4js-api](https://github.com/log4js-node/log4js-api)。

## 文档

[官方英文在线文档](https://log4js-node.github.io/log4js-node/)
还有一个[示例应用程序](https://github.com/log4js-node/log4js-example)。

## TypeScript

```js
import { configure, getLogger } from "log4js";
configure("./filename");
const logger = getLogger();
logger.level = "debug";
logger.debug("Some debug messages");

configure({
  appenders: { cheese: { type: "file", filename: "cheese.log" } },
  categories: { default: { appenders: ["cheese"], level: "error" } }
});
```
