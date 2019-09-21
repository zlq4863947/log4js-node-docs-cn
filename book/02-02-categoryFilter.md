# Category Filter

严格来说，这不是一个 appender - 它将另一个 appender 包裹起来，并阻止将来自特定类别的日志事件写入该 appender。这在调试应用程序时可能很有用，但是您有一个组件会发出嘈杂的日志，或者与您的调查无关。

## 配置

- `type` - `"categoryFilter"`
- `exclude` - `string | Array<string>` - 将从 appender 中排除的类别（如果提供的是数组，则为类别列表）。
- `appender` - `string` - 要筛选的 appender 的名称。

## 例子

```javascript
log4js.configure({
  appenders: {
    everything: { type: "file", filename: "all-the-logs.log" },
    "no-noise": {
      type: "categoryFilter",
      exclude: "noisy.component",
      appender: "everything"
    }
  },
  categories: {
    default: { appenders: ["no-noise"], level: "debug" }
  }
});

const logger = log4js.getLogger();
const noisyLogger = log4js.getLogger("noisy.component");
logger.debug("I will be logged in all-the-logs.log");
noisyLogger.debug("I will not be logged.");
```

注意，您可以在不使用类别过滤器的情况下实现相同的结果，如下所示:

```javascript
log4js.configure({
  appenders: {
    everything: { type: "file", filename: "all-the-logs.log" }
  },
  categories: {
    default: { appenders: ["everything"], level: "debug" },
    "noisy.component": { appenders: ["everything"], level: "off" }
  }
});

const logger = log4js.getLogger();
const noisyLogger = log4js.getLogger("noisy.component");
logger.debug("I will be logged in all-the-logs.log");
noisyLogger.debug("I will not be logged.");
```

当您有许多要排除的类别时，类别筛选器将变得非常有用，并将它们作为数组传递。
