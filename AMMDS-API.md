# AMMDS 开放 API 文档

## 1. 概述

### 1.1 基础信息

| 项目 | 说明 |
|------|------|
| Base URL | `http://{host}:{port}` |
| 字符编码 | UTF-8 |
| 响应格式 | JSON |

### 1.2 鉴权说明

AMMDS 开放 API（`/api/v1/**`）采用 **API Key** 鉴权机制。

- **鉴权方式**：在请求头中携带 `x-api-key` 字段
- **前置条件**：系统全局配置 `GLOBAL_OPEN_API` 必须已开启
- **公开接口**：`/api/v1/health` 相关接口无需鉴权，可直接访问

**请求头示例：**

```
x-api-key: your-api-key-here
```

**鉴权失败响应：**

```json
{
  "code": 401,
  "message": "您的登录已过期，请重新登录",
  "data": null,
  "timestamp": 1753958400000
}
```

> **注意**：API Key 需在系统管理后台生成并分配给对应的用户。请联系系统管理员获取。

### 1.3 统一响应格式

所有接口均使用 `ResponseTemplate<T>` 作为统一响应体：

```json
{
  "code": 200,
  "message": "成功",
  "data": {},
  "timestamp": 1753958400000
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `code` | Integer | 业务状态码，详见 [1.4 通用错误码](#14-通用错误码) |
| `message` | String | 状态描述信息 |
| `data` | Object | 返回数据，具体结构见各接口定义；失败时为 `null` |
| `timestamp` | Long | 响应时间戳（毫秒） |

### 1.4 通用错误码

| 状态码 | 枚举值 | 说明 |
|--------|--------|------|
| `200` | `SUCCESS` | 请求成功 |
| `400` | `REQUEST_ERROR` | 请求参数错误 |
| `401` | `UNAUTHORIZED` | 未授权（API Key 无效或缺失） |
| `403` | `FORBIDDEN` | 禁止访问 |
| `404` | `NOT_FOUND` | 资源不存在 |
| `500` | `FAIL` | 服务端内部错误 |

---

## 2. 系统 API

### 2.1 健康检查

检查服务运行状态。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/health/check` |
| **请求方式** | `GET` / `POST` |
| **鉴权** | 否（公开接口） |
| **Content-Type** | 无 |

**请求参数**

无。

**请求示例**

```bash
curl -X GET "http://localhost:8080/api/v1/health/check"
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | Boolean | 固定返回 `true`，表示服务正常运行 |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": true,
  "timestamp": 1753958400000
}
```

---

## 3. 影视 API

### 3.1 影视导入

