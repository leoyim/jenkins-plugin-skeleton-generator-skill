---
description: 根据用户提供的Jenkins版本和JDK版本生成Jenkins插件骨架。
name: jenkins-plugin-skeleton-generator-skill
activation:
  - 用户请求创建/生成/搭建/开发 Jenkins 插件
---

# Jenkins插件骨架生成器技能

## 目的

生成与用户环境匹配的Jenkins插件基础结构。

**重要规则**：本技能**不得**假定默认的Jenkins版本或JDK版本。在用户确认版本之前，**请勿生成任何文件**。

---

## 目录结构

```
SKILL.md                          # 本文件：工作流指令
templates/                         # 代码与配置模板
  ├── pom.md                        # Maven POM 模板
  ├── repository-config.md         # Maven 仓库配置片段
  ├── builder.md                   # Builder 扩展点模板
  ├── run-listener.md              # RunListener 扩展点模板
  ├── async-periodic-work.md       # 异步任务模板
  ├── plugin-configuration.md      # 持久化配置模板
  ├── config-jelly.md              # Jelly 配置界面模板
  ├── readme-template.md           # 项目 README 模板
  └── project-structure.md         # 项目目录结构
references/                        # 参考文档
  ├── plugin-types.md             # 插件类型 / 扩展点对照表
  ├── trigger-timing-reference.md # 触发时机参考表
  └── compatibility-check.md      # 兼容性检查清单
```

---

## 执行前所需的输入信息

请向用户询问以下信息：

1. **Jenkins版本**（例如：2.401.1）
2. **JDK版本**（例如：11、17、21）
3. **Maven版本**（可选）
4. **插件元数据**：
   - 插件名称（Display Name）
   - groupId
   - artifactId
   - 包名（package name）

5. **插件类型 / 扩展点**（明确选择）：
   - [ ] Builder（构建步骤）
   - [ ] Publisher（构建后发布者）
   - [ ] Trigger / SCM（触发器）
   - [ ] Action（界面操作）
   - [ ] Listener（系统事件监听器）
   - [ ] QueueTaskDispatcher（队列任务分发）
   - [ ] ComputerListener（节点监听器）
   - [ ] 其他：_______

6. **触发时机**（与扩展点对应）：
   - 构建执行阶段触发
   - 构建完成后触发
   - 定时/轮询触发
   - 界面交互触发
   - 系统事件触发
   - 节点状态变更触发

7. **是否需要异步执行**：□ 是 □ 否
8. **是否需要持久化配置**：□ 是 □ 否
9. **是否需要全局配置页面**：□ 是 □ 否

---

## 工作流程

### 第一步：环境信息收集与分析

收集用户提供的所有信息后，进行兼容性分析：

- Jenkins API兼容性检查
- Java语言级别确认
- Jenkins Plugin Parent版本选择
- 依赖版本确定

> 详细兼容性检查项目见 `references/compatibility-check.md`

### 第二步：触发时机与扩展点设计

根据用户选择的插件类型，参考扩展点对照表确定代码模板。

> 完整扩展点与触发时机对照见 `references/plugin-types.md` 和 `references/trigger-timing-reference.md`

### 第三步：生成项目结构

按照 Maven HPI 项目标准生成目录结构。

> 标准目录结构见 `templates/project-structure.md`

### 第四步：README 生成

根据用户提供的插件元数据，生成 `README.md` 文件。模板 `templates/readme-template.md` 使用 `{{PLACEHOLDER}}` 占位符，生成时替换为实际值：

| 占位符 | 来源 |
|--------|------|
| `{{PLUGIN_DISPLAY_NAME}}` | 用户提供的插件名称 |
| `{{PLUGIN_DESCRIPTION}}` | 根据插件类型自动生成一段简要描述 |
| `{{JENKINS_VERSION}}` | 用户提供的 Jenkins 版本 |
| `{{JDK_VERSION}}` | 用户提供的 JDK 版本 |
| `{{MAVEN_VERSION}}` | 用户提供的 Maven 版本（未提供则填"3.6+"） |
| `{{EXTENSION_TYPE}}` | 用户选择的插件类型 |
| `{{TRIGGER_TIMING}}` | 用户选择的触发时机 |
| `{{ARTIFACT_ID}}` | 用户提供的 artifactId |
| `{{PACKAGE_PATH}}` | 包名对应的路径（如 `com/example/plugin`） |
| `{{MAIN_CLASS}}` | 主扩展类名 |
| `{{USAGE_*}}` | 根据插件类型生成对应使用说明 |
| `{{CONFIG_ITEMS}}` | 根据是否需要持久化配置生成配置项表格 |
| `{{ASYNC_FEATURE}}` / `{{PERSISTENCE_FEATURE}}` / `{{GLOBAL_CONFIG_FEATURE}}` | 根据用户选择填充或不生成 |
| `{{LICENSE_INFO}}` | 默认 MIT License |

