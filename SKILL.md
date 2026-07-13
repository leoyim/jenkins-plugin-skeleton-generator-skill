---
description: 根据用户提供的Jenkins版本和JDK版本生成Jenkins插件骨架。
name: jenkins-plugin-skeleton-generator
activation:
  - 用户请求创建/生成/搭建/开发 Jenkins 插件
---

# Jenkins插件骨架生成器技能

## 目的

生成与用户环境匹配的Jenkins插件基础结构。

本技能**不得**假定默认的Jenkins版本或JDK版本。

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

**在Jenkins版本和JDK版本确认之前，请勿生成任何文件。**

---

## 工作流程

### 第一步：环境信息收集与分析

收集用户提供的所有信息后，进行兼容性分析：

- Jenkins API兼容性检查
- Java语言级别确认
- Jenkins Plugin Parent版本选择
- 依赖版本确定

### 第二步：触发时机与扩展点设计

根据用户选择的插件类型，确定代码生成模板：

| 插件类型 | 核心类/接口 | 触发时机 | 主要方法 |
|---------|------------|---------|---------|
| Builder | Builder | 构建执行阶段 | `perform()` |
| Publisher | Publisher | 构建完成后 | `perform()` |
| Trigger | Trigger | 定时/轮询触发 | `run()` |
| Action | Action | 界面交互 | `doXxx()` |
| RunListener | RunListener | 构建生命周期事件 | `onStarted()`, `onCompleted()` |
| ComputerListener | ComputerListener | 节点状态变更 | `onOnline()`, `onOffline()` |
| QueueTaskDispatcher | QueueTaskDispatcher | 任务分配时 | `canTake()` |

### 第三步：生成项目结构

创建Maven HPI项目目录结构：

```
plugin-name/
├── pom.xml
├── README.md
├── .gitignore
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/plugin/   # 按包名生成
│   │   │       ├── YourPlugin.java          # 主扩展类
│   │   │       ├── YourPluginDescriptor.java # Descriptor
│   │   │       └── config/                  # 配置类（如需要）
│   │   ├── resources/
│   │   │   ├── com/example/plugin/
│   │   │   │   └── YourPlugin/             # 资源文件
│   │   │   │       ├── config.jelly        # 配置界面
│   │   │   │       └── help.html           # 帮助文档
│   │   │   └── index.jelly                 # 全局配置（如需要）
│   │   └── webapp/
│   │       └── images/
│   └── test/
│       └── java/
│           └── com/example/plugin/
│               └── YourPluginTest.java
```

### 第四步：Maven配置生成

根据用户输入生成`pom.xml`配置，其中`packaging=hpi`并使用兼容的Jenkins Plugin Parent POM。

**Maven仓库（Repository）配置要求**：

- 生成的 `pom.xml` 默认必须同时配置 `<repositories>` 与 `<pluginRepositories>`，不应假设用户已配置 Maven `settings.xml`。
- **除非用户明确指定其他仓库**，否则始终默认使用 Jenkins 官方 Repository Proxy（`https://repo.jenkins-ci.org/public/`），不应默认依赖 Maven Central（Maven 中央仓库）、阿里云镜像或企业 Nexus 来获取 Jenkins Plugin 依赖。

默认仓库配置片段：

```xml
<repositories>
    <repository>
        <id>repo.jenkins-ci.org</id>
        <url>https://repo.jenkins-ci.org/public/</url>
    </repository>
</repositories>

<pluginRepositories>
    <pluginRepository>
        <id>repo.jenkins-ci.org</id>
        <url>https://repo.jenkins-ci.org/public/</url>
    </pluginRepository>
</pluginRepositories>
```

完整`pom.xml`模板：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.jenkins-ci.plugins</groupId>
        <artifactId>plugin</artifactId>
        <version>版本根据Jenkins版本确定</version>
    </parent>
    
    <groupId>用户提供</groupId>
    <artifactId>用户提供</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>hpi</packaging>
    <name>用户提供</name>
    
    <properties>
        <jenkins.version>用户提供</jenkins.version>
        <java.level>用户提供</java.level>
    </properties>

    <repositories>
        <repository>
            <id>repo.jenkins-ci.org</id>
            <url>https://repo.jenkins-ci.org/public/</url>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>repo.jenkins-ci.org</id>
            <url>https://repo.jenkins-ci.org/public/</url>
        </pluginRepository>
    </pluginRepositories>
    
    <dependencies>
        <dependency>
            <groupId>org.jenkins-ci.main</groupId>
            <artifactId>jenkins-core</artifactId>
            <version>${jenkins.version}</version>
            <scope>provided</scope>
        </dependency>
        <!-- 根据插件类型添加其他依赖 -->
    </dependencies>
