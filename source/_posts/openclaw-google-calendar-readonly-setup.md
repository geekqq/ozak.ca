---
title: OpenClaw 接入 Google Calendar（只读）实战记录
date: 2026-03-07 15:20:00
tags:
  - OpenClaw
  - Google Calendar
  - OAuth
  - Hexo
categories:
  - blog
---

这篇是给未来的自己留的操作手册：

目标很简单——让 OpenClaw 可以**直接读取**我的 Google Calendar（先只读，不改写）。

> 结论先说：已经跑通，可读到牙医预约。

---

## 一、场景背景

我之前把 Google Calendar 同步到了 Todoist，App 里能看到日历事件。
但 OpenClaw 通过 Todoist tasks API 拉取时，不一定能拿到这些“同步过来的日历事件”。

所以最终方案是：**直接给 OpenClaw 接 Google Calendar API（OAuth）**。

---

## 二、准备工作

### 1) 本地脚本与环境

在 OpenClaw 工作区新建 Python 脚本，并创建 venv：

```bash
python3 -m venv /root/.openclaw/workspace/.venv
/root/.openclaw/workspace/.venv/bin/pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib
```

脚本路径：

```text
/root/.openclaw/workspace/scripts/google_calendar_cli.py
```

功能：
- `auth`：OAuth 授权
- `list`：列出未来事件
- `next`：读取下一条匹配事件

### 2) Google Cloud 配置

在 Google Cloud Console：

1. 启用 **Google Calendar API**
2. 创建 OAuth Client，类型选 **Desktop app**
3. 下载 `credentials.json`

把文件放到：

```text
/root/.openclaw/workspace/.secrets/google-calendar/credentials.json
```

> 注意：这里必须是目录 + 文件结构，不要把 `google-calendar` 当成单个文件。

---

## 三、授权流程（踩坑版）

### 坑 1：`missing redirect_uri`

第一次授权链接报错缺少 `redirect_uri`。
修复：脚本里显式设置：

```python
flow.redirect_uri = 'http://localhost'
```

### 坑 2：`403 access_denied`

Google 报“应用还在测试阶段，未授权用户”。
修复：

- 打开 **Google Auth Platform → Audience**
- 在 **Test users** 添加自己的 Google 账号

### 坑 3：state 不匹配 / code 失效

OAuth 的 `state` 和 `code` 是一次性绑定的。
必须使用**同一轮生成的授权链接**完成授权并立刻回传回调 URL。

---

## 四、成功验证

最终读到了牙医预约（示例）：

- 2026-09-13 12:30 - 13:00
- `Appointment with your dentist at Eden Shores Dental Care`
- `28553 Hesperian Blvd. Hayward CA 94545`

说明读取链路已打通：

**OpenClaw ↔ Google Calendar（readonly scope）**

---

## 五、常用命令（备忘）

### 发起授权（首次）

```bash
OAUTHLIB_INSECURE_TRANSPORT=1 /root/.openclaw/workspace/.venv/bin/python /root/.openclaw/workspace/scripts/google_calendar_cli.py auth
```

### 查询未来 365 天里包含 dentist 的事件

```bash
OAUTHLIB_INSECURE_TRANSPORT=1 /root/.openclaw/workspace/.venv/bin/python /root/.openclaw/workspace/scripts/google_calendar_cli.py list --days 365 --limit 200 --query dentist
```

---

## 六、权限说明

目前是：

- ✅ 可读取 Google Calendar 事件
- ❌ 不可新增/修改/删除（只读 scope）

如果以后要让 OpenClaw 直接改日历，再把 scope 升级到可写并重新授权一次即可。

---

## 七、给未来自己的建议

1. Todoist 能看到，不代表 API 就一定能读到全部同步事件。
2. 关键事件（牙医、移民、学校）尽量用“主日历直连读取”。
3. OAuth 授权问题 80% 都是：redirect_uri、test users、state/code 轮次不一致。

完。下次再折腾 OAuth，先看这篇。