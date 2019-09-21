# 3.x 版本中的变化

log4js 不再支持低于 6 的 nodejs 版本。

以下 appender 已从核心中删除，并移动到它们自己的项目中：

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

如果您使用它们，则需要 `npm i @log4js-node/<appender>`.

删除可选的 appender 将删除所有安全漏洞。

引入 TCP [client](tcp.md)/[server](tcp-server.md)来代替多进程 appender。

[在 3.0.0 中解决的问题](https://github.com/log4js-node/log4js-node/milestone/31?closed=1)

[代码更改的 PR](https://github.com/log4js-node/log4js-node/pull/754)