导入影视元数据，支持文件上传（海报、背景图等）。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/import` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `multipart/form-data` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `uniqueid` | String | 否 | 番号/唯一标识 |
| `originalTitle` | String | 否 | 原始标题 |
| `titleCn` | String | 否 | 中文标题 |
| `plot` | String | 否 | 剧情简介 |
| `plotCn` | String | 否 | 中文剧情简介 |
| `rating` | Double | 否 | 评分 |
| `premiered` | String | 否 | 发行日期 |
| `runtime` | Integer | 否 | 时长（分钟） |
| `language` | String | 否 | 语言 |
| `country` | String | 否 | 国家/地区 |
| `mosaic` | String | 否 | 马赛克类型 |
| `numbers` | String | 否 | 番号列表（JSON 数组字符串） |
| `genres` | String | 否 | 类型标签（JSON 数组字符串） |
| `tags` | String | 否 | 标签（JSON 数组字符串） |
| `studio` | String | 否 | 制作公司（JSON 数组字符串） |
| `issueStudio` | String | 否 | 发行公司（JSON 数组字符串） |
| `director` | String | 否 | 导演（JSON 数组字符串） |
| `actors` | String | 否 | 演员列表（JSON 数组字符串） |
| `subscribe` | Boolean | 否 | 是否订阅（默认 `false`） |
| `favorite` | Boolean | 否 | 是否收藏（默认 `false`） |
| `poster` | File[] | 否 | 海报图片文件 |
| `posterUrl` | String | 否 | 海报 URL（JSON 数组字符串） |
| `fanart` | File[] | 否 | 背景图文件 |
| `fanartUrl` | String | 否 | 背景图 URL（JSON 数组字符串） |
| `thumb` | File[] | 否 | 缩略图文件 |
| `thumbUrl` | String | 否 | 缩略图 URL（JSON 数组字符串） |
| `extrafanart` | File[] | 否 | 额外背景图文件 |
| `extrafanartUrl` | String | 否 | 额外背景图 URL（JSON 数组字符串） |

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/movie/import" \
  -H "x-api-key: your-api-key-here" \
  -F "uniqueid=ABC-123" \
  -F "originalTitle=Sample Movie" \
  -F "titleCn=示例影片" \
  -F "plot=This is a sample movie." \
  -F "rating=8.5" \
  -F "premiered=2024-01-15" \
  -F "runtime=120" \
  -F "language=ja" \
  -F "mosaic=有码" \
  -F 'genres=["动作","剧情"]' \
  -F 'tags=["热门","推荐"]' \
  -F "subscribe=true" \
  -F "poster=@/path/to/poster.jpg"
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | Boolean | `true` 表示导入成功 |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": true,
  "timestamp": 1753958400000
}
```

**失败响应示例**

```json
{
  "code": 500,
  "message": "导入失败",
  "data": false,
  "timestamp": 1753958400000
}
```

---

### 3.2 快捷导入

简化版影视导入，使用 JSON 请求体，无需文件上传，适合开发者快速导入元数据。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/import/quick` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `uniqueid` | String | **是** | 番号/唯一标识 |
| `originalTitle` | String | **是** | 原始标题 |
| `titleCn` | String | 否 | 中文标题 |
| `plot` | String | 否 | 剧情简介 |
| `plotCn` | String | 否 | 中文剧情简介 |
| `rating` | Double | 否 | 评分 |
| `premiered` | String | 否 | 发行日期 |
| `runtime` | Integer | 否 | 时长（分钟） |
| `language` | String | 否 | 语言 |
| `country` | String | 否 | 国家/地区 |
| `mosaic` | String | 否 | 马赛克类型 |
| `numbers` | String[] | 否 | 番号列表 |
| `genres` | String[] | 否 | 类型标签 |
| `tags` | String[] | 否 | 标签 |
| `studio` | String[] | 否 | 制作公司 |
| `issueStudio` | String[] | 否 | 发行公司 |
| `director` | String[] | 否 | 导演列表 |
| `posterUrl` | String[] | 否 | 海报图片 URL 列表 |
| `fanartUrl` | String[] | 否 | 背景图 URL 列表 |
| `thumbUrl` | String[] | 否 | 缩略图 URL 列表 |
| `extrafanartUrl` | String[] | 否 | 额外背景图 URL 列表 |
| `subscribe` | Boolean | 否 | 是否订阅（默认 `false`） |
| `favorite` | Boolean | 否 | 是否收藏（默认 `false`） |

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/movie/import/quick" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "uniqueid": "ABC-123",
    "originalTitle": "Sample Movie",
    "titleCn": "示例影片",
    "plot": "这是一部示例影片。",
    "rating": 8.5,
    "premiered": "2024-01-15",
    "runtime": 120,
    "language": "ja",
    "mosaic": "有码",
    "genres": ["动作", "剧情"],
    "tags": ["热门", "推荐"],
    "posterUrl": ["https://example.com/poster.jpg"],
    "subscribe": true
  }'
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | Boolean | `true` 表示导入成功 |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": true,
  "timestamp": 1753958400000
}
```

**失败响应示例**

```json
{
  "code": 500,
  "message": "导入失败",
  "data": false,
  "timestamp": 1753958400000
}
```

