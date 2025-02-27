# 虚拟服务参数配置

本页介绍创建和编辑**虚拟服务**时的参数配置。

## 基本配置

| UI 元素  |      | YAML 字段          | 描述                                                         |
| -------- | ---- | ------------------ | ------------------------------------------------------------ |
| 名称     | y    | metadata.name      | 必填。虚拟服务名称。<br />格式要求：小写字母、数字和中划线（-）组成，必须以小写字母开头，以小写字母或数字结尾，最长 63 个字符。 |
| 命名空间 | y    | metadata.namespace | 必填。虚拟服务所属命名空间。  同一个命名空间内，请求身份认证不可重名。 |
| 应用范围 | y    | spec.gateways      | 必填。虚拟服务应用的范围，包含两类：<br />- 指定网关规则（可添加多个），可用于对外暴露网格内部服务；<br />- 对所有边车（-mesh）生效。 |
| 所属服务 | y    | spec.hosts         | 必填。应用虚拟服务的服务对象，可包含三类：<br />- 来自 Kubernetes 注册中心的注册服务；<br />- 来自服务条目的注册服务；<br />- 服务域名。 |

## 路由配置

可添加多条路由规则，执行顺序由上至下，排名在前的路由规则优先生效。

### HTTP 路由

多条 HTTP 路由规则之间可以拖动排序，每条路由可收起，仅显示路由名称。

#### 基本信息

| UI 元素  | YAML 字段       | 描述                                                         |
| -------- | --------------- | ------------------------------------------------------------ |
| 路由名称 | spec.http.-name | 必填。http 路由名称。<br />格式要求：小写字母、数字和中划线（-）组成，必须以小写字母开头，以小写字母或数字结尾，最长 63 个字符。 |

#### 路由匹配规则

必填。YAML 字段为 spec.http.-name.match。

通过 URI 路径、端口等方式对请求做匹配，可添加多条规则，执行顺序由上至下，优先使用排名在前的匹配规则。

| UI 元素    | YAML 字段                    | 描述                                                         |
| ---------- | ---------------------------- | ------------------------------------------------------------ |
| 匹配URI    | spec.http.-name.match.uri    | 可选。对请求的URI路径做匹配，有三种匹配方式：<br />- 精确（exact）：字段完全匹配<br />- 前缀（prefix）：对字段前缀的匹配<br />- 正则（regex）：基于RE2样式正则表达式的匹配 |
| 匹配端口   | spec.http.-name.match.port   | 可选。对请求的端口做匹配。                                   |
| 匹配header | spec.http.-name.match.header | 必填。对请求的 http header 做匹配，同样支持三种匹配方式：<br />- 精确（exact）：字段完全匹配<br />- 前缀（prefix）：对字段前缀的匹配<br />- 正则（regex）：基于RE2样式正则表达式的匹配 |

#### 路由目标/重定向规则

`路由目标`功能和`重定向`功能为互斥功能，一条 `HTTP 路由规则`内仅可以二选一。

> 注意：当用户开启`代理`开关后，该项及相关内容置灰。

**路由规则**

| UI 元素  | YAML 字段                                      | 描述                                                         |
| -------- | ---------------------------------------------- | ------------------------------------------------------------ |
| 路由目标 | spec.http.-name.route.-destination             | 可选。已匹配请求的路由目标，可添加多条，优先执行排名在前的路由目标。 |
| 服务名称 | spec.http.-name.route.-destination.host        | 必填。路由目标服务的名称或 IP。                              |
| 版本服务 | spec.http.-name.route.-destination.subset      | 可选。列表内容来自所选服务的可用`目标规则`。                 |
| 权重     | spec.http.-name.route.weight                   | 可选。本条`路由目标`内各条所占流量的分配权重。所有`路由目标`的权重总和应为100。 |
| 端口     | spec.http.-name.route.-destination.port.number | 可选。路由目标服务的端口。                                   |

**重定向**

| UI 元素    | YAML 字段                             | 描述                                                         |
| ---------- | ------------------------------------- | ------------------------------------------------------------ |
| 重定向     | spec.http.-name.redirect              | 可选。重定向用于将请求转发至其他路径。                       |
| 重定向路径 | spec.http.-name.redirect.uri          | 必填。新的访问地址路径（URI）。                              |
| authority  | spec.http.-name.redirect.authority    | 可选。URI 路径中认证信息部分，通常为//开始，/结束。          |
| 端口       | spec.http.-name.redirect.port.number  | 可选。重定向服务的端口号。                                   |
| 响应码     | spec.http.-name.redirect.redirectCode | 可选。指定响应码，当返回指定错误码时，执行重定向操作，默认不填为 301。 |

