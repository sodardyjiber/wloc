# Apple WLOC 定位修改

修改 Apple 网络定位服务 (WiFi/基站) 返回的坐标，实现 iOS 网络定位虚拟定位。打开在线选点页面选位置即可生效，无需手动填经纬度。

> 本仓库已去除对原作者仓库的硬编码依赖；所有订阅链接、图标和脚本地址都指向当前仓库 `sodardyjiber/wloc`，因此即使原作者删除了原始仓库，本 fork 仍然可以继续提供完整功能。

---

## 订阅地址

**Surge:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.sgmodule

**Quantumult X:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.conf

**Loon:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.lpx

**Stash:**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.stoverride

**Shadowrocket(小火箭):**
https://raw.githubusercontent.com/sodardyjiber/wloc/refs/heads/main/modules/wloc.module

---

## 自部署 / 维护说明

如果你希望在原作者仓库消失后仍然能独立运行：

1. 保留本仓库 `sodardyjiber/wloc` 作为主项目。
2. 继续使用 `dist/wloc.js` 和 `dist/wloc-settings.js` 作为脚本源。
3. 部署 `worker/` 目录中的页面；不依赖原作者的 Cloudflare Worker 地址。
4. 将代理模块中的 `script-path` 和 `icon` 替换为本仓库对应的 `raw.githubusercontent.com/sodardyjiber/wloc/...` URL。

此仓库的核心功能已经封装在本地文件中，不依赖原作者的远程仓库继续提供脚本。

---

## 快捷指令（推荐，最方便）

直接用快捷指令切换 / 清除定位，无需打开选点页面：

- **wloc Set Location**：https://www.icloud.com/shortcuts/182f3a014597468eb1b15b99261cdf22
- **wloc Clear & Restore Location**：https://www.icloud.com/shortcuts/0352d53ed79849d382f50e9adf050662

**用法**

- **设置位置：** 在地图 App 里选好位置（长按地图选点）→ 共享 → 选「wloc Set Location」即可切换。
  - 苹果地图：选点 → 共享 → 「wloc Set Location」
  - 高德地图：选点 → 分享 → **更多** → 「wloc Set Location」
- **清理位置：** 点「wloc Clear & Restore Location」即可恢复真实定位。

支持苹果地图、高德（含短链，自动跟跳转 + GCJ-02→WGS84 坐标换算）。

---

### 关于地图链接解析（worker）

为了让苹果地图和高德走同一条流程，链接统一发给本地部署的 worker 解析：

- **高德**：分享出来是短链，真实坐标只藏在 302 跳转的 `Location` 头里，且是 GCJ-02 偏移坐标。
- **苹果地图**：链接里直接带 `coordinate=纬度,经度`，但在中国大陆同样是 GCJ-02 偏移坐标，故由 worker 统一做 GCJ-02→WGS84 换算后返回。

**隐私：** `/api/parse` 是纯转发解析——收到链接 → 跟跳转 → 解析坐标 → 返回 JSON，全程不写任何存储、不记日志、不缓存，处理完即丢。

**自部署：**

```bash
# 1. 克隆本仓库
git clone https://github.com/sodardyjiber/wloc.git
cd wloc/worker

# 2. 安装依赖
npm install

# 3. 登录 Cloudflare（首次需要）
npx wrangler login

# 4. 部署
npm run deploy
```

部署成功后会得到你自己的 Worker 地址，用这个地址替换快捷指令或页面中的原始域名即可。

---

## 推荐的工作流

1. 订阅模块并启用 MITM
2. 打开在线选点页面（本地部署 Worker / Pages）
3. 地图选位置 / 搜索地名 / 粘贴地图链接
4. 点击「储存到设备」
5. 下次 Apple 定位触发时自动生效

---

## 结论

本仓库已改成“自包含型”结构：脚本源、订阅模块和部署说明都指向 `sodardyjiber/wloc`，从而避免依赖原作者仓库的删除导致功能失效。
