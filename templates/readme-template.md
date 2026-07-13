# 项目 README 模板

```markdown
# {{PLUGIN_DISPLAY_NAME}}

{{PLUGIN_DESCRIPTION}}

## 特性

- 基于 Jenkins {{JENKINS_VERSION}} 开发
- 扩展点类型：{{EXTENSION_TYPE}}
- 触发时机：{{TRIGGER_TIMING}}
- {{ASYNC_FEATURE}}{{PERSISTENCE_FEATURE}}{{GLOBAL_CONFIG_FEATURE}}

## 环境要求

| 组件 | 最低版本 |
|------|---------|
| Jenkins | {{JENKINS_VERSION}} |
| JDK | {{JDK_VERSION}} |
| Maven | {{MAVEN_VERSION}} |

## 安装

### 通过 Jenkins 插件管理中心

1. 下载 `{{ARTIFACT_ID}}.hpi`
2. 登录 Jenkins，进入 **Manage Jenkins** → **Plugins** → **Advanced**
3. 在 **Upload Plugin** 区域上传 `.hpi` 文件
4. 重启 Jenkins

### 手动安装

```bash
cp {{ARTIFACT_ID}}.hpi $JENKINS_HOME/plugins/
# 重启 Jenkins
```

## 使用方式

### {{USAGE_TITLE}}

{{USAGE_DESCRIPTION}}

{{USAGE_STEPS}}

## 配置说明

{{CONFIGURATION_DETAILS}}

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
{{CONFIG_ITEMS}}

## 开发

### 构建

```bash
# 清理并打包
mvn clean package

# 本地测试运行
mvn hpi:run

# 跳过测试打包
mvn clean package -DskipTests
```

输出产物：`target/{{ARTIFACT_ID}}.hpi`

### 项目结构

```
{{ARTIFACT_ID}}/
├── pom.xml
├── README.md
├── .gitignore
├── src/
│   ├── main/
│   │   ├── java/{{PACKAGE_PATH}}/
│   │   │   ├── {{MAIN_CLASS}}.java
│   │   │   ├── {{MAIN_CLASS}}Descriptor.java
│   │   │   └── config/
│   │   ├── resources/
│   │   │   └── {{PACKAGE_PATH}}/{{MAIN_CLASS}}/
│   │   │       ├── config.jelly
│   │   │       └── help.html
│   │   └── webapp/
│   └── test/
│       └── java/{{PACKAGE_PATH}}/
│           └── {{MAIN_CLASS}}Test.java
```

## 兼容性

| Jenkins 版本 | 状态 |
|--------------|------|
| {{JENKINS_VERSION}}+ | 支持 |

## 许可

{{LICENSE_INFO}}
```