#### 可选设置

另外还提供了 6 个可选设置，您可以根据实际需求启用或禁用。

**重写**

| UI 元素   | YAML 字段                          | 描述                                                         |
| --------- | ---------------------------------- | ------------------------------------------------------------ |
| 重写      | spec.http.-name.rewrite            | 可选。默认关闭  l 可以重现完整路径，也可以仅重写 http 前缀。 |
| 重写路径  | spec.http.-name.rewrite.uri        | 必填。新的访问地址路径（URI）。                              |
| authority | spec.http.-name.redirect.authority | 可选。URI 路径中认证信息部分，通常为//开始，/结束。          |

**超时**

| UI 元素  | YAML 字段               | 描述                                                         |
| -------- | ----------------------- | ------------------------------------------------------------ |
| 超时     | spec.http.-name.timeout | 可选。默认关闭。超时功能用于定义向目标服务发起请求的等待时长。 |
| 超时时长 | spec.http.-name.timeout | 必填。可容忍的超时时长。输入格式：数字 + 单位（s，m，h，ms）。 |

**重试**

| UI 元素  | YAML 字段                             | 描述                                                         |
| -------- | ------------------------------------- | ------------------------------------------------------------ |
| 重试     | spec.http.-name.retries               | 可选。默认关闭。重试功能用于定义当请求反馈为异常时再次发起请求的尝试次数。 |
| 重试次数 | spec.http.-name.retries.attempts      | 可选。一个反馈异常的请求的可重试次数，重试间隔默认为 25ms。<br />当重试和超时功能均开启时，重试次数和重试超时的乘积与超时时长取最短者生效，因此实际的重试次数可能会小于设置值。 |
| 重试超时 | spec.http.-name.retries.perTryTimeout | 可选。每次重试的超时时长，默认与超时功能的设置（http.route.timeout）相同。输入格式：数字 + 单位（s，m，h，ms）。 |
| 重试条件 | spec.http.-name.retries.retryOn       | 可选。允许重试的前提条件，该项下包含多个复选内容：<br />- 5xx：当上游服务器返回5xx响应码或无响应（断开/重置/读取超时），envoy 将重试。<br />- refused-stream：当上游服务器用错误码 REFUSED_STREAM 重置数据流，envoy 将重试。<br />- gateway-error：仅针对 502、503、504 错误进行重试。<br />- retriable-status-codes：当上游服务器的返回码与响应码或请求头中 x-envoy-retriable-status-codes 定义相同，则重试。<br />- reset：当上游服务器无响应时（断开/重置/读取超时），将重试。<br />- connect-failure：当与上游服务器的连接失败（连接超时等）导致请求失败，将重试。<br />- retriable-headers：当上游服务器响应码与重试策略中定义匹配或与 x-ENVIGET-retriable-header-NAME 头匹配，将尝试重试。<br />- envoy-ratelimited：当存在报头 x-ENVISENT-ratelimited 时，将重试。<br />- retriable-4xx：当上游服务器返回 4xx 响应码时（目前仅有 409），envoy 将重试。 |

**故障注入**

| UI 元素      | YAML 字段                              | 描述                                                         |
| ------------ | -------------------------------------- | ------------------------------------------------------------ |
| 故障注入     | spec.http.-name.fault                  | 可选。默认关闭。<br />故障注入功能用于在应用层向目标服务注入故障，并可提供“延迟”和“终止”两种故障类型；使用故障注入时，不能启用超时和重试功能； |
| 延迟时长     | spec.http.-name.fault.delay.fixedDelay | 必填。请求响应可延迟的时长   输入格式：数字 + 单位（s，m，h，ms）。 |
| 故障注入占比 | spec.http.-name.fault.delay.percentage | 可选。所有请求中故障注入比率，默认 100%。                    |
| 终止响应码   | spec.http.-name.fault.abort.httpStatus | 必填。用于终止当前请求的 http 返回码。                       |
| 故障注入占比 | spec.http.-name.fault.abort.percentage | 可选。所有请求中故障注入比率，默认 100%。                    |

**代理虚拟服务**

YAML 字段为 spec.http.-name.delegate，默认关闭。

- 虚拟服务代理功能可以将路由配置拆分至主从两个虚拟服务中，由主虚拟服务完成基本设置和匹配规则，由代理虚拟服务完成具体路由规则。

- 开启代理功能后，仅主虚拟服务的路由匹配生效，代理虚拟服务的路由匹配规则不需设置。

- 代理虚拟服务的路由规则会和主虚拟服务路由规则合并执行。

