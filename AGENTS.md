# AGENTS.md

给在此仓库工作的 AI agent 的操作手册。**先读完这份再动手**，能省掉大量重复的考古。

---

## 一、这是一个什么站

Jekyll + Minimal Mistakes 主题的学术个人主页，托管在 GitHub Pages，从 `main` 分支根目录发布。

| 项 | 值 |
| --- | --- |
| 线上地址 | <https://66leslie.github.io/> |
| 站点所有人 | Xuewen Zhao（赵学文），南京师范大学电气与自动化工程学院 |
| 主题 | Minimal Mistakes 的样式与布局**已内置进本仓库**（`_sass/` + `_layouts/`），非 gem 引入 |
| Markdown | kramdown + GFM |
| 时区 | Asia/Shanghai |

**部署方式是 GitHub Pages 自动构建**：push 到 `main` 即上线。本地构建只是用来预览，不参与部署。

---

## 二、环境：先读这段，否则你会浪费半小时

### 这台机器上 `bundle exec jekyll serve` **跑不通**

不是你的错，也不是代码坏了，是**版本不可能满足**：

- 系统 ruby 是 **3.0.2**（Ubuntu 22.04 自带，2021 年的版本）
- 但 `Gemfile.lock` 锁的是 `github-pages 232`，其依赖 `nokogiri 1.18.10` **要求 ruby ≥ 3.1**
- 另外 `bundle` 这个命令在系统 PATH 里**根本不存在**（bundler 2.2.22 是 ruby 的 default gem，但没有可执行入口）

所以任何 `bundle exec ...`、`bundle install` 都会直接失败。

### ⚠️ 不要试图"修"环境

如果你读到类似 `Could not find tzinfo-data-1.2025.3` 的报错，**不要去 `gem install` 补依赖**。补不完的，而且会污染系统 gem 目录（`/var/lib/gems/3.0.0` 还未必有写权限）。这是一个死胡同。

### ⚠️ `/tmp` 是个 10MB 的 tmpfs —— 这是第二个"看起来像环境坏了"的坑

`df -h /tmp` 会看到它**只有 10M**。bundler 解析依赖时要在 `/tmp` 写 compact index，空间不足会报：

```
There was an error while trying to write to
`/tmp/bundler-compact-index-.../versions`.
There was insufficient space remaining on the device.
```

**这不是网络问题，也不是依赖冲突。** 对策是把 `TMPDIR` 指到有空间的分区：

```bash
# <scratch> 换成项目所在分区上的任意目录，不要用 /tmp
mkdir -p <scratch>/.tmpbuild
export TMPDIR=<scratch>/.tmpbuild
bundle install
```

注意：`/var/tmp` 在这台机器上**不存在**，不要用它。项目所在分区有 200G+ 空闲。

### 正确做法：用 rbenv 指定的 ruby

本仓库的用户级 rbenv 装在 `~/.rbenv`，已编译好可用的 ruby 3.3.x。仓库根目录的 `.ruby-version` 会告诉 rbenv 用哪个版本。

```bash
# 一次性：把 rbenv 挂进当前 shell（非交互式 shell 默认不加载）
export PATH="$HOME/.rbenv/bin:$HOME/.rbenv/shims:$PATH"
eval "$(rbenv init - bash)"

# 确认版本
ruby -v          # 应显示 3.3.x，而不是 3.0.2
```

若 `ruby -v` 仍是 3.0.2，说明 shims 没进 PATH —— 检查上面两条 export 是否执行了。

### 构建与预览

```bash
cd <repo>

# 构建（产出到 _site/）
bundle exec jekyll build

# 本地预览 http://127.0.0.1:4000
bundle exec jekyll serve --host 127.0.0.1 --port 4000
```

首次在新 ruby 下需要装依赖（一次性，会写入 `Gemfile.lock`）：

```bash
bundle install
```

**验证构建成功的标志**：`_site/index.html` 存在，且 `_site/assets/css/main.css` 里能搜到你改动的类名。

### 兜底方案（仅当 rbenv 不可用时）

若 `~/.rbenv` 被删除或编译失败，还有一个已验证可行的绕法：用一个**临时的独立 Gemfile** 绕开项目的锁文件。

```bash
mkdir -p /tmp/jekyll-preview && cat > /tmp/jekyll-preview/Gemfile <<'EOF'
source "https://rubygems.org"
gem "jekyll", "3.10.0"
gem "kramdown", "2.4.0"
gem "kramdown-parser-gfm"
gem "rouge", "3.30.0"
group :jekyll_plugins do
  gem "jekyll-paginate", "1.1.0"
  gem "jekyll-sitemap", "1.4.0"
  gem "jekyll-gist", "1.5.0"
  gem "jekyll-feed", "0.17.0"
  gem "jekyll-redirect-from", "0.16.0"
end
EOF
cd /tmp/jekyll-preview && bundle install --local

cd <repo>
BUNDLE_GEMFILE=/tmp/jekyll-preview/Gemfile \
  ruby -rrubygems -e "load Gem.bin_path('jekyll','jekyll')" build
```

