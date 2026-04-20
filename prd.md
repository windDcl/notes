
# 律所官网及内容管理平台 PRD

## 1. Project Overview (项目概览)
- **定位**：品牌展示型律所官网 + 轻量级 CMS。
- **目标**：通过极简的视觉语言、克制的动效和秩序感，建立专业、理性的法律服务品牌形象。
- **核心价值**：强调“确定性”，通过高质量的专业资讯和研究成果展示律所实力。

## 2. Core Requirements (核心需求)
- **响应式设计**：必须完美适配 Desktop (1200px+), Tablet, 和 Mobile 端。
- **SEO 友好**：全站内容需支持搜索引擎抓取（利用 Next.js SSR/SSG）。
- **极速加载**：首屏开场动画需平滑，LCP（最大内容绘制）需控制在 1.5s 内。
- **内容自管理**：非技术人员需能通过 CMS 快速发布资讯、PDF 及招聘岗位。

## 3. Core Features (核心功能)
- **动态 Hero 展示**：首屏 Canvas/WebGL 抽象动效，结合品牌 Slogan。
- **多维度内容展示**：区分“专业资讯”（时效性）与“专业研究”（深度长文）。
- **人才档案管理**：标准化的律师详情页，支持一键下载个人简历 PDF。
- **简历投递入口**：极简招聘列表，展示邮箱联络方式。
- **极简后台 (CMS)**：支持单管理员模式下的团队、文章和招聘内容的增删改查。

## 4. Core Components (核心组件)
- **Navigation (Header)**：固定定位，背景毛玻璃或纯白，支持透明到实色的滚动切换。
- **Member Card**：1:1 或 4:5 比例图片 + 姓名 + 职位的极简卡片。
- **Content List Item**：日期 + 标题 + 摘要的纯文本列表项。
- **Action Button**：带有微动效的实色/描边按钮（用于下载 PDF 或跳转）。
- **Minimal Footer**：包含版权信息、律所地址与核心联系方式。

## 5. App/User Flow (应用流程)
- **浏览流**：首页 -> 团队列表 -> 律师详情 -> 查看该律师关联研究 -> 下载 PDF。
- **内容流**：首页 -> 专业资讯 -> 文章详情页 -> 底部推荐阅读。
- **管理流 (CMS)**：登录页 -> 仪表盘 -> 选择模块（如文章）-> 填写 Markdown 内容 -> 预览并发布。

## 6. Tech Stack (技术栈)
- **Frontend**: Next.js 14+ (App Router), Tailwind CSS, Framer Motion (动效).
- **Backend**: FastAPI (Python), JWT 认证.
- **Database**: PostgreSQL (Supabase 或原生).
- **Storage**: MinIO 或 AWS S3 (用于存储图片和 PDF 文件).
- **Deployment**: Vercel (前端) + Docker/VPS (后端).

## 7. Implementation Plan (实施计划)
- **Phase 1: 基础设施 (Week 1)**
  - 初始化代码库，配置 Tailwind 间距系统与字体层级。
  - 建立数据库模型与基础 API。
- **Phase 2: 品牌展示面 (Week 2)**
  - 实现首页 Hero 动效及 UI 骨架。
  - 完成“关于我们”与“团队列表/详情”开发。
- **Phase 3: 内容与管理 (Week 3)**
  - 开发资讯/研究列表及详情页。
  - 构建 CMS 后台及文件上传功能。
- **Phase 4: 打磨与部署 (Week 4)**
  - 全站 SEO 优化与性能调优。
  - 完成端到端测试与正式上线。