</project>
```

应避免：不支持的 API、依赖冲突、servlet 命名空间问题。

### 第五步：插件代码生成

根据用户选择的插件类型，生成对应的扩展代码模板。

#### Builder 类型模板示例：

```java
package com.example.plugin;

import hudson.Extension;
import hudson.Launcher;
import hudson.model.AbstractProject;
import hudson.model.Run;
import hudson.model.TaskListener;
import hudson.tasks.BuildStepDescriptor;
import hudson.tasks.Builder;
import jenkins.tasks.SimpleBuildStep;
import org.kohsuke.stapler.DataBoundConstructor;
import org.kohsuke.stapler.DataBoundSetter;

import javax.annotation.Nonnull;
import java.io.Serializable;

public class YourPluginBuilder extends Builder implements SimpleBuildStep, Serializable {

    private final String message;
    private boolean useTimestamp;

    @DataBoundConstructor
    public YourPluginBuilder(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    @DataBoundSetter
    public void setUseTimestamp(boolean useTimestamp) {
        this.useTimestamp = useTimestamp;
    }

    public boolean isUseTimestamp() {
        return useTimestamp;
    }

    @Override
    public void perform(@Nonnull Run<?, ?> run, @Nonnull FilePath workspace, 
                        @Nonnull Launcher launcher, @Nonnull TaskListener listener) 
                        throws InterruptedException, IOException {
        // 构建逻辑 - 在构建执行阶段触发
        listener.getLogger().println("Executing plugin: " + message);
        if (useTimestamp) {
            listener.getLogger().println("Timestamp: " + System.currentTimeMillis());
        }
    }

    @Extension
    public static final class DescriptorImpl extends BuildStepDescriptor<Builder> {

        @Override
        public boolean isApplicable(Class<? extends AbstractProject> jobType) {
            return true;
        }

        @Override
        @Nonnull
        public String getDisplayName() {
            return "用户提供的插件名称";
        }
    }
}
```

#### RunListener 类型模板示例：

```java
package com.example.plugin;

import hudson.Extension;
import hudson.model.Run;
import hudson.model.TaskListener;
import hudson.model.listeners.RunListener;

import javax.annotation.Nonnull;

@Extension
public class YourPluginListener extends RunListener<Run<?, ?>> {

    @Override
    public void onStarted(Run<?, ?> run, TaskListener listener) {
        // 构建开始时触发
        listener.getLogger().println("Build started: " + run.getFullDisplayName());
    }

