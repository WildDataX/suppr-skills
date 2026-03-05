---
name: suppr-translate
description: 超能文献（Suppr）文档翻译 API。当用户需要翻译文档（Word、Excel、PowerPoint、PDF、TXT、HTML）、查询翻译状态或管理翻译任务时激活。
---

# 超能文献文档翻译 API

超能文献（Suppr）提供 AI 驱动的多语言文档翻译服务，支持 Word、Excel、PowerPoint、PDF、TXT、HTML 等格式。

- **API 基础地址**：`https://api.suppr.wilddata.cn`
- **API 文档**：https://openapi.suppr.wilddata.cn/introduction.html
- **认证方式**：`Authorization: Bearer <your_api_key>`

## 何时激活

- 用户需要翻译文档（Word、Excel、PowerPoint、PDF、TXT、HTML）
- 用户需要查询翻译任务状态或下载翻译结果
- 用户需要管理翻译任务（列表、停止）

## API 端点

### 1. 创建翻译任务（文件上传）

```
POST https://api.suppr.wilddata.cn/v1/translations
Content-Type: multipart/form-data
Authorization: Bearer <your_api_key>
```

**参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| file | File | 与 file_url 二选一 | 要翻译的文档文件 |
| file_url | String | 与 file 二选一 | 文档的 URL 地址 |
| file_name | String | 否 | 使用 file_url 时指定文件名 |
| from_lang | String | 否 | 源语言代码，默认 `auto`（自动检测） |
| to_lang | String | 是 | 目标语言代码 |
| optimize_math_formula | Boolean | 否 | 是否优化数学公式（仅 PDF 有效），默认 `false` |

### 2. 创建翻译任务（JSON）

仅支持 file_url 模式。

```
POST https://api.suppr.wilddata.cn/v1/translations
Content-Type: application/json
Authorization: Bearer <your_api_key>

{
  "file_url": "https://example.com/document.docx",
  "file_name": "document.docx",
  "from_lang": "en",
  "to_lang": "zh-cn",
  "optimize_math_formula": false
}
```

### 3. 获取翻译任务详情

```
GET https://api.suppr.wilddata.cn/v1/translations/{task_id}
Authorization: Bearer <your_api_key>
```

### 4. 获取翻译任务列表

```
GET https://api.suppr.wilddata.cn/v1/translations?offset=0&limit=20
Authorization: Bearer <your_api_key>
```

**查询参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| offset | Integer | 0 | 分页偏移量 |
| limit | Integer | 20 | 每页数量（1-100） |
| task_id | String | - | 可选，若提供则返回单个任务 |

### 5. 停止翻译任务

```
POST https://api.suppr.wilddata.cn/v1/translations/{task_id}/stop?reason=用户取消
Authorization: Bearer <your_api_key>
```

## 支持的文件格式

| 格式 | 扩展名 |
|------|--------|
| Word | .docx |
| Excel | .xlsx |
| PowerPoint | .pptx |
| PDF | .pdf |
| 纯文本 | .txt |
| 网页 | .html |

最大文件大小：2GB

## 语言代码

| 代码 | 语言 |
|------|------|
| auto | 任意语言（仅 from_lang，自动检测） |
| en | 英语 |
| zh-cn | 简体中文 |
| zh-tw | 繁体中文 |
| ja | 日语 |
| ko | 韩语 |
| es | 西班牙语 |
| fr | 法语 |
| pt | 葡萄牙语 |
| ru | 俄语 |
| de | 德语 |
| pl | 波兰语 |
| it | 意大利语 |

## 响应格式

所有响应使用统一格式：

```json
{
  "code": 0,
  "data": { ... },
  "msg": ""
}
```

`code` 为 0 表示成功，非 0 表示错误。

## 任务状态

| 状态 | 说明 |
|------|------|
| INIT | 任务已创建，等待处理 |
| PROGRESS | 翻译进行中 |
| DONE | 翻译完成 |
| STOPPING | 正在停止 |

## 典型工作流

1. **创建翻译任务**：通过文件上传或 URL 提交文档
2. **轮询任务状态**：定期调用获取任务详情接口，检查 `status` 字段
3. **获取翻译结果**：当状态为 `DONE` 时，从响应中的 `target_file_url` 下载译文

```bash
# 步骤 1：创建任务
curl -X POST https://api.suppr.wilddata.cn/v1/translations \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"file_url": "https://example.com/paper.pdf", "from_lang": "en", "to_lang": "zh-cn"}'

# 步骤 2：查询状态（使用返回的 task_id）
curl https://api.suppr.wilddata.cn/v1/translations/TASK_ID \
  -H "Authorization: Bearer YOUR_API_KEY"

# 步骤 3：当 status 为 DONE 时，从 target_file_url 下载译文
```

## 响应示例

### 创建任务成功

```json
{
  "code": 0,
  "data": {
    "task_id": "02a6c6d1-3f70-4a5a-80bc-971d53a37bb1",
    "status": "INIT",
    "consumed_point": 453,
    "source_lang": "auto",
    "target_lang": "ko",
    "optimize_math_formula": false
  },
  "msg": ""
}
```

### 任务详情

```json
{
  "code": 0,
  "data": {
    "task_id": "taskid-1234",
    "status": "DONE",
    "progress": 1,
    "consumed_point": 453,
    "source_file_name": "example.docx",
    "source_file_url": "https://example.com/source.docx",
    "target_file_url": "https://example.com/target.docx",
    "source_lang": "auto",
    "target_lang": "ko",
    "error_msg": null,
    "optimize_math_formula": false
  },
  "msg": ""
}
```