---

### 3.3 从数据源导入

通过指定数据源和影视 ID，直接从第三方数据源获取完整元数据并导入到本地库。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/import/from-source` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `source` | String | **是** | 数据源标识，可选值见[附录 B](#b-数据源枚举值) |
| `id` | String | **是** | 目标影视在数据源中的 ID |
| `provider` | String | 否 | 数据源子类型（按数据源区分） |

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/movie/import/from-source" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "source": "metatube",
    "id": "12345"
  }'
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | String | 导入成功后返回新创建的影片 ID；失败时为 `null` |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "timestamp": 1753958400000
}
```

**失败响应示例**

```json
{
  "code": 500,
  "message": "导入失败，请检查数据源和参数",
  "data": null,
  "timestamp": 1753958400000
}
```

---

### 3.4 影片详情

根据影片 ID 获取完整的影片元数据。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/{id}` |
| **请求方式** | `GET` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | 无 |

**路径参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | String | **是** | 影片 ID（UUID 格式） |

**请求示例**

```bash
curl -X GET "http://localhost:8080/api/v1/movie/a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -H "x-api-key: your-api-key-here"
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | String | 影片 ID |
| `uniqueid` | String | 番号/唯一标识 |
| `originalTitle` | String | 原始标题 |
| `titleCn` | String | 中文标题 |
| `plot` | String | 剧情简介 |
| `plotCn` | String | 中文剧情简介 |
| `tagline` | String | 标语 |
| `outline` | String | 概要 |
| `rating` | Double | 评分 |
| `premiered` | String | 发行日期 |
| `poster` | String | 海报 URL |
| `fanart` | String | 背景图 URL |
| `thumb` | String | 缩略图 URL |
| `extrafanart` | String | 额外背景图 URL |
| `genre` | String[] | 类型标签列表 |
| `tag` | String[] | 标签列表 |
| `studio` | String[] | 制作公司列表 |
| `issueStudio` | String[] | 发行公司列表 |
| `runtime` | Integer | 时长（分钟） |
| `mpaa` | String | 分级 |
| `language` | String | 语言 |
| `country` | String | 国家/地区 |
| `sets` | Map | 系列信息 |
| `director` | String[] | 导演列表 |
| `actors` | Map | 演员信息 |
| `extend` | Map | 扩展信息 |
| `sourceLinks` | Map | 来源链接 |
| `files` | Object[] | 关联文件列表 |
| `shared` | Boolean | 是否共享 |
| `finished` | Boolean | 是否完结 |
| `subscribe` | Boolean | 是否订阅 |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": {
    "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "uniqueid": "ABC-123",
    "originalTitle": "Sample Movie",
    "titleCn": "示例影片",
    "plot": "这是一部示例影片的剧情简介。",
    "plotCn": "这是一部示例影片的剧情简介（中文）。",
    "tagline": "精彩不容错过",
    "outline": "影片概要",
    "rating": 8.5,
    "premiered": "2024-01-15",
    "poster": "https://example.com/poster.jpg",
    "fanart": "https://example.com/fanart.jpg",
    "thumb": "https://example.com/thumb.jpg",
    "extrafanart": "https://example.com/extrafanart.jpg",
    "genre": ["动作", "剧情"],
    "tag": ["热门", "推荐"],
    "studio": ["Sample Studio"],
    "issueStudio": ["Sample Publisher"],
    "runtime": 120,
    "mpaa": "R18",
    "language": "ja",
    "country": "日本",
    "sets": {},
    "director": ["导演A"],
    "actors": {"主演A": "角色A"},
    "extend": {},
    "sourceLinks": {},
    "files": [],
    "shared": false,
    "finished": false,
    "subscribe": true
  },
  "timestamp": 1753958400000
}
```

**失败响应示例**

```json
{
  "code": 404,
  "message": "影片不存在",
  "data": null,
  "timestamp": 1753958400000
}
```

---

### 3.5 影片总数

获取当前系统中已入库的影片总数。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/count` |
| **请求方式** | `GET` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | 无 |

