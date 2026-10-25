# 备考刷题站 · 数学二 / 811 电路 / 时政

在线地址：**https://303234cjq.github.io/beikao-shuati/**

单文件静态刷题页（题库内嵌在 `index.html`），外加一份可在线编辑的题库增量 `bank-extra.json`。手机浏览器打开后建议「添加到主屏幕」。

## 内容

- 1000 道练习 + 456 道历年真题（1990–2024）+ 88 道生成练习，按模块（极限、导数、积分、线代…）和年份筛选
- 数学公式由 MathJax 渲染（首次打开需要能访问 `cdn.jsdelivr.net`）
- 夜间模式、字号调节、必背公式栏、统计与预测

## 相对原始副本做的改动

原始文件是从云端站点导出的，凡是依赖服务端的地方在静态托管下都是 404。已用 `patch_quiz.py` 打了三处补丁（脚本可重复运行，换新版 HTML 后重跑即可）：

| 改动 | 说明 |
|---|---|
| 修复「下载我的学习记录」 | 原来调用 `/api/state`（404）。现在直接读浏览器里的 `localStorage['study_cloud_state_v2']` |
| 新增「导入学习记录」 | 设置页多了按钮，选 JSON 备份即可合并恢复（错题取并集、笔记以备份为准、统计合并） |
| 新增在线题库更新 | 打开页面时同步拉取 `bank-extra.json`，把新题合并进题库；有新题会弹提示 |
| 改写设置页文案 | 原文案说「登录一次、看到云端已保存」，静态版没有登录，改为说明记录存在本机、如何搬运 |

刷题、判分、解析、错题本、统计这些本来就是纯前端逻辑，未改动。

## 更新题库（不用重发 1.7 MB 的 HTML）

编辑 **`bank-extra.json`**（可以直接在 GitHub 网页上改，手机也行）：

```json
{
  "rev": "2026-11-01",
  "questions": [ { "id": "extra-math-001", "subject": "math", "module": "01-limits", "moduleName": "模块1 极限与连续", "stem": "…", "opts": ["A. …","B. …"], "answer": "B", "solution": ["…"], "type": "选择题", "qtype": "选择题" } ],
  "removed": ["要下架的题目 id"]
}
```

- `id` 必须唯一：已存在则覆盖，不存在则新增，列在 `removed` 里的会被移除
- `module` / `moduleName` 沿用现有模块（如 `01-limits` / `模块1 极限与连续`），新题才会出现在对应模块里
- `rev` 改成新值，用户下次打开会看到「题库已更新：+N 题」

## 学习记录怎么跨设备

### 方式一：在线同步（推荐，配一次即可）

记录同步到私有仓库 **303234Cjq/study-sync** 的 `state.json`，换设备只要填同一个令牌。

1. 生成令牌：<https://github.com/settings/tokens?type=beta> → Generate new token
   - Repository access 选 **Only select repositories** → 勾 `study-sync`
   - Permissions → Repository permissions → **Contents: Read and write**
   - 有效期按需，建议 90 天，过期重新生成一次
2. 手机上打开站点 → 设置 → 「在线同步（可选）」→ 粘贴令牌 → **连接并同步**
3. 该设备以后每次打开自动拉取，作答/笔记/错题自动写回仓库，顶部显示「云端已保存」

注意：

- 令牌只存在**该浏览器**的 localStorage，不写进网页源码；公用电脑上用完请点「断开」
- 每次作答/标记会产生一次 commit（`study sync …`），这是正常的
- 令牌泄露或丢失：去 GitHub 撤销并重新生成，再在设备上重填

### 方式二：手动导出导入（不配令牌时可用）

1. 旧设备：设置 → 数据备份 →「下载我的学习记录」
2. 把 JSON 发到新设备（微信/隔空投送都行）
3. 新设备：设置 →「导入学习记录」

## 更新页面本身

覆盖 `index.html` 后重新跑一次补丁再推送：

```bash
python3 ../patch_quiz.py index.html   # 幂等
git add -A && git commit -m "更新" && git push
```

约 30 秒生效。
