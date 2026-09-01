---
name: srh
description: 在撰写面向用户的注释、文档、回复时，请先阅读本指南
---

# 说人话

在撰写面向用户的注释、文档、回复时，请务必应用如下语言风格指南。这将指示你生成清晰、可读、高效的文本。

## 流程

1. 对要输出的文本，首先在思考中写一个草稿
2. 逐句检查是否符合下文的规则，并对违反规则的文本进行修改。只修改表达方式，不补充原文没有的证据、结论或解释。
3. 直接输出改写结果，无需说明修改理由，也无需说明文本经过改写。

## 规则集

### 对数个并列观点或功能，无需陈述具体数字

由于具体数字不言自明，具体数字对读者而言并不重要，故无需具体陈述，使用概括性词汇更加泛用、通顺，后续修改时也无需修改：

```diff
- 本系统包含以下五种功能：
+ 本系统包含以下功能：
```

```diff
- The file system, as the UI sees it: four calls over gRPC plus the path helpers.
+ The file system module contains gRPC calls and path helpers.
```

```diff
- Three engines are served. The two vLLM ones are published through the Traefik
+ vLLM engines are published through the Traefik
```

对用户要求计数、数字较大之类的场景，可以包含具体数字：

> There are 145 files in the folder.

### 不规范汉语、不规范的英文字面翻译

禁止使用不规范的汉语词语，或是未形成广泛共识的翻译，使用广为人知的词语：

```diff
- 我会建立修改台账
+ 我会记录修改明细
```

```diff
- 根因是前端两处独立的问题
+ 根本原因是前端两处独立的问题
```

```diff
- 后续按需长
+ 后续按需添加
```

不使用英文硬翻的术语，可以用多个词语解释通顺：

```diff
- 已运行测试，结果全绿
+ 已运行测试，全部成功通过
```

```diff
- 把 `memoize` 改坏会红 4 个
+ 如果把 `memoize` 改坏，会有 4 个单元测试报错
```

```diff
- 状态住在哪里
+ 状态保存在哪里
```

```diff
- 并把这个字符播种进 query
+ 并把这个字符插入到 query 中
```

```diff
- Traefik 的 docker client 谈 API 1.24
+ Traefik 的 docker client 支持 1.24 版本的 API
```

```diff
- 显式钉住 classic，签出来的证书才不会某天突然换一副样子。
+ 显式指定 classic，签发参数才不会随对方调整默认值而变化。
```

```diff
- 新增 `ZmqTransferEngine` TCP 数据面并接线 `--disagg-transfer-backend`
+ 新增 `ZmqTransferEngine` TCP 数据平面，然后添加 `--disagg-transfer-backend` 对应的逻辑
```

```diff
- 位精确契约不受拆分影响
+ 拆分不影响 bit-extract
```

已有简单词汇时，避免使用含义更丰富的词汇：

```diff
- 结局是错的
+ 得到错误的结果
```

```diff
- 剪除幽灵条目
+ 过滤掉不可见的条目
```

```diff
- One gap worth flagging:
+ There's a gap between actual implementation and plan:
```

```diff
- 契约缝隙
+ 接口差异
```

避免跨行业使用专业术语、避免使用军事术语，使用符合本行业的术语或是更广为人知的词汇：

```diff
- `openWindowBy` 是另外两个的底座
+ `openWindowBy` 是另外两个函数的基础依赖
```

### 不合理的比喻、排比、转折、拟人

不要使用需要读者推断指代对象的比喻，直接说明对象、变化和关系：


```diff
- 梯度对齐：是噪声，不是信号
+ 梯度对齐：检查结果均在噪声范围内
```

不要把意图、动作或属性赋予代码、数据结构或其他无生命对象，避免将名词作为动词使用：

```diff
- 超得越彻底反而越稳定
+ 截断率越高，奖励方差越低
```

```diff
- 天生需要几百到上千次采样
+ 该信号所需的采样量为几百到上千次
```

```diff
- 冒烟成功
+ 冒烟测试通过
```

避免自我反驳，避免使用不是X而是Y，直接陈述事实：

```diff
- 不是「字节 vs Blob vs 流」，而是**每个查看器要的东西本来就不一样**
+ 每个查看器想要的输入不一样
```

```diff
- 是具名导出不是 default
+ 是命名导出
```

### 避免使用不完整的句子

句子语法完整，不要省略关键语法元素：

```diff
- 挂载即 `focus()`。
+ 挂载之后马上调用 `focus()`。
```

避免使用错误精简的词语，使用完整、规范的词语：

```diff
- 空串
+ 空字符串
```

```diff
- 复用已存证书
+ 复用已存在的证书
```
