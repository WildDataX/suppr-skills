---
name: suppr-search-articles
description: 超能文献（Suppr）学术文献检索 API。当用户需要检索学术论文、查找 PubMed 文献、搜索研究资料时激活。
---

# 超能文献学术文献检索 API

超能文献（Suppr）提供基于 PubMed 的 AI 语义文献检索服务，支持自然语言查询和丰富的文献元数据返回。

- **API 基础地址**：`https://api.suppr.wilddata.cn`
- **API 文档**：https://openapi.suppr.wilddata.cn/introduction.html
- **认证方式**：`Authorization: Bearer <your_api_key>`

## 何时激活

- 用户需要检索学术论文或文献
- 用户需要搜索 PubMed 数据库
- 用户需要查找特定主题的研究资料
- 用户需要获取论文的 DOI、PMID、影响因子等元数据

## API 端点

### 语义文献检索

```
POST https://api.suppr.wilddata.cn/v1/docs/semantic_search
Content-Type: application/json
Authorization: Bearer <your_api_key>
```

**请求体参数：**

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| query | String | 是 | - | 自然语言查询字符串 |
| topk | Integer | 否 | 20 | 最大返回结果数量，范围 1-100 |
| return_doc_keys | String[] | 否 | [] | 指定返回的文献字段列表 |
| auto_select | Boolean | 否 | true | 是否由 AI 自动选择最优结果 |

## return_doc_keys 完整字段列表

| 字段 | 说明 |
|------|------|
| title | 标题 |
| link | 链接 |
| snippet | 检索条目内容片段/摘要 |
| datetime | 时间 |
| abstract | 论文摘要 |
| doi | 数字对象标识符 DOI |
| elocation_doi | 电子定位符 DOI |
| pii | 出版商项目标识符 PII |
| elocation_pii | 电子定位符 PII |
| cited_by_num | 被引用次数 |
| pub_year | 出版年份 |
| pub_season | 出版季 |
| pub_month | 出版月份 |
| pub_day | 出版日 |
| issue_pub_year | 出版时间（期刊印刷版） |
| issue_pub_season | 出版季（期刊印刷版） |
| issue_pub_month | 出版月份（期刊印刷版） |
| issue_pub_day | 出版日（期刊印刷版） |
| article_pub_year | 出版年份（电子版） |
| article_pub_season | 出版季（电子版） |
| article_pub_month | 出版月份（电子版） |
| article_pub_day | 出版日（电子版） |
| publication | 出版物 |
| publication_abbr | 出版物缩写 |
| publication_nlm_id | 出版物 NLM ID |
| p_issn | 纸质版 ISSN |
| e_issn | 电子版 ISSN |
| l_issn | 链接 ISSN |
| impact_factor | 影响因子 |
| publisher | 出版商 |
| publisher_abbr | 出版商（缩写） |
| publisher_location | 出版机构所在地 |
| pub_source_str | 出版源 |
| language | 语言（title/abstract/snippet） |
| pub_language | 原出版语言 |
| i18n_infos | 国际化信息 |
| figure_ids | 图片 ID 列表 |
| figure_urls | 图片 URL 列表 |
| table_ids | 表格 ID 列表 |
| pmid | PubMed ID |
| pmcid | PubMed Central ID |
| pub_volume | 卷 |
| pub_issue | 期 |
| pub_page | 页码 |
| pub_start_page | 起始页码 |
| pub_end_page | 结束页码 |
| pub_model | 出版模式 |

## 常用字段组合

### 快速概览

```json
{
  "query": "糖尿病最新研究进展",
  "topk": 10,
  "return_doc_keys": ["title", "abstract", "pub_year"],
  "auto_select": true
}
```

### 引用格式

```json
{
  "query": "CRISPR gene editing therapy",
  "topk": 20,
  "return_doc_keys": ["title", "doi", "pmid", "authors", "publication", "pub_year", "pub_volume", "pub_issue", "pub_page"],
  "auto_select": true
}
```

### 完整元数据

```json
{
  "query": "machine learning drug discovery",
  "topk": 5,
  "return_doc_keys": ["title", "abstract", "doi", "pmid", "pmcid", "impact_factor", "authors", "pub_year", "publication", "cited_by_num"],
  "auto_select": true
}
```

## 响应格式

```json
{
  "code": 0,
  "data": {
    "search_items": [
      {
        "doc": {
          "title": "论文标题",
          "abstract": "论文摘要...",
          "doi": "10.1007/xxxxx",
          "pmid": "35397038",
          "authors": [
            {
              "fore_name": "名",
              "last_name": "姓",
              "affiliations": [
                { "name": "所属机构" }
              ]
            }
          ],
          "publication": "期刊名称",
          "pub_year": 2024,
          "impact_factor": 5.2
        },
        "search_gateway": "pubmed"
      }
    ],
    "consumed_points": 20
  },
  "msg": ""
}
```

`code` 为 0 表示成功，非 0 表示错误。

## 使用示例

```bash
curl -X POST https://api.suppr.wilddata.cn/v1/docs/semantic_search \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "阿尔茨海默病早期诊断生物标志物",
    "topk": 10,
    "return_doc_keys": ["title", "abstract", "doi", "pmid", "pub_year", "impact_factor"],
    "auto_select": true
  }'
```

## 注意事项

- `topk` 为期望返回的最大数量，实际返回可能少于该值（取决于检索结果）
- 不指定 `return_doc_keys` 时返回默认字段集
- `auto_select` 为 `true` 时，AI 会自动筛选与查询最相关的结果
- 速率限制：60 次/分钟
