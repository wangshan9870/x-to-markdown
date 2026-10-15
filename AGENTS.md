# X Library 公开站点与旧版扩展项目规范

## 产品目标

此仓库的扩展源码是旧版，只作历史参考；当前 X Library 扩展在私有仓库开发。本仓库的 `site/` 继续承载 X Library 公开首页、支持页与隐私政策。

旧版扩展源码的原始目标：把用户当前打开且有权访问的单条 X 帖子保存为 Markdown，并允许选择将帖子图片一并本地归档。项目采用本地浏览器扩展，不引入账号、后端或数据库。

## 产品边界

- 只处理用户主动打开的单条帖子详情页
- 不做时间线批量抓取、自动遍历、登录绕过、访问控制规避或视频下载
- 默认导出 Markdown 并保留网络媒体地址
- 用户选择下载图片时，导出 Markdown 与 `images/` 组成的 ZIP
- 图片失败时保留网络地址并报告失败数量，不静默丢失内容

## 技术约定

- Chrome Extension Manifest V3
- TypeScript + Vite，UI 使用原生 DOM API
- Content Script 负责识别、提取和注入 Shadow DOM 快捷入口
- Background 负责跨域图片获取、ZIP 打包和浏览器下载
- Chrome `commands` 快捷键由 Background 路由到当前标签页，并复用 Content Script 的保存流程
- Popup 负责偏好设置、导出触发和状态反馈
- `chrome.storage.local` 只保存“是否下载图片”的偏好
- 页面按钮与快捷键必须使用同一保存函数，并阻止并发重复导出
- 所有解析和归档均在用户浏览器本地完成

## 目录约定

- `src/content/`：X 页面识别、帖子定位与提取
- `src/background/`：图片下载、ZIP 归档和文件下载
- `src/core/`：领域类型、Markdown、媒体与文件名规则
- `src/popup/`：扩展弹窗
- `public/icons/`：扩展图标母版及 16/32/48/128 像素 PNG，不使用平台官方商标
- `site/`：Chrome Web Store 使用的公开首页、支持页和隐私政策静态站点，不加载第三方脚本或统计服务
- `.github/workflows/`：GitHub Pages 自动部署流程
- `tests/`：与核心源码对应的单元测试
- `dist/`：构建产物，不提交 Git

## 质量门槛

- 修改后运行 `npm test` 和 `npm run build`
- UI 必须有加载、成功、不支持、进行中和失败状态
- 文件名兼容 Windows 和 macOS
- 页面入口使用 Shadow DOM，支持键盘焦点和 reduced motion
- 不记录或上传帖子、Cookie、Token 或浏览历史
- 公开站点必须提供可直接访问的 `/`、`/support/` 和 `/privacy/`，法律说明与扩展真实行为一致
- 提交前检查最终差异，只提交当前任务文件

## Git 约定

- Commit message 使用简洁英文
- 不自动 push
- 不提交 `node_modules/`、`dist/`、日志和编辑器配置
