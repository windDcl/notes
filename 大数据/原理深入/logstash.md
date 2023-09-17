## 常见的部署模式
Elastic Stack？
filebeats在上千台机器上收集日志将其发送给logstash。logstash统一处理发送给下游
logstash提供了持久化缓存队列

## 命令行操作
参考： https://www.elastic.co/guide/en/logstash/8.9/running-logstash-command-line.html#command-line-flags

**`--node.name NAME`**

指定此 Logstash 实例的名称。如果没有给出任何值，它将默认为当前主机名。

**`-f, --path.config CONFIG_PATH`**

从特定文件或目录加载 Logstash 配置。如果给定一个目录，则该目录中的所有文件将按字典顺序连接，然后解析为单个配置文件。不支持多次指定此标志。如果多次指定此标志，Logstash 将使用最后一次出现的位置（例如，`-f foo -f bar` 与 相同`-f bar`）。

您可以指定通配符 ( [glob](https://www.elastic.co/guide/en/logstash/8.9/glob-support.html "全局模式支持") )，任何匹配的文件都将按照上述顺序加载。例如，您可以使用通配符功能按名称加载特定文件：

bin / logstash -- debug - f '/tmp/{一，二，三}'

使用此命令，Logstash 连接三个配置文件 、`/tmp/one`、`/tmp/two`和 `/tmp/three`，并将它们解析为单个配置。

**`-e, --config.string CONFIG_STRING`**

使用给定的字符串作为配置数据。与配置文件的语法相同。如果未指定输入，则使用以下内容作为默认输入： `input { stdin { type => stdin } }`如果未指定输出，则使用以下内容作为默认输出：`output { stdout { codec => rubydebug } }`。如果您想使用这两个默认值，请使用空字符串作为标志`-e`。默认为零。

**`--plugin-classloaders`**

（测试版）在独立的类加载器中加载 Java 插件以隔离它们的依赖关系。

**`--modules`**

启动指定的模块。`-M`与为指定模块的默认变量赋值的选项结合使用。如果`--modules`在命令行上使用，则其中的任何模块`logstash.yml`以及其中的任何设置都将被忽略。`-f`该标志与和标志互斥`-e`。只能指定`-f`、`-e`、 或之一。`--modules`可以通过用逗号分隔或`--modules`多次调用该标志来指定多个模块。

**`-M, --modules.variable`**

为模块的可配置选项分配一个值。分配变量的格式适用 `-M "MODULE_NAME.var.PLUGIN_TYPE.PLUGIN_NAME.KEY_NAME=value"`于 Logstash 变量。对于其他设置，它将是`-M "MODULE_NAME.KEY_NAME.SUB_KEYNAME=value"`. 该`-M`标志可以根据需要多次使用。如果未`-M`指定选项，则将使用该设置的默认值。该`-M`标志仅与标志一起使用`--modules` 。`--modules`如果该标志不存在， 它将被忽略。

**`--pipeline.id ID`**

设置管道ID。默认为`main`.

**`-w, --pipeline.workers COUNT`**

设置要运行的管道工作人员的数量。此选项设置将并行执行管道的过滤器和输出阶段的工作人员数量。如果您发现事件正在备份，或者 CPU 未饱和，请考虑增加此数字以更好地利用机器处理能力。默认为主机CPU核心数。

**`--pipeline.ordered ORDERED`**

保留事件顺序。可能的值为`auto`（默认）`true`和`false`。仅当管道也使用单个工作人员时，此设置才有效。请注意，启用后，它可能会影响过滤器和输出处理的性能。如果设置设为，该`auto`选项将自动启用排序 。用于在管道上启用排序，并在有多个工作线程时阻止 Logstash 启动。用于禁用保留排序所需的任何额外处理。 `pipeline.workers``1``true``false`

**`-b, --pipeline.batch.size SIZE`**

管道要处理的批次大小。此选项定义单个工作线程在尝试执行其过滤器和输出之前将从输入收集的最大事件数。默认值为 125 个事件。较大的批处理大小通常更高效，但代价是增加内存开销。您可能需要在`jvm.options`配置文件中增加 JVM 堆空间。有关详细信息， 请参阅[Logstash 配置文件。](https://www.elastic.co/guide/en/logstash/8.9/config-setting-files.html "Logstash 配置文件")

**`-u, --pipeline.batch.delay DELAY_IN_MS`**

创建管道批次时，轮询下一个事件时等待的时间。此选项定义在将尺寸不足的批次分派到过滤器和输出之前轮询下一个事件时等待的时间（以毫秒为单位）。默认值为 50 毫秒。

**`--pipeline.ecs_compatibility MODE`**

设置 ECS 兼容模式的进程默认值。可以是 ECS 版本，例如`v1`或`v8`、 或`disabled`。默认为`v8`. Logstash 8 之前定义的管道在运行时没有考虑 ECS。`pipeline.ecs_compatibility: disabled`为了确保迁移的管道继续像在旧版本的 Logstash 中一样运行，请通过在其定义中设置来选择退出单个管道的 ECS `pipelines.yml`。使用命令行标志将为_所有_管道（包括新管道）设置默认值。有关更多信息， 请参阅[ECS 兼容性。](https://www.elastic.co/guide/en/logstash/8.9/ecs-ls.html#ecs-compatibility "云服务器兼容性")

**`--pipeline.unsafe_shutdown`**

即使内存中仍有正在进行的事件，也会强制 Logstash 在关闭期间退出。默认情况下，Logstash 将拒绝退出，直到所有接收到的事件都已推送到输出。启用此选项可能会导致关机期间数据丢失。

**`--path.data PATH`**

这应该指向一个可写目录。Logstash 每当需要存储数据时就会使用该目录。插件也可以访问此路径。默认是`data`Logstash home下的目录。

**`-p, --path.plugins PATH`**

查找自定义插件的路径。该标志可以多次给出以包含多个路径。插件应位于特定的目录层次结构中： 其中、`PATH/logstash/TYPE/NAME.rb`、或是插件的名称。 `TYPE``inputs``filters``outputs``codecs``NAME`

**`-l, --path.logs PATH`**

Logstash 内部日志写入的目录。

**`--log.level LEVEL`**

设置 Logstash 的日志级别。可能的值为：

- `fatal`：记录非常严重的错误消息，通常会导致应用程序中止
- `error`: 记录错误
- `warn`：记录警告
- `info`：记录详细信息（这是默认值）
- `debug`：记录调试信息（供开发人员使用）
- `trace`：记录除调试信息之外的更细粒度的消息

**`--config.debug`**

将完全编译的配置显示为调试日志消息（您还必须已`--log.level=debug`启用）。警告：日志消息将包括以明文形式传递给插件配置的任何_密码_选项，并可能导致明文密码出现在您的日志中！

**`-i, --interactive SHELL`**

落入 shell 而不是正常运行。有效的 shell 是“irb”和“pry”。

**`-V, --version`**

发出 Logstash 及其朋友的版本，然后退出。

**`-t, --config.test_and_exit`**

检查配置的语法是否有效，然后退出。请注意，不会使用此标志检查 grok 模式的正确性。Logstash 可以从一个目录读取多个配置文件。如果将此标志与 结合使用`--log.level=debug`，Logstash 将记录组合​​的配置文件，并使用其来源文件来注释每个配置块。

**`-r, --config.reload.automatic`**

监视配置更改并在配置更改时重新加载。注意：使用 SIGHUP 手动重新加载配置。默认为 false。

**`--config.reload.interval RELOAD_INTERVAL`**

轮询配置位置是否发生更改的频率。默认值为“3s”。请注意，单位限定符 ( `s`) 是必需的。

**`--api.enabled ENABLED`**

默认情况下启用 HTTP API，但可以通过传递`false`到此选项来禁用。

**`--api.http.host HTTP_HOST`**

Web API 绑定主机。此选项指定指标 REST 端点的绑定地址。默认值为“127.0.0.1”。

**`--api.http.port HTTP_PORT`**

Web API http 端口。此选项指定指标 REST 端点的绑定端口。默认值为 9600-9700。此设置接受格式为 9600-9700 的范围。Logstash 将选择第一个可用端口。

**`--log.format FORMAT`**

指定 Logstash 是否应以 JSON 形式（每行一个事件）或纯文本（使用 Ruby 的 Object#inspect）写入自己的日志。默认为“普通”。

**`--path.settings SETTINGS_DIR`**

`logstash.yml` [设置包含设置文件](https://www.elastic.co/guide/en/logstash/8.9/logstash-settings-file.html "Logstash.yml")以及 log4j 日志记录配置的 目录。这也可以通过 LS_SETTINGS_DIR 环境变量进行设置。默认是`config`Logstash home下的目录。

**`--enable-local-plugin-development`**

此标志使开发人员能够更新其本地 Gemfile，而不会遇到由冻结的锁定文件引起的问题。当您在本地开发/测试插件时，此标志会很有帮助。

## 基本使用部署


## 监控API

### 节点状态api
The node stats API retrieves runtime stats about Logstash.

`curl -XGET 'localhost:9600/_node/stats/<types>'`

Where `<types>` is optional and specifies the types of stats you want to return.

默认情况下，会返回所有的统计信息。您可以通过将以下类型组合在逗号分隔的列表中，来限制返回的信息：

```


jvm

获取JVM统计信息，包括线程、内存使用、垃圾收集器和运行时间的统计信息。

process

获取进程统计信息，包括文件描述符、内存消耗和CPU使用率的统计信息。

events

获取Logstash实例的事件相关统计信息（无论创建和销毁了多少个管道）。

flow

获取Logstash实例的流程相关统计信息（无论创建和销毁了多少个管道）。

pipelines

获取每个Logstash管道的运行时统计信息。

reloads

获取配置重新加载成功和失败的运行时统计信息。

os

获取在Logstash在容器中运行时，关于cgroups的运行时统计信息。

geoip_download_manager

获取与Geoip过滤器插件一起使用的数据库的统计信息。

请参阅"Common Options"以查看可应用于所有Logstash监控API的选项列表。
```
