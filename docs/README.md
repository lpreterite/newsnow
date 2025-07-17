# 项目爬虫技术原理分析

## 基础架构

1. **HTTP请求**:
   - 使用`ofetch`库作为基础HTTP客户端
   - 配置了浏览器User-Agent伪装: `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)`
   - 默认设置10秒超时和3次重试

2. **图片代理**:
   - 通过`/api/proxy/img.png`接口代理图片请求
   - 支持两种编码方式: `encodeURIComponent`和`encodeBase64URL`

## 数据源实现

1. **数据源配置**:
   - 所有数据源定义在`shared/sources.json`
   - 每个数据源包含名称、类型、分类、主页、颜色和更新间隔等配置
   - 支持两种数据获取类型:
     - `hottest`: 热门内容
     - `realtime`: 实时内容

2. **实现方式**:
   - 每个数据源在`server/sources/`目录下有独立实现文件
   - 实现文件导出`defineSource`函数
   - 示例实现(知乎):
     ```ts
     export default defineSource({
       zhihu: async () => {
         const url = "https://www.zhihu.com/api/v3/feed/topstory/hot-list-web"
         const res = await myFetch(url)
         return res.data.map(processItem)
       }
     })
     ```

3. **数据结构**:
   - 新闻条目结构(`NewsItem`):
     ```ts
     interface NewsItem {
       id: string | number
       title: string
       url: string
       extra?: {
         hover?: string
         info?: string
       }
     }
     ```
   - API响应结构(`SourceResponse`):
     ```ts
     interface SourceResponse {
       status: "success" | "cache"
       id: string
       updatedTime: number
       items: NewsItem[]
     }
     ```

## 特点

1. **多数据源支持**:
   - 支持50+个国内外主流平台
   - 包括社交媒体、新闻网站、技术社区等

2. **分类管理**:
   - 数据按分类(tech/china/world/finance)组织
   - 每个分类包含多个数据源

3. **更新机制**:
   - 每个数据源可配置独立更新间隔(60秒-60分钟)
   - 支持实时和热门两种更新模式
