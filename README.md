# LiteratureShelf（本地文献索引）

一个用 Python 标准库写的 Windows 本地文献索引。支持多个文献库；PDF 留在原文件夹，程序只读取文件和保存独立索引。

## 运行

需要 Python 3.9 或更新版本。在 PowerShell 中进入放置 `LiteratureShelf.py` 的文件夹，然后运行：

```powershell
py LiteratureShelf.py
```

程序会打开本机浏览器。在“管理文献库”中添加名称 `NB` 和路径 `D:\project\NB\ARTICLE`；首次扫描按 PDF 总大小可能需要一些时间。以后可在同一界面添加其他文献库。关闭 PowerShell 窗口或按 Ctrl+C 退出。

如果 Windows 没有 `py` 命令，可以尝试 `python LiteratureShelf.py`。无需安装第三方 Python 包。

## 能做什么

- 递归索引各文献库中的 PDF，从 `[A]`、`[R]`、`[B]`、`[P]` 提取类型，识别标题开头的 `★`；未按此格式命名的文件也会收录。
- 同时搜索标题、原文件夹、多个标签、笔记和建议标签。多个关键词需同时出现；用双引号搜索完整短语。可按文献库、类型、阅读状态、标签筛选；点击文献后打开原 PDF。
- 同标题或文件内容完全相同的条目会列入“重复候选”。程序不会自动删除、移动或改名 PDF。
- 在“分类规则”中，为**每个文献库**设定标签和标题关键词。例如：标签 `ADRN/MES`，关键词 `adrenergic, mesenchymal, noradrenergic`。已有文件夹名称若出现在其他文章的标题中，也会作为待确认建议。建议标签需要在文献详情中确认。
- 每次扫描会检测新增、改名、移走或消失的文件。文件内容相同且仅有一条旧记录对应时，改名和移动后会保留笔记与标签。同一路径的 PDF 内容变化会显示复核提示。
- 默认每 120 秒自动扫描一次已登记的文献库；新增文献**保存进该文献库文件夹或子文件夹后**即可自动录入。浏览器默认 Downloads 文件夹若不在登记的文献库内，则不会被扫描。可以点击“扫描更新”立即扫描。

## 数据与限制

本地索引、标签和笔记位于 `%LOCALAPPDATA%\LiteratureShelf\literature.sqlite3`。建议定期在**退出程序后**备份这个文件；更换电脑时复制此文件并保证文献库路径仍有效。本程序不会把 PDF 发往外部服务。

当前版本只索引文件名、文件夹、你输入的标签和笔记，不读取 PDF 正文、摘要或 DOI。标题规则只产生建议，不代表文章内容已得到验证。由于浏览器不能直接提供 Windows 文件夹的本地路径，添加文献库时需粘贴完整路径。文献库名称可重命名；如需换一个不同的根目录，先添加新文献库，避免旧笔记对应到另一批文件。

可选参数：

```powershell
py LiteratureShelf.py --interval 30
py LiteratureShelf.py --interval 0       # 仅手动扫描
py LiteratureShelf.py --no-browser --port 8765
```

开发检查：`py -m unittest -v test_literature_shelf.py`。
