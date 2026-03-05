# 超能文献（Suppr）Skills 使用指南

超能文献 Skills 是一套 Claude Code 技能插件，让你可以在 Claude Code 中直接调用超能文献的 **文档翻译** 和 **学术文献检索** API。

## 安装

在 Claude Code 中执行以下命令：

```bash
# 从 Marketplace 添加插件源
/plugin marketplace add WildDataX/suppr-skills

# 安装插件
/plugin install suppr-skills@WildDataX-suppr-skills
```

安装完成后，插件会自动放置在 `.claude/skills/suppr-skills/` 目录下：

```
.claude/skills/suppr-skills/
├── .claude-plugin/
│   ├── marketplace.json
│   └── plugin.json
└── skills/
    ├── suppr-translate/SKILL.md
    └── suppr-search-articles/SKILL.md
```

## 前置条件

你需要一个超能文献 API Key。前往 [超能文献](https://suppr.wilddata.cn) 注册并获取。

## 技能一览

| 技能名称 | 触发命令 | 功能 |
|----------|----------|------|
| `suppr-translate` | `/suppr-translate` | 文档翻译（Word、Excel、PPT、PDF、TXT、HTML） |
| `suppr-search-articles` | `/suppr-search-articles` | 学术文献语义检索（基于 PubMed） |

---

## 文档翻译（suppr-translate）

### 何时触发

当你在 Claude Code 中提到以下意图时，该技能会自动激活：

- 翻译一份文档
- 查询翻译任务状态
- 管理翻译任务（查看列表、停止任务）

### 使用示例

#### 翻译一份英文 PDF 为中文

在 Claude Code 中直接说：

```
请帮我把这份 PDF 翻译成中文：https://example.com/paper.pdf
```

或者使用命令调用：

```
/suppr-translate
```

Claude 会自动调用超能文献 API，完成以下流程：

1. **创建翻译任务** — 提交文档（支持文件上传或 URL）
2. **轮询任务状态** — 等待翻译完成
3. **返回翻译结果** — 提供译文下载链接

### 支持的文件格式

| 格式 | 扩展名 |
|------|--------|
| Word | .docx |
| Excel | .xlsx |
| PowerPoint | .pptx |
| PDF | .pdf |
| 纯文本 | .txt |
| 网页 | .html |

**最大文件大小：**

| 用户类型 | 文件大小限制 |
|----------|-------------|
| 会员用户 | 100MB |
| 非会员用户 | 5MB |

### 支持的语言

| 代码 | 语言 |
|------|------|
| `auto` | 自动检测（仅源语言） |
| `en` | 英语 |
| `zh-cn` | 简体中文 |
| `zh-tw` | 繁体中文 |
| `ja` | 日语 |
| `ko` | 韩语 |
| `es` | 西班牙语 |
| `fr` | 法语 |
| `pt` | 葡萄牙语 |
| `ru` | 俄语 |
| `de` | 德语 |
| `pl` | 波兰语 |
| `it` | 意大利语 |

### 更多用法

```
帮我把 ./report.docx 翻译成日语
```

```
查一下翻译任务 02a6c6d1-3f70-4a5a-80bc-971d53a37bb1 的状态
```

```
列出我最近的翻译任务
```

```
停止翻译任务 02a6c6d1-3f70-4a5a-80bc-971d53a37bb1
```

---

## 学术文献检索（suppr-search-articles）

### 何时触发

当你在 Claude Code 中提到以下意图时，该技能会自动激活：

- 检索学术论文或文献
- 搜索 PubMed 数据库
- 查找特定主题的研究资料
- 获取论文的 DOI、PMID、影响因子等元数据

### 使用示例

#### 搜索特定主题的文献

在 Claude Code 中直接说：

```
帮我搜索关于 CRISPR 基因编辑治疗的最新论文
```

或者使用命令调用：

```
/suppr-search-articles
```

Claude 会调用超能文献的语义检索 API，返回结构化的文献结果。

### 常见查询场景

#### 快速概览 — 获取标题和摘要

```
搜索糖尿病最新研究进展，返回前 10 篇
```

#### 获取引用信息

```
搜索 "machine learning drug discovery" 的论文，我需要 DOI、作者和期刊信息用于引用
```

#### 获取完整元数据

```
查找阿尔茨海默病早期诊断生物标志物的相关文献，包含影响因子和被引次数
```

### 可返回的文献字段

| 字段 | 说明 |
|------|------|
| `title` | 论文标题 |
| `abstract` | 论文摘要 |
| `doi` | 数字对象标识符 |
| `pmid` | PubMed ID |
| `pmcid` | PubMed Central ID |
| `pub_year` | 出版年份 |
| `publication` | 期刊名称 |
| `impact_factor` | 影响因子 |
| `cited_by_num` | 被引用次数 |
| `authors` | 作者列表（含姓名、机构） |
| `link` | 论文链接 |
| `snippet` | 内容片段 |

完整字段列表请参考 [SKILL.md](skills/suppr-search-articles/SKILL.md)。

### 注意事项

- 每次请求最多返回 100 条结果（通过 `topk` 控制）
- 支持自然语言查询，中英文均可
- `auto_select` 模式下 AI 会自动筛选最相关的结果
- 速率限制：60 次/分钟

---

## API 认证

所有 API 请求需要在 HTTP Header 中携带 API Key：

```
Authorization: Bearer <your_api_key>
```

你可以通过环境变量设置：

```bash
export SUPPR_API_KEY="your_api_key_here"
```

## 相关链接

- [超能文献官网](https://suppr.wilddata.cn)
- [API 文档](https://openapi.suppr.wilddata.cn/introduction.html)
- 联系方式：IT@wilddata.cn
