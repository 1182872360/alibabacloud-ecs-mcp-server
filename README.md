# Alibaba Cloud ECS MCP Server

基于 [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) 的阿里云 ECS 管理服务。

## 功能特性

- 🖥️ **实例管理**: 创建、启动、停止、重启、删除 ECS 实例
- 🔍 **资源查询**: 查询地域、可用区、实例规格、镜像、安全组、交换机
- 🛡️ **智能错误处理**: 结构化错误响应，自动推荐相关工具
- 🔗 **工具链引导**: 参数缺失时自动引导 LLM 调用正确的工具

## 快速开始

### 安装依赖

```bash
uv add mcp pydantic alibabacloud-ecs20140526 alibabacloud-tea-openapi alibabacloud-tea-util
```

### 配置环境变量

```bash
export ALIBABA_CLOUD_ACCESS_KEY_ID="your-access-key-id"
export ALIBABA_CLOUD_ACCESS_KEY_SECRET="your-access-key-secret"
# 可选
export ECS_DEFAULT_REGION="cn-hangzhou"
export LOG_LEVEL="INFO"
```

### 运行服务

```bash
python main.py
```

## 工具列表

### 实例生命周期管理

| 工具名 | 描述 |
|--------|------|
| `runInstances` | 创建 ECS 实例 |
| `startInstance` | 启动实例 |
| `stopInstance` | 停止实例 |
| `rebootInstance` | 重启实例 |
| `deleteInstance` | 删除实例 |

### 资源查询

| 工具名 | 描述 |
|--------|------|
| `describeInstances` | 查询实例列表 |
| `describeInstanceAttribute` | 获取实例详情 |
| `describeRegions` | 查询可用地域 |
| `describeZones` | 查询可用区 |
| `describeInstanceTypes` | 查询实例规格 |
| `describeImages` | 查询镜像 |
| `describeSecurityGroups` | 查询安全组 |
| `describeVSwitches` | 查询交换机 |

## 使用示例

### 创建实例流程

1. 查询地域: `describeRegions`
2. 查询镜像: `describeImages(region_id="cn-hangzhou")`
3. 查询规格: `describeInstanceTypes(region_id="cn-hangzhou")`
4. 查询安全组: `describeSecurityGroups(region_id="cn-hangzhou")`
5. 查询交换机: `describeVSwitches(region_id="cn-hangzhou")`
6. 创建实例: `runInstances(...)`

## MCP 客户端配置

### Claude Desktop

```json
{
  "mcpServers": {
    "ecs": {
      "command": "python",
      "args": ["/path/to/main.py"],
      "env": {
        "ALIBABA_CLOUD_ACCESS_KEY_ID": "your-ak",
        "ALIBABA_CLOUD_ACCESS_KEY_SECRET": "your-sk"
      }
    }
  }
}
```

## 错误处理

所有工具返回统一的 `MCPResult` 格式：

```json
{
  "success": false,
  "error": {
    "error_type": "RESOURCE_NOT_FOUND",
    "message": "指定的实例规格在当前地域不可用",
    "suggestion": "请使用 describeInstanceTypes 工具查询可用的实例规格",
    "related_tools": [
      {
        "tool_name": "describeInstanceTypes",
        "description": "查询可用的实例规格",
        "example_params": {"region_id": "cn-hangzhou"}
      }
    ]
  }
}
```

## License

MIT
