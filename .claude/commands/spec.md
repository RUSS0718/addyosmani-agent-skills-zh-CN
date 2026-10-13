---
description: Start spec-driven development — write a structured specification before writing code
---

调用代理技能:spec-driven-development 技术。

 首先要了解用户想要构建什么。请回答澄清的问题:
1. 目标用户
2. 核心特征和接受标准
3. 技术堆栈偏好和制约
4. 已知边界 (做什么总是做,先问, 从来没有做)

 然后生成一个结构化的光谱,涵盖所有六个核心领域:目标,命令,项目结构,代码样式,测试策略,以及边界.

 如果请求捆绑了几种独立的可测试能力,则首先提出能力地图 (模块 ID、 依赖方向、 构建秩序)  每个技能的阶段0 并获得批准,然后按依赖顺序选择每个模块。

 在工程根中将光谱保存为SPEC.md,并在进行前与用户确认.
