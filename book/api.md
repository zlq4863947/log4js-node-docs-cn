## API

## 配置 - `log4js.configure(object | string)`

配置`log4js`的入口，字符串参数被视为要从中加载配置的文件名。
配置文件应为`json`格式，并且包含一个配置对象（见下面的格式）。

您还可以直接将配置对象传递给**configure**。

- 首次使用

在您的应用程序中**首次使用** log4js 时，应立即进行**配置**。 如果您不调用`configure`，log4js 将使用`LOG4JS_CONFIG`（如果已定义）或默认配置。

默认配置定义了一个附加程序，它将以**彩色布局**输出到**stdout**，但也默认**日志级别**定义为**OFF** - 这意味着将不输出任何日志。

如果使用的是 `cluster`， 那么在工作进程和主进程中都应包含对 `configure`的调用。

这样，工作进程将为您的**类别**以及您可能定义的**任何自定义级别**选择合适的**级别**。

因此没有多个进程尝试使用同一**appender**的风险。与以往版本不同，在集群中使用**log4js**不需要特殊配置。

配置对象必须至少定义一个**appender**和一个默认**category**。如果配置无效，log4js 将引发异常。

`configure` 方法调用返回已配置的 log4js 对象。

### 配置对象

属性:

- `levels` (可选, 对象) - 用于设置**自定义日志级别**或重新设置**现有日志级别**

对象结构: `{key: {value, color}}`

其中级别名称为`key`(字符串，不区分大小写)，而值为对象，该对象具有 2 个属性:value(级别值，整数)和 color(颜色)。

日志级别用于为日志消息分配重要性，整数值用于对它们进行排序。
如果未在配置中指定任何内容，则使用默认值：

```
ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < MARK < OFF
```

> 请注意，OFF 用于关闭日志记录，而不是作为 实际日志记录级别，即您的`logger.off（'some log message'）`永远不会被调用

除默认级别外，还可定义额外的级别，并使用整数值确定它们与默认级别的关系。 如果使用与默认级别相同的名称定义级别，则覆盖掉默认的 value 值。

级别名称必须以字母开头，并且只能包含字母，数字和下划线。

- `appenders` (对象)
  对象结构: `{ [name: string]: Appender; }`

  Appender 对象必须有一个属性 `type` (string) - 其他属性取决于 appender 的 type.

- `categories` (对象)
  对象结构: `{ [name: string]: { appenders: string[]; level: string; enableCallStack: boolean } }`

  映射日志类别的定义，必须定义`default`类别，用于未匹配到相应类别时，使用默认值。

  - `name` - 要映射的日志类别名称。
  - `appenders`(字符串数组) - 用于此类别的 **appender 名称列表**。 **一个类别**必须至少有**一个 appender**。
  - `level`(字符串,不区分大小写) - 该**类别**将发送到**appender**的最低**日志级别**。

  例如，如果设置为**ERROR**，则**appender**将仅接收**级别**为**ERROR**、**FATAL**、**MARK**的日志事件。

  > **INFO**、**WARN**、**DEBUG**、**TRACE**的日志事件将被忽略。

  - `enableCallStack` (boolean, 可选, 默认为`false`) - 设置为`true`将使该类别的日志事件，使用**调用堆栈**在事件中生成**行号**和**文件名**。有关如何在**appender**中**输出这些值**的信息，请参见[模式布局](layouts.md)。

- `pm2` (boolean) (可选) - 如果使用[pm2](http://pm2.keymetrics.io),请设置此属性为`true`, 否则日志将不起作用（您还需要将 pm2-intercom 作为 pm2 模块安装：`pm2 install pm2-intercom`）
- `pm2InstanceVar` (string)可选，默认为'NODE_APP_INSTANCE'）-如果您使用的是 pm2，并且更改了 NODE_APP_INSTANCE 变量的默认名称，请设置此项。
- `disableClustering` (boolean)（可选）- 如果您想让 log4js 忽略集群环境的方式，或者您在 pm2 日志记录方面遇到问题，请将此设置为`true`。每个工作进程都将做自己的日志记录。如果你要输出到文件中，请特别注意这一点，可能会出现奇怪的情况。

## Loggers - `log4js.getLogger(category?: string)`

此方法接收一个**可选的字符串**，表示要用于此记录器的日志类别。
如果未指定，则路由到`default`类别的**appender**。

方法返回`Logger`对象，此对象可以使用下列方法:

- `<level>(args...)` - `<level>`可以是级别的任何小写名称（包括自定义级别）。
  例如: `logger.info('some info')` 将分配`info`级别的日志事件。如果您使用基础、彩色或 message pass-through [layouts](layouts.md), 则记录的字符串格式(占位符,例如`%s`、`%d`等) 将委托给[util.format](https://nodejs.org/api/util.html#util_util_format_format_args)。
- `is<level>Enabled()` - 返回布尔值，查询是否开启`<level>`(驼峰大小写)的日志级别。例如: 如果 logger 的**级别**为**INFO**或**更低**,则调用`logger.isInfoEnabled()` 将返回**true**。
- `addContext(key: string,value: any)` - 添加一个键值对，该键值对将添加到记录器生成的缩影日志事件中。例如可以添加 ID，来追踪应用中同一个用户的所以日志。目前只有`logFaces` 的 appender 使用了 context 值。
- `removeContext(key: string)` - 删除指定的 context。
- `clearContext()` - 清除所有 context 键值对。
- `setParseCallStackFunction(function)` - 允许重写解析布局模式的调用堆栈数据的默认方式，将向回调函数传递一个**通用 js 错误对象**。必须返回具有以下属性的对象, `functionName` / `fileName` / `lineNumber` / `columnNumber` / `callStack`。例如，如果您的所有日志调用均来自一个`debug`类，并且您希望从调用堆栈中**擦除**该类,以便仅显示调用了`debug`类的函数，则可以使用该方法。
- `level` - 当前级别值，可以通过更改该值从而重写原本的级别值。

## Shutdown - `log4js.shutdown(cb)`

`shutdown` 接受一个回调方法，以使**log4js**在**关闭全部 appender**并**完成全部日志事件入**时，调用此回调。用例: 在程序退出时使用该方法，可以保证所有文件写入后，socket 都已关闭，等等。

## 自定义布局 - `log4js.addLayout(type, fn)`

此方法用于添加用户定义的布局功能。有关更多详细信息和示例，请参见[layouts](layouts.md)。
