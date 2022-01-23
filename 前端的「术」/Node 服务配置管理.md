# Node 服务配置管理

## 背景

任何服务的两大硬需:

- 配置管理；
- 日志和监控；

如何理解配置呢？
配置，本质也是数据，一种仅供开发使用的数据，比如数据库 URI、环境等，用于在不改代码的情况下，调整应用的行为。

## 问题分析

以最常用的 tsconfig.json 为例：

```json
{
  "extends": "@tsconfig/node10/tsconfig.json",
  "compilerOptions": {
    // Object.fromEntries
    "lib": ["es2019.object"],
    "declaration": true,
    "composite": true,
    "emitDeclarationOnly": true,
    "isolatedModules": true,
    "importsNotUsedAsValues": "error",

    "strict": true,

    /* Additional Checks */
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,

    /* Module Resolution Options */
    "moduleResolution": "node",
    /* This needs to be false so our types are possible to consume without setting this */
    "esModuleInterop": false,
    "skipLibCheck": false,
    "resolveJsonModule": true
  },
  "exclude": [
    ".yarn/releases/*",
    "**/__mocks__/**/*",
    "**/__tests__/**/*",
    "**/__typechecks__/**/*",
    "**/build/**/*"
  ]
}
```

Ref: https://github.com/facebook/jest/blob/main/tsconfig.json

分析下配置的内容，首先从类型上看：

- 有简单数据类型，比如字符串 "@tsconfig/node10/tsconfig.json"；
- 有复杂数据类型，比如对象。
内容上，就和具体的应用有关了。

从配置的来源方式看：
- tsconfig.json 使用文件配置，一般 git 管理；
- 环境变量，尤其敏感配置，比如数据库密码，不能放在代码仓库中。

配置的生效：程序一般只在启动运行时加载一次，也就意味着，变更要生效，程序必须重启。

配置一般对应用行为影响重大，动态加载配置是玩火行为。配置的变更必须像代码一样，走完整的 CR、发布流程。

经过以上分析，确认对配置管理的功能需求：

1. 支持简单数据类型和复杂数据类型；
2. 支持从环境变量加载配置；

## 方案设计

1. 约定 NODE_ENV 枚举应用的环境：dev、test、production;
2. 环境变量的配置优先级高于文件配置；
3. 环境变量的配置

## 实现

## 总结

### 技术要点
