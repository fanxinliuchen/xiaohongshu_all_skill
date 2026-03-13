# xiaohongshu_all_skill

小红书全能助手技能项目，提供两类能力：

- 文案创作：标题、正文、封面图
- 平台操作：发布、搜索、互动、登录检查

当前仓库只同步 `skills/xhs/` 这部分内容，并通过 `.gitignore` 排除了其他 workspace 文件与本地密钥配置。

## 目录结构

```text
skills/xhs/
├── SKILL.md
├── check_env.sh
├── _meta.json
├── references/
│   ├── title-guide.md
│   ├── content-guide.md
│   └── cover-guide.md
└── scripts/
    ├── cover.sh
    └── generate.sh
```

## 功能说明

### 1. 文案创作

优先使用当前对话模型直接生成：

- 标题
- 正文
- 封面图提示词

备用脚本：

- `scripts/generate.sh`：基于指定 AI API 生成标题或正文
- `scripts/cover.sh`：生成小红书 3:4 封面图

### 2. 平台操作

- `check_env.sh`：检查 MCP、Xvfb、生图 API、登录状态
- 依赖本地小红书 MCP 服务

## 封面图生成

### 默认输出

`cover.sh` 默认输出到：

```bash
/tmp/xhs_cover.png
```

也可以手动指定输出路径：

```bash
bash skills/xhs/scripts/cover.sh "标题文字" "英文prompt" /tmp/my_cover.png "#FFF4E8" "#5C4033"
```

### 当前支持的生图方式

- Gemini
- OpenAI / OpenAI 兼容接口
- 火山引擎方舟（通过 OpenAI 兼容接口接入）
- 腾讯云混元生图（AIART）
- 用户上传图片拼接封面

### 火山引擎方舟配置

可在 `skills/xhs/.env` 中配置：

```bash
IMG_API_TYPE=openai
ARK_API_KEY=your_ark_api_key
ARK_BASE_URL=https://ark.cn-beijing.volces.com/api/v3
ARK_MODEL_ID=doubao-seedream-4-5-251128
ARK_IMAGE_SIZE=2560x1440

# 可选：显式映射
IMG_API_KEY=your_ark_api_key
IMG_API_BASE=https://ark.cn-beijing.volces.com/api/v3
IMG_MODEL=doubao-seedream-4-5-251128
IMG_SIZE=2560x1440
```

> 注意：`skills/xhs/.env` 已被 `.gitignore` 排除，不会提交到仓库。

## 依赖

建议系统具备：

- `convert` 或 `magick`（ImageMagick）
- `identify`（ImageMagick 组件）
- `curl`
- `python3`
- 中文字体，如 `fonts-noto-cjk`
- 小红书 MCP 服务

## 已修复事项

### 火山引擎方舟生图适配

已适配：

- 自动加载 `skills/xhs/.env`
- 支持 `ARK_API_KEY / ARK_BASE_URL / ARK_MODEL_ID`
- OpenAI 兼容接口调用火山方舟图片生成
- 针对方舟调整默认图片尺寸为 `2560x1440`

### ImageMagick 兼容性修复

已修复 `cover.sh` 在 ImageMagick 6 环境下错误调用：

- 错误形式：`convert identify ...`
- 正确形式：
  - `magick identify ...`
  - 或 `identify ...`

## 产物文件放在哪里？

当前这套 skill 的文件输出位置分两类：

### 1. 封面图最终产物

如果没有显式指定输出路径，默认放在：

```bash
/tmp/xhs_cover.png
```

如果调用时指定了第三个参数，则按你指定的路径保存。

### 2. 运行过程中的临时文件

例如：

- 生图 API 返回的中间图片
- 裁剪后的顶部图
- 标题文字图
- 临时 Python 脚本

这些文件会放在脚本自动创建的临时目录里，例如：

```bash
/tmp/tmp.xxxxxxxx/
```

脚本结束后会自动清理，不会长期保留。

### 3. 如果当前对话模型先生成图片再拼封面

按 `SKILL.md` 的约定，可能会临时保存到类似：

```bash
/tmp/xhs_ai_img.png
```

然后再交给 `cover.sh` 拼接。这个路径是约定路径，不一定每次固定，取决于调用方式。

## 本次测试文件

本次我手动验证时生成的测试封面在：

```bash
/tmp/xhs_cover_test.png
```

文件信息：

- 尺寸：1080x1440
- 格式：PNG

## 仓库说明

当前仓库用于同步 `skills/xhs/` 项目文件，不包含：

- 整个 OpenClaw workspace
- 本地 `.env` 密钥
- 其他无关 skills

如果后续需要，也可以继续补充：

- 发布示例
- MCP 登录流程说明
- 常见报错排查