**请求参数**

无。

**请求示例**

```bash
curl -X GET "http://localhost:8080/api/v1/movie/count" \
  -H "x-api-key: your-api-key-here"
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | Integer | 影片总数 |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": 1024,
  "timestamp": 1753958400000
}
```

---

### 3.6 影片匹配

通过番号或文件哈希（pHash / osHash）查找本地已入库的影片 ID，可用于本地文件与媒体库的自动匹配。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/lookup` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `number` | String | 否 | 番号 |
| `pHash` | String | 否 | 感知哈希（Perceptual Hash） |
| `osHash` | String | 否 | 文件系统哈希 |

> **注意**：`number`、`pHash`、`osHash` 至少提供一个，否则返回参数错误。

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/movie/lookup" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "number": "ABC-123"
  }'
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data` | String | 匹配到的影片 ID；未匹配到时为 `null` |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "timestamp": 1753958400000
}
```

**未匹配响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": null,
  "timestamp": 1753958400000
}
```

**错误码**

| 状态码 | 说明 |
|--------|------|
| `400` | `number`、`pHash`、`osHash` 三个参数均为空 |

---

### 3.7 影片检索

调用所有已开启的数据源进行在线影片检索，返回统一结构的检索结果。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/movie/search` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `keyword` | String | **是** | 检索关键词（番号或影片名） |
| `waitTime` | Integer | 否 | 超时时间（秒），默认 `60` |

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/movie/search" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "keyword": "ABC-123",
    "waitTime": 30
  }'
```

**响应参数（`data[]` 中每项字段）**

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | String | 检索结果 ID |
| `provider` | String | 数据源标识 |
| `source` | String | 数据源名称 |
| `number` | String | 番号 |
| `title` | String | 标题 |
| `summary` | String | 简介 |
| `url` | String | 详情页 URL |
| `cover` | String | 封面图 URL |
| `thumb` | String | 缩略图 URL |
| `previewImages` | String[] | 预览图/剧照 URL 列表 |
| `trailerUrl` | String | 预告片 URL |
| `actors` | String[] | 演员列表 |
| `director` | String | 导演 |
| `genres` | String[] | 类型标签列表 |
| `tags` | String[] | 标签列表 |
| `studio` | String | 制作公司 |
| `label` | String | 厂商标识 |
| `series` | String | 系列名称 |
| `score` | Double | 评分 |
| `runtime` | Integer | 时长（分钟） |
| `release` | String | 发行日期 |
| `mosaic` | String | 马赛克信息 |
| `extras` | Map\<String, Object\> | 扩展字段，承载数据源特有数据 |

> **注意**：各数据源按自身能力填充字段，不支持的字段返回 `null`。

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": [
    {
      "id": "12345",
      "provider": "metatube",
      "source": "MetaTube",
      "number": "ABC-123",
      "title": "Sample Movie Title",
      "summary": "This is a sample movie summary.",
      "url": "https://example.com/movie/12345",
      "cover": "https://example.com/cover.jpg",
      "thumb": "https://example.com/thumb.jpg",
      "previewImages": ["https://example.com/preview1.jpg"],
      "trailerUrl": null,
      "actors": ["Actor A", "Actor B"],
      "director": "Director X",
      "genres": ["动作", "剧情"],
      "tags": ["热门"],
      "studio": "Sample Studio",
      "label": null,
      "series": null,
      "score": 8.5,
      "runtime": 120,
      "release": "2024-01-15",
      "mosaic": null,
      "extras": {}
    }
  ],
  "timestamp": 1753958400000
}
```

---

## 4. 翻译 API

### 4.1 语言识别

识别输入文本的语言，返回语言代码和语言名称。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/translate/language/identify` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `text` | String | **是** | 待识别的文本内容 |