    @Override
    public void onCompleted(Run<?, ?> run, @Nonnull TaskListener listener) {
        // 构建完成时触发
        listener.getLogger().println("Build completed: " + run.getResult());
    }
}
```

### 第六步：资源文件生成

根据是否需要配置界面，生成对应的Jelly文件。

#### config.jelly（构建步骤配置界面）：

```xml
<?jelly escape-by-default='true'?>
<j:jelly xmlns:j="jelly:core" xmlns:f="/lib/form">
    <f:entry title="消息" field="message">
        <f:textbox default="Hello Jenkins"/>
    </f:entry>
    <f:entry title="包含时间戳" field="useTimestamp">
        <f:checkbox default="false"/>
    </f:entry>
</j:jelly>
```

### 第七步：构建与测试

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

### 第八步：安装与验证

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

## 兼容性检查清单

根据用户提供的配置进行验证：

### Jenkins版本兼容性
- [ ] Jenkins核心API是否可用
- [ ] Plugin Parent版本是否匹配
- [ ] 依赖版本是否与Jenkins版本兼容

### JDK版本兼容性
- [ ] Java语言级别是否正确设置
- [ ] API是否使用正确的Java版本特性
- [ ] 编译配置是否匹配JDK版本

### Maven配置检查
- [ ] pom.xml语法正确
- [ ] 依赖版本无冲突
- [ ] HPI插件配置正确
- [ ] 仓库配置指向 Jenkins 官方 Repository Proxy（默认）

---

## 输出要求

生成完整的插件项目后，提供以下内容：

1. **技术设计说明** - 插件架构、扩展点选择、触发时机说明
2. **目录结构** - 完整的项目文件树
3. **pom.xml** - 完整的Maven配置文件
4. **Java源代码** - 所有扩展类和配置类
5. **资源文件** - Jelly配置界面、帮助文档、国际化文件
6. **测试代码** - 单元测试和集成测试（可选）
7. **构建步骤** - 详细的构建命令
8. **安装步骤** - 插件安装和验证指南
9. **触发时机说明** - 明确说明各扩展点的触发时机
10. **故障排除** - 常见问题和解决方法

---

## 质量规则

生成的插件必须满足：

- [ ] 与用户要求的Jenkins版本匹配
- [ ] 与用户要求的JDK版本匹配
- [ ] 使用正确的扩展点类型
- [ ] 在正确的触发时机执行
- [ ] 能够成功构建（`mvn clean package`）
- [ ] 生成有效的HPI包
- [ ] 遵循Jenkins插件开发规范
- [ ] 包含完整的配置界面（如需要）
- [ ] 代码注释完整
- [ ] 无已知API冲突
- [ ] 无servlet命名空间问题

---

## 触发时机参考表

| 扩展点 | 触发时机 | 适用场景 |
|--------|---------|---------|
| **Builder** | 构建执行阶段，在`perform()`调用时 | 自定义构建步骤 |
| **Publisher** | 构建完成后（成功/失败/不稳定） | 通知、归档、报告 |
| **Trigger** | 按Cron表达式或SCM变更 | 定时构建、自动触发 |
| **SCM** | 轮询检测到变更时 | Git/SVN变更检测 |
| **Action** | 用户点击链接/按钮时 | 手动操作、数据查看 |
| **RunListener** | 构建开始/结束/状态变更 | 审计、监控、指标 |
| **ItemListener** | 项目创建/删除/重命名 | 项目生命周期管理 |
| **ComputerListener** | 节点上线/离线/配置变更 | 节点监控、资源管理 |
| **QueueTaskDispatcher** | 任务分配节点时 | 负载均衡、节点过滤 |
| **BuildWrapper** | 构建前后（包装构建） | 环境准备、清理 |
| **EnvironmentContributor** | 构建开始时 | 环境变量注入 |
| **SCMBrowser** | 查看SCM变更时 | 自定义SCM浏览 |

---

## 异步执行模板

当用户选择需要异步执行时，使用以下模板：

```java
import hudson.model.AsyncPeriodicWork;
import hudson.model.TaskListener;

@Extension
public class YourAsyncPlugin extends AsyncPeriodicWork {

    public YourAsyncPlugin() {
        super("Your Plugin Async Task");
    }

    @Override
    protected void execute(TaskListener listener) throws Exception {
        // 异步执行逻辑
        listener.getLogger().println("Async task executed at: " + System.currentTimeMillis());
    }

    @Override
    public long getRecurrencePeriod() {
        return MIN * 5; // 每5分钟执行一次
    }
}
```

---

## 持久化配置模板

当用户选择需要持久化配置时，添加配置类：

```java
package com.example.plugin.config;

import hudson.Extension;
import hudson.model.AbstractDescribableImpl;
import hudson.model.Descriptor;
import org.kohsuke.stapler.DataBoundConstructor;

import java.io.Serializable;

public class PluginConfiguration extends AbstractDescribableImpl<PluginConfiguration> implements Serializable {
    
    private final String key;
    private final String value;

    @DataBoundConstructor
    public PluginConfiguration(String key, String value) {
        this.key = key;
        this.value = value;
    }

    public String getKey() { return key; }
    public String getValue() { return value; }

    @Extension
    public static class DescriptorImpl extends Descriptor<PluginConfiguration> {
        @Override
        public String getDisplayName() {
            return "Plugin Configuration";
        }
    }
}
```
