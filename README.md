# Xuewen Zhao 的个人主页

网站：<https://66leslie.github.io/>。使用 Jekyll 构建，由 GitHub Pages 从 `main` 分支根目录发布。

## 修改文字

About 和 Publications 直接编辑 Markdown 正文；Competitions 的奖项列表改由数据文件驱动，见下文。

| 页面 | 文件 | 内容 |
| --- | --- | --- |
| About Me `/` | `_pages/about.md` | 简介、研究兴趣、兴趣爱好 |
| Competitions `/competition/` | `_data/competitions.yml` | 竞赛奖项（时间轴 + 分类筛选） |
| Publications `/publications/` | `_pages/publications.md` | 论文，目前为 TBD |

文件顶部两行 `---` 之间是页面配置，包含网址和标题；日常改文字只需编辑第二行 `---` 后面的正文。

```markdown
## 小标题

这里写一段介绍，用 **加粗** 强调关键词。

- 第一条内容
- 第二条内容，可以添加 [链接文字](https://example.com)。
```

### 新增一条竞赛奖项

打开 `_data/competitions.yml`，在对应年份 `items` 的最上面照抄一条改字段即可（文件顶部有每个字段的说明）：

```yaml
      - month: "Nov."
        date: "2027-11"
        event: "比赛名称"
        prize: "National Second Prize"
        tier: national          # world / national / provincial / international，只决定徽章配色
        category: embedded      # 必须是文件开头 categories 里的 id
        detail:
          - "赛道说明"
          - "Team Captain"
        links:
          - text: "Code"
            url: "https://github.com/66Leslie/xxx"
```

顶部的 Awards / International / National 数字、每个筛选按钮上的计数都会自动重算，不用手动维护。`_pages/competition.md` 只剩开头一句引言，不需要改动。

首页暂不展示 Education 或 News。Hobbies 只保留在首页正文，不出现在顶栏。

## 修改个人资料与导航

- `_config.yml` → `author`：姓名、学校、头像、Gmail、GitHub、小红书地址。
- `_data/navigation.yml`：顶栏栏目名称、顺序和网址。
- `pixel_leslie.png`：当前头像，也用作网站图标。

正文邮箱 `{{ site.author.email }}` 自动读取配置；只修改 `author.email`，侧栏和正文就会同步。

## 本地预览

> **先读 [`AGENTS.md`](AGENTS.md)。** 系统自带的 ruby 版本过旧，直接跑 `bundle exec` 会失败，需要先用 rbenv 切到仓库 `.ruby-version` 指定的版本。

在仓库根目录运行：

```bash
# 1) 启用 rbenv 指定的 ruby（非交互式 shell 需要显式加载）
export PATH="$HOME/.rbenv/bin:$HOME/.rbenv/shims:$PATH"
eval "$(rbenv init - bash)"

# 2) 首次需要安装依赖
bundle install

# 3) 起服务
bundle exec jekyll serve --livereload --host 127.0.0.1 --port 4000
```

打开 <http://127.0.0.1:4000/>。保存 Markdown 后会自动重新构建；修改 `_config.yml` 后需要重启服务。

构建可以用 `bundle exec jekyll build` 单独跑，产物在 `_site/`。若 rbenv 不可用，`AGENTS.md` 第二节记录了一个已验证的临时绕法。

`_site/` 是自动生成的网站，不要编辑里面的文件。静态 HTTP 服务只展示上一次构建结果，不会自动处理 Markdown 修改。

## 其他目录

| 路径 | 用途 |
| --- | --- |
| `_includes/` | 头像栏、导航、页面头部等公共组件 |
| `_layouts/` | 页面骨架 |
| `_sass/`、`assets/` | 样式、字体和交互脚本 |
| `_local/`（仅本地） | 简历 PDF、旧头像等参考文件，不提交、不发布 |
| `Gemfile`、`Gemfile.lock` | Jekyll 依赖配置 |

本机原有的 `start-jekyll.bat`、`start-jekyll.ps1` 保留在根目录，含有特定 Windows Ruby 安装路径，仅供本机使用，已排除提交和发布。

GitHub 的 `pages build and deployment` 和 `github-pages` 环境负责发布网站，应保留。模板的赞助配置和失效引用统计任务已移除。

## Credits

This site is based on the AcadHomepage template and the Minimal Mistakes theme ecosystem. The original license is preserved in `LICENSE`.
