# 项目接口与RSS支持问答

## RSS支持

1. **RSS解析功能**:
   - 项目内置RSS解析器(server/utils/rss2json.ts)
   - 支持将RSS/Atom格式转换为JSON
   - 功能特点:
     - 支持标准RSS和Atom格式
     - 解析标题、描述、链接、图片等基本信息
     - 支持媒体内容(音频/视频)解析
     - 支持iTunes扩展标签

## 内容获取接口

1. **主要接口**:
   - 路径: `/api/s`
   - 参数:
     - `id`: 数据源ID(如"zhihu")
     - `latest`: 是否强制获取最新内容(true/false)
     - `format`: 返回格式(json/rss/atom)，默认为json
   - 返回格式:
     - JSON: application/json
     - RSS: application/rss+xml
     - Atom: application/atom+xml
   - 示例:
     ```
     GET /api/s?id=zhihu&format=rss
     ```
     ```
     GET /api/s?id=weibo&format=atom
     ```
     ```ts
     interface SourceResponse {
       status: "success" | "cache"
       id: string
       updatedTime: number
       items: NewsItem[]
     }
     ```

2. **批量获取接口**:
   - 路径: `/api/s/entire` (POST)
   - 参数:
     - `sources`: 数据源ID数组
   - 返回格式: JSON数组

3. **RSS输出**:
   - 目前接口不支持直接输出RSS格式
   - 可以通过以下方式实现:
     - 使用`/api/s`获取JSON数据
     - 使用`rss2json`工具反向转换为RSS格式

4. **使用示例**:
   - 获取知乎热门内容:
     ```
     GET /api/s?id=zhihu
     ```
   - 批量获取多个数据源:
     ```
     POST /api/s/entire
     {"sources": ["zhihu", "weibo"]}
     ```

5. **缓存机制**:
   - 默认使用缓存数据
   - 根据数据源配置的interval判断是否需要更新
   - 支持TTL缓存过期机制
