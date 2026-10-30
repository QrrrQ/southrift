# 裂土之南 · 隐私政策

《裂土之南》（Southrift）的隐私政策页，托管在 GitHub Pages。

**对外链接**（填到 TapTap 后台的「隐私政策链接」）：

```
https://qrrrq.github.io/southrift/
```

> ⚠️ 注意用户名大小写：GitHub 用户名是 `QrrrQ`，但 **Pages 域名一律小写**
> （`qrrrq.github.io`）。写成大写会 404。

---

## 这个页面怎么来的

`index.html` 是**生成产物，不要直接改**。

真源在游戏仓库里：`game/scripts/consent.gd` 的 `PRIVACY_TEXT`。
游戏内弹窗和这个网页都由它生成，改动流程：

```bash
cd <游戏仓库>/game
python3 tools/make_privacy_html.py          # 生成到 deploy/privacy/southrift-privacy.html
cp deploy/privacy/southrift-privacy.html <本仓库>/index.html
```

### 为什么要这样

TapTap 审核规范 **2.2.1** 要求：后台填的隐私政策链接内容，必须与 APP 内的
隐私政策**一致**。这条的驳回理由只会笼统写「不一致」，不告诉你差在哪。

人工维护两份 600 字的中文法律文本，必然分叉。所以真源只有一个。

### 怎么核对一致性

页面 `<head>` 里有一行 HTML 注释（不影响渲染）：

```html
<!-- content-fingerprint: 5b41918e40e47a04 -->
```

它是 `PRIVACY_TEXT` 去掉所有空白字符后的 SHA-256 前 16 位。

```bash
# 线上指纹
curl -s https://qrrrq.github.io/southrift/ | grep -o "content-fingerprint: [a-f0-9]*"

# 与游戏内比对
cd <游戏仓库>/game && python3 tools/make_privacy_html.py --check
```

两边指纹相同 = 内容和游戏内一致。

---

## 更新后要做什么

推送到 `main` 分支即可，GitHub Pages 会自动重新发布（约 1 分钟）：

```bash
git add index.html && git commit -m "更新隐私政策" && git push
```

然后验证：

```bash
curl -sI https://qrrrq.github.io/southrift/ | grep -iE "^HTTP|content-type"
# 期望：HTTP/2 200 + Content-Type: text/html

curl -s https://qrrrq.github.io/southrift/ | grep -c "王浩宇"
# 期望：>= 1
```

**如果 `Content-Type` 是 `text/plain`，页面会显示成源码**——那是 raw 直链的
坑，GitHub Pages 不会踩到，它按扩展名给 MIME。

---

## 为什么不用自己的服务器

原本计划挂在 `www.qrrrq.cn` 下，实测发现那台机器上：

- Caddy 是跑在 Docker 容器里的
- `www.qrrrq.cn` 的站点块**只有 `reverse_proxy`，没有 `file_server`**
- 所有请求（含任意 `.html`）都被转发给 Next.js，返回 Next.js 自己的 404 页

要在那儿挂静态文件，得改 Caddy 配置并 reload（虽然不断站），或者往博客镜像
里塞文件再重建。为一份隐私政策付这个代价不值。

GitHub Pages 免费、HTTPS 自动、内容改完 `git push` 就生效，且**与应用内的
一致性检查（指纹）照样能做**。审核只看链接能否打开、内容是否与 APP 一致，
不关心托管在哪 —— 规范 2.2.7 只明确禁止用 **TapTap 论坛帖**承载。

---

## 仓库为什么是 public

GitHub Pages 免费版要求仓库公开（私有仓库需要 Pro / Team）。

这个仓库里**只有**这一份隐私政策，不含游戏源码，公开没有风险 ——
隐私政策本来就是给所有人看的。游戏源码在另一个私有位置。