**请求示例**

```bash
curl -X POST "http://localhost:8080/api/v1/translate/language/identify" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "text": "这是一段中文文本"
  }'
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data.code` | String | 语言代码（如 `zh`）；无法识别时返回 `unknown` |
| `data.name` | String | 语言名称（如 `简体中文`） |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": {
    "code": "zh",
    "name": "简体中文"
  },
  "timestamp": 1753958400000
}
```

**无法识别时**

```json
{
  "code": 200,
  "message": "成功",
  "data": {
    "code": "unknown",
    "name": "unknown"
  },
  "timestamp": 1753958400000
}
```

---

### 4.2 文本翻译

翻译文本内容，支持自动识别源语言或指定源语言，返回翻译结果及翻译引擎信息。

| 项目 | 说明 |
|------|------|
| **接口路径** | `/api/v1/translate/text` |
| **请求方式** | `POST` |
| **鉴权** | 是（`x-api-key`） |
| **Content-Type** | `application/json` |

**请求参数**

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `text` | String | **是** | 待翻译的文本内容 |
| `target` | String | **是** | 目标语言代码，取值见[附录 A](#a-语言代码表) |
| `source` | String | 否 | 源语言代码，留空时自动识别。取值见[附录 A](#a-语言代码表)（不含 `auto`） |
| `provider` | String | 否 | 指定翻译器 ID；留空时自动选择最合适的翻译器 |

**请求示例**

```bash
# 自动识别源语言翻译为中文
curl -X POST "http://localhost:8080/api/v1/translate/text" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "text": "Hello, world!",
    "target": "zh"
  }'

# 指定源语言翻译为英文
curl -X POST "http://localhost:8080/api/v1/translate/text" \
  -H "Content-Type: application/json" \
  -H "x-api-key: your-api-key-here" \
  -d '{
    "text": "こんにちは世界",
    "source": "ja",
    "target": "en"
  }'
```

**响应参数**

| 字段 | 类型 | 说明 |
|------|------|------|
| `data.originalText` | String | 原始文本 |
| `data.originalLanguage` | String | 源语言名称 |
| `data.originalLanguageCode` | String | 源语言代码 |
| `data.translateText` | String | 翻译后文本 |
| `data.translateLanguage` | String | 目标语言名称 |
| `data.translateLanguageCode` | String | 目标语言代码 |
| `data.translateEngine` | String | 翻译引擎名称（如 `百度翻译`） |

**成功响应示例**

```json
{
  "code": 200,
  "message": "成功",
  "data": {
    "originalText": "Hello, world!",
    "originalLanguage": "英语",
    "originalLanguageCode": "en",
    "translateText": "你好，世界！",
    "translateLanguage": "简体中文",
    "translateLanguageCode": "zh",
    "translateEngine": "百度翻译"
  },
  "timestamp": 1753958400000
}
```

**错误码**

| 状态码 | 说明 |
|--------|------|
| `400` | `text` 或 `target` 为空 |
| `500` | 翻译失败（翻译器返回错误），`message` 中包含具体错误信息 |
| `500` | 指定的 `provider` 翻译器不存在 |

---

## 附录

### A. 语言代码表

| 代码 | 语言名称 |
|------|----------|
| `auto` | 自动检测 |
| `zh` | 简体中文 |
| `cht` | 繁体中文 |
| `en` | 英语 |
| `ja` | 日语 |
| `ko` | 韩语 |
| `ru` | 俄语 |
| `cs` | 捷克语 |

> **注意**：`auto` 仅在翻译接口的 `source` 参数留空时自动生效，不可作为显式参数值使用。

### B. 数据源枚举值

| 标识 | 数据源名称 |
|------|-----------|
| `metatube` | MetaTube |
| `theporndb` | ThePornDB |
| `stashbox` | StashBox |
| `fanzadmm` | FanzaDMM |