这是**临时手段**，`/tmp` 会被清空。优先级低于 rbenv 方案。

**注意**：`gem install` 一律要加 `--user-install`，因为 `/var/lib/gems/3.0.0` 无写权限。

---

## 三、页面与文件对照表

| 页面 | URL | 内容来源 | 改什么 |
| --- | --- | --- | --- |
| 首页 About | `/` | `_pages/about.md` | 直接改 Markdown 正文 |
| 竞赛 | `/competition/` | **`_data/competitions.yml`** + `_includes/awards.html` | 加奖项改 yml，不要改 HTML |
| 论文 | `/publications/` | `_pages/publications.md` | 直接改 Markdown（目前是 TBD） |

其他关键位置：

| 路径 | 作用 |
| --- | --- |
| `_config.yml` | 站点配置、导航外的全局设置、author 信息。**注意：`_config.yml` 里没有 `theme` 或 `remote_theme` 声明**——主题文件已内置，改布局/样式直接改 `_sass/` 和 `_layouts/` |
| `_data/navigation.yml` | 顶部导航项（加页面记得来这里登记） |
| `_sass/` | 全部样式（20 个文件 + `vendor/`）；入口是 `assets/css/main.scss` 的 `@import` 列表 |
| `_includes/` | 可复用 HTML 片段 |
| `_layouts/` | 布局模板 |
| `_pages/` | 各页面正文 |
| `_local/` | **本地参考资料，已被 gitignore，不进仓库** |

---

## 四、站点约定（改之前务必遵守）

### 数据驱动，不要手写数字

竞赛页的**统计数字、筛选按钮计数、年份分组**全部由模板计算得出。新增/删除奖项后，这些数字会自动更新。**绝对不要手动改这些数字**，否则筛选后必然对不上。

新增一条奖项：打开 `_data/competitions.yml`，在对应年份的 `items` 最上面加一条，字段说明见文件顶部注释。`tier` 取值决定配色与归类：

- `world` → 计入顶部 International
- `national` → 计入 National
- `provincial` → 省级
- `participation` → 参赛/入围，不计入任何统计

`category` 必须取自文件里的 `categories` 列表 id。

### 图片、视频一律不进 Git

`.git` 目前只有 4.4M。二进制文件一旦 commit，会永久留在历史里**无法真正删除**，之后每次 clone 都变慢，且 GitHub 对单文件 > 100MB 直接拒收。

- 视频：外链（Bilibili / YouTube）或对象存储，页面上用 `<iframe>` / `<video>` 嵌入
- 图片：走 `assets/` 但需先压缩；大图不要提交

### 用相对路径

站内的 import、链接、图片引用一律用相对路径，不要写绝对文件系统路径。

### 不要动的东西

- `_site/` —— 构建产物，已 gitignore，不要手工编辑或提交
- `.workbuddy/` —— agent 的项目记忆目录，**不是缓存**，不要删除

---

## 五、提交前自查

- [ ] 构建无报错，`_site/` 里能看到改动生效
- [ ] 如果改了数据文件，页面上的统计数字看起来合理
- [ ] 没有把二进制大文件加进 Git（`git status` 扫一眼）
- [ ] 新加的页面已登记到 `_data/navigation.yml`（如果希望出现在导航里）
- [ ] 样式改动集中在 `_sass/`，没有散落的 inline style
- [ ] **没有把说明类文档发布上线**：`find _site -maxdepth 1 -name '*.md'` 必须为空。根目录新增 `.md`（本文件、NOTES、TODO 等）后要同步加进 `_config.yml` 的 `exclude`，否则 Jekyll 会原样复制甚至渲染成页面

---

## 六、已知的坑

| 坑 | 现象 | 对策 |
| --- | --- | --- |
| 系统 ruby 版本过低 | `bundle` 找不到 / nokogiri 报错 | 用 rbenv 切到 3.3.x，见第二节 |
| `Gemfile.lock` 含跨平台条目 | `universal-darwin-19`、`x64-mingw-ucrt`，在 Linux 上可能报奇怪错 | 不要手工改 lock；让 `bundle install` 处理 |
| `pkill -f "jekyll.*serve"` | **会匹配到执行这条命令的 shell 自身，把当前会话杀掉** | 用 `ps aux | grep jekyll` 拿到 PID，再 `kill <PID>` |
| 非交互式 shell 没有 rbenv | agent 里 `ruby -v` 显示 3.0.2 | 显式 `export PATH=...` + `eval "$(rbenv init - bash)"` |
| 改了 SCSS 但页面没变 | 浏览器缓存或没重新构建 | 重新 build，硬刷新（Ctrl+Shift+R） |
