# 一、markitDown

## 功能介绍
MarkItDown 是一款轻量级的 Python 工具，用于将各种文件转换为 Markdown，用于大型语言模型（LLM）及相关文本分析管道。为此，它最类似于 textract，但更注重保留重要的文档结构和内容（包括：标题、列表、表格、链接等）。虽然输出通常相当简洁且用户友好，但它是为文本分析工具设计的——对于高保真文档转换来说，可能不是最适合人类消费的选择。
==依赖python3.10+==
## 下载地址
[https://github.com/microsoft/markitdown](https://github.com/microsoft/markitdown)
## 安装方式

```
# 1. 解压你下载的 ZIP 文件，比如解压到 D:\markitdown

# 2. 进入解压后的目录
cd D:\markitdown

# 3. 安装完整版（支持所有文件格式：PDF、Word、PPT、Excel、音视频等）
pip install "markitdown[all]" -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 安装验证
```
markitdown --version
```

## 使用方式
```
# 转换单个文件
markitdown D:\文档.pdf -o D:\输出.md

# 查看帮助
markitdown --help
```

# 二、文捕
## 功能介绍
一款在线博客内容解析下载工具，支持多个平台，多种格式，尽量保留文章原始排版
## 请求地址
[https://www.blog-keeper.com/](https://www.blog-keeper.com/)

# 三、Obsidian插件（飞书上下载的md文件中图片转换问题）
## 功能介绍
扫描笔记中所有外部图片链接 → 下载到本地 → 自动把链接替换成相对路径

## 安装步骤
1. 打开 Obsidian 的 **设置**。
    
2. 在左侧菜单找到并点击 **第三方插件**。
    
3. 确保 **安全模式** 是关闭状态。
    
4. 点击 **社区插件** 右侧的 **浏览** 按钮。
    
5. 在搜索框输入 **`Local Images Plus`**。
    
6. 找到后，点击插件卡片上的 **安装** 按钮，安装成功后，再点击 **启用**。
    
7. 打开 Obsidian **设置**
    
8. 左侧菜单点击 **文件与链接**
    
9. 找到 **"附件文件夹"**（Attachment folder path）
    
10. 在这里输入你想要的路径，例如：
    
    - `assets/` → 图片保存在笔记同级目录下的 `assets` 文件夹
        
    - `_attachments/` → 保存在 `_attachments` 文件夹
        
    - `./` → 直接保存在笔记同级目录

## 使用方法
1. 打开包含远程图片的笔记
    
2. 按 `Ctrl/Cmd + P` 打开命令面板，搜索 **`Local Images Plus: Download all images`** 并执行
    
3. 所有图片会自动下载到 `./assets/` 或 `./media/` 文件夹（可配置），链接自动更新