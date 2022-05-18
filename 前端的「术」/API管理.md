# 我们到底需要一个怎样的 API 管理平台

## 背景

在业务开发之余，我还负责维护腾讯内部一个 API 管理平台，然而作为维护者，自己确不咋用，为什么呢？

作为 API 消费者：
业务中调用的都是 RPC 接口，接口协议以 PB 提供，PB 文件到请求 SDK，mock 数据都由 cli 工具自动生成了，即便有 UI 展示接口，基本也没有看的必要。

作为 API 提供者：
开发时确有调用自测的需求，但一想到，在页面表单一一录入请求参数，path、body，辛苦构造请求后，还没法在代码中复用，不胜直接编码请求函数作为测试用例，不仅自测返回，还能封装为调用 SDK。对于接口使用者，相比 API 接口文档，直接提供拥有完善类型的 SDK，无疑更加令人愉悦。

总的来说，一没必要，二缺乏自动化。

开发自己平时用不到的工具，热情会不断衰减，以至无法持续。

这种状况迫使我思考：到底一个怎样 API 管理平台才是我需要的，抑或 API 管理平台是否一个伪需求？

抱着这个疑问，开始用下面关键字 google：

- modern api management
- how google manage api

一个理想的 API 管理平台慢慢浮出水面。

## Overview

1. 自动化才能让 API 管理平台不可或缺；
2. 语言和框架无关的 API 描述协议，让 API 的定义，一次付出，处处收益；
3. API 管理平台的核心之一是 proxy，或至少应该和网关紧密结合。

## OpenAPI Specification

选型的 guildline：跟谁开源，业内主流。

尤其在规范的选择上，规范的设计固然重要，同样重要还有围绕规范的工具链：接口定义的解析库、多语言代码生成、可视化展示、和框架的结合等。

OpenAPI 是原来的 Swagger Specification，现已捐赠给 [OpenAPI Initiative](http://openapis.org/) 进行开源和标准化，定义最新的规范为 OpenAPI 3.0。

OpenAPI 的优势直接翻一下官网的内容：

> 使用机器易处理的格式把 API 的描述规范化，为自动化工具的处理打下基础，而这，也打开了新世界的大门：
>
> - API 定义文件语法校验和 linting： 保证 API 定义符合特定版本规范，并且格式一致。
> - 数据验证：验证接口返回的数据类型正确。
> - 文档生成：从机器易读的格式自动转换为人易读的文档，并且始终保持同步。
> 代码生成：根据 API 定义文件，生成多语言的 Client 和 Server 代码，把开发从这些例行工作中解放出来 —— 数据验证、路由到 handler 的胶水代码等。
> - 可视化编辑器：降低手写接口定义文件的难度。
> - Mock：在真正接口 ready 之前，创建 fake server，返回 mock 数据。
> - 安全分析：在 API 设计阶段分析接口可能的安全漏洞。
>
> 在这之上，OpenAPI Specification 还提供了：
>
> - 一个开源的格式：每个人对规范的未来发展具有发言权！
> - 成熟的工具生态：受益于开源，OpenAPI 提供了大量的工具，可以充分释放接口定义文件的生产力。(参考：[OpenAPI.Tools.](https://openapi.tools/))
> - 接口定义格式不仅易于程序处理，还具有很强的可读性：接口定义文件仅仅是 text files，很容易使用编辑器浏览和 debug。
>
> (翻译自：[Advantages of Using OpenAPI](https://oai.github.io/Documentation/start-here.html))
>

### 总结下

OpenAPI 规范（OAS）是一种通用的、和编程语言无关的 API 描述规范。

记得曾经使用 json schema 自动生成表单吗？
现在有了仅写 json（或 yaml）自动生成接口文档的机会，而且不仅如此，这个 json 可以生成桩代码（关于桩代码：[跨端复用的实践：代码生成](https://zhuanlan.zhihu.com/p/470882562)。

这样，开发 HTTP 接口的流程就和开发 RPC 接口完全一致了：

1. 先编码接口定义文件；
2. 接口定义文件生成 Client/Server 桩代码；
3. 实现 Server 每个接口 handler 函数。

这个开发流程也叫设计优先（Design-First），推荐设计优先的 API 开发模式，如此才能充分发挥前后端并行。

下面看看如何建设一个 API 管理平台，配合这个理想的开发流。

## API 管理平台

API 管理平台的主要任务应该是管理 OpenAPI 格式的接口定义文件。
其次，管理根据接口定义文件生成的桩代码。
最后，对接测试环境网关做接口数据验证及可能的 mock。

### 实现方案

接口协议文件及桩代码统一管理：

1. 使用 [swagger-editor](https://github.com/swagger-api/swagger-editor) 编写接口协议或上传；
2. 点击保存，储存接口协议文件到 git 仓库；
3. 点击代码生成，生成代码储存到 git 仓库，如果 JS 代码，执行 npm 发包。

提供测试网关（or 接入已有）

1. 接口协议仓库 git push 解析接口，索引接口数据到数据库；
2. 测试网关：
   1. 验证已有接口符合协议；
   2. 根据接口流量生成接口协议文件，save到 git 仓库。

#### 编写接口协议文件

虽然 OpenAPI 声称协议定义人机皆友好，手动编码深层嵌套的 JSON 或 YAML 文件绝不是一件令人愉悦的事。

所以，使用 swagger-editor 编写 OpenAPI 只是可选项，更推荐借助 DSL，一门使用自己熟悉语言的 DSL 来编写，比如 [spot](https://github.com/airtasker/spot)，然后转码为 OpenAPI，最后上传到 API 管理平台。

#### 代码自动生成

为什么要代码生成呢？

正如前面提到，OpenAPI 就像 PB，类似一种语言无关的描述格式，进而可以生成各种语言的 Client/Server 代码，让我们从大量的前后端胶水代码中解放出来。

如何实现代码生成呢？

这里再次体现了开源和业内标准的价值：[OpenAPI Generator](https://openapi-generator.tech/) 是另一个基于 OpenAPI 2.0/3.x 的开源项目，可用 generator 之丰富令人惊讶：




更重要的是，OpenAPI Generator 具备完善的扩展机制。


在实际业务中，自定义 generator 总会发生，比如注册业务相关中间件等，OpenAPI Generator 为开发自定义 generator 提供了极大的便利。



### 预期的实现效果

实现上会先基于腾讯内部 git 及现有业务网关，之后把业务无关的代码开发出来。

预期的效果：

对于新服务、新接口：可以完全按照上面的开发流，接口定义文件 -> Client/Server 桩代码生成 -> 接口文档生成 -> Mock Server 启动。

对于存量服务：提供 HTTP 请求转接口协议文件选项，自动生成接口文档。

```mermaid

```

## 参考

- [Apigee API Management | Google Cloud](https://cloud.google.com/apigee)
- [OpenAPI](https://oai.github.io/Documentation/start-here.html)
- [A Guide to What’s New in OpenAPI 3.0](https://swagger.io/blog/news/whats-new-in-openapi-3-0/)
- [swagger-codegen](https://github.com/swagger-api/swagger-codegen)
- [OpenAPI Generator](https://openapi-generator.tech/)
- [OpenAPI.Tools](https://openapi.tools/)
- [Tools and Integrations](https://swagger.io/tools/open-source/open-source-integrations/)
- [spot](https://github.com/airtasker/spot)
- [Modern API Management with Kong](https://konghq.com/reports/modern-api-management-with-kong)
- [hoppscotch](https://github.com/hoppscotch/hoppscotch)
- [stoplight](https://elements-demo.stoplight.io/?_ga=2.230638170.752068091.1652754628-1873838241.1652754628#/operations/get-todos)
- [darabonba](https://github.com/aliyun/darabonba)
- [OpenAPI-GUI v3](https://mermade.github.io/openapi-gui/)
- [OpenAPI/Swagger](https://help.coding.net/docs/document/api/import/openapi.html)





















































































