### 第五步：Maven配置生成

根据用户输入生成 `pom.xml`。**必须注意**：

- 生成的 `pom.xml` 默认必须同时配置 `<repositories>` 与 `<pluginRepositories>`，不应假设用户已配置 Maven `settings.xml`
- **除非用户明确指定其他仓库**，否则始终默认使用 Jenkins 官方 Repository Proxy（`https://repo.jenkins-ci.org/public/`），不应默认依赖 Maven Central、阿里云镜像或企业 Nexus

生成时参考以下模板：

- 仓库配置片段：`templates/repository-config.md`
- 完整 POM 模板：`templates/pom.md`

应避免：不支持的 API、依赖冲突、servlet 命名空间问题。

### 第六步：插件代码生成

根据用户选择的插件类型，使用对应模板生成扩展代码：

| 插件类型 | 对应模板文件 |
|---------|------------|
| Builder | `templates/builder.md` |
| RunListener | `templates/run-listener.md` |
| AsyncPeriodicWork（异步） | `templates/async-periodic-work.md` |
| 持久化配置 | `templates/plugin-configuration.md` |

### 第七步：资源文件生成

根据是否需要配置界面，生成对应的Jelly文件。

> Jelly 配置界面模板：`templates/config-jelly.md`

### 第八步：构建与测试

提供构建命令：

```bash
# 清理并打包
mvn clean package

# 本地测试运行
mvn hpi:run

# 仅打包不测试
mvn clean package -DskipTests

# 生成IDE项目文件
mvn eclipse:eclipse   # Eclipse
mvn idea:idea         # IntelliJ IDEA
```

输出位置：`target/插件名称.hpi`

### 第九步：安装与验证

安装步骤：

1. 登录Jenkins
2. 进入 **Manage Jenkins** → **Plugins** → **Available** → **Advanced**
3. 在 **Upload Plugin** 区域，选择生成的 `.hpi` 文件
4. 点击 **Upload** 上传
5. 重启Jenkins（如需要）
6. 验证插件在 **Installed** 列表中显示
7. 根据插件类型，在相应位置测试功能

验证清单：

- [ ] 插件成功上传并安装
- [ ] Jenkins日志无报错
- [ ] 配置界面正常显示
- [ ] 触发逻辑按预期执行
- [ ] 构建日志输出正确信息
- [ ] 异步任务正常运行（如适用）

---

## 输出要求

生成完整的插件项目后，提供以下内容：

1. **技术设计说明** - 插件架构、扩展点选择、触发时机说明
2. **README.md** - 完整的项目 README（功能、安装、使用、开发）
3. **目录结构** - 完整的项目文件树
4. **pom.xml** - 完整的Maven配置文件
5. **Java源代码** - 所有扩展类和配置类
6. **资源文件** - Jelly配置界面、帮助文档、国际化文件
7. **测试代码** - 单元测试和集成测试（可选）
8. **构建步骤** - 详细的构建命令
9. **安装步骤** - 插件安装和验证指南
10. **触发时机说明** - 明确说明各扩展点的触发时机
11. **故障排除** - 常见问题和解决方法

---

## 质量规则

生成的插件必须满足：

- [ ] 与用户要求的Jenkins版本匹配
- [ ] 与用户要求的JDK版本匹配
- [ ] 使用正确的扩展点类型
- [ ] 在正确的触发时机执行
- [ ] 能够成功构建（`mvn clean package`）
- [ ] 生成有效的HPI包
- [ ] README.md 包含完整的安装和使用说明
- [ ] 遵循Jenkins插件开发规范
- [ ] 包含完整的配置界面（如需要）
- [ ] 代码注释完整
- [ ] 无已知API冲突
- [ ] 无servlet命名空间问题

## 质量保证

- 与指定的 Jenkins 版本严格兼容
- 与指定的 JDK 版本严格兼容
- 构建零错误
- 遵循 Jenkins 插件开发最佳实践
- 最小化外部依赖

## 参考

- Jenkins 插件开发文档：https://www.jenkins.io/doc/developer/
- Plugin POM：https://github.com/jenkinsci/plugin-pom
- 示例插件：https://github.com/jenkinsci/hello-world-plugin/