- 代理功能不支持嵌套，仅主虚拟服务可以开启代理功能。

- 当虚拟服务有“路由目标/重定向”配置时，不可配置代理。

| UI 元素      | YAML 字段                          | 描述                                                         |
| ------------ | ---------------------------------- | ------------------------------------------------------------ |
| 代理虚拟服务 | spec.http.-name.delegate.name      | 必填。用于代理的从虚拟服务  <br />注意：<br />一个已配置代理项的虚拟服务不可做为代理，即不可嵌套代理；<br />一个配置了spec.hosts字段的虚拟服务不可作为代理。 |
| 所属命名空间 | spec.http.-name.delegate.namespace | 可选。从虚拟服务所属命名空间，默认与主虚拟服务相同。         |

**流量镜像**

| UI 元素      |      | YAML 字段                                     | 描述。                                                   |
| ------------ | ---- | --------------------------------------------- | -------------------------------------------------------- |
| 流量镜像     | n    | spec.http.-name.mirror                        | 可选。默认关闭。用于将请求流量复制至其他目标服务。       |
| 镜像至服务   | y    | spec.http.-name.mirror.host                   | 必填。流量镜像的传输目标服务。                           |
| 流量镜像占比 | n    | spec.http.-name.mirror.mirrorPercentage:value | 可选。复制的请求流量与原请求流量的比率，默认100%。       |
| 服务版本     | n    | spec.http.-name.mirror.subset                 | 可选。服务版本列表内容来自当前目标服务的可用“目标规则”。 |

### TLS 路由

- 可添加多条，执行顺序由上至下，排名在前的路由规则优先生效

- 多条 TLS 路由规则之间可以拖动排序

- 每条路由可收起，仅显示路由名称

#### 路由匹配规则

YAML 字段为 spec.tls.-name.match。

通过端口（-port）和 SNI（-port.sniHosts）名称方式对请求做匹配，可添加多条规则，执行顺序由上至下，优先使用排名在前的匹配规则。

#### 路由目标

| UI 元素      | YAML 字段                                     | 描述                                                         |
| ------------ | --------------------------------------------- | ------------------------------------------------------------ |
| 添加路由目标 | spec.tls.-name.route                          | 必填。添加路由目标信息，可添加多条，执行顺序由上至下         |
| 服务名称     | spec.tls.-name.route.-destination.host        | 必填。目标服务名称，下拉列表包含当前命名空间下启用tls协议的所有服务 |
| 端口         | spec.tls.-name.route.-destination.port.number | 可选。目标服务端口                                           |
| 服务版本     | spec.tls.-name.route.-destination.subset      | 可选。服务版本列表内容来自当前目标服务的可用“目标规则”。     |
| 权重         | spec.tls.-name.route.-destination.weight      | 可选。本条“tls路由”规则内各个“路由目标”所占流量的分配权重，各条规则的权重总和应为100。 |

### TCP 路由

- 可添加多条，执行顺序由上至下，排名在前的路由规则优先生效

- 多条 TCP 路由规则之间可以拖动排序

- 每条路由可收起，仅显示路由名称

#### 路由匹配规则

| UI 元素          | YAML 字段                  | 描述                                                         |
| ---------------- | -------------------------- | ------------------------------------------------------------ |
| 添加路由匹配规则 | spec.tcp.-name.match       | 可选。通过端口（-port）方式对请求做匹配，可添加多条规则，执行顺序由上至下，优先使用排名在前的匹配规则。 |
| 端口             | spec.tcp.-name.match.-port | 必填。tcp 端口号。                                           |

#### 路由目标

| UI 元素      |      | YAML 字段                                     | 描述                                                         |
| ------------ | ---- | --------------------------------------------- | ------------------------------------------------------------ |
| 添加路由目标 | Y    | spec.tcp.-name.route                          | 必填。添加路由目标信息，可添加多条，执行顺序由上至下。       |
| 服务名称     | Y    | spec.tcp.-name.route.-destination.host        | 必填。目标服务名称，下拉列表包含当前命名空间下可用 tcp 协议的所有服务。 |
| 端口         | N    | spec.tcp.-name.route.-destination.port.number | 可选。目标服务端口。                                         |
| 服务版本     | n    | spec.tcp.-name.route.-destination.subset      | 可选。服务版本列表内容来自当前目标服务的可用“目标规则”。     |
| 权重         | N    | spec.tcp.-name.route.-destination.weight      | 可选。本条“TCP路由”规则内各个“路由目标”所占流量的分配权重，各条规则的权重总和应为100。 |
