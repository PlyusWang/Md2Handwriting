# Markdown 转手写体生成器

这个脚本读取 Markdown 文件，公式会先做轻量 LaTeX 排版，再把中文、英文、数字、数学符号和结构线分别处理，最后可以导出为拍摄效果或打印效果。它会自动避开中文标点出现在行首、开始括号/引号出现在行尾的情况。

## 运行

```powershell
python -m pip install -r requirements.txt
python handwriting_app.py
```

界面左侧可以选择 Markdown、手写字体、背景目录和输出目录。左右两栏之间的分割线可以拖动，预览区支持滚动、缩放滑杆、`适合窗口`，也支持 `Ctrl + 鼠标滚轮` 缩放。

## 参数预设

- `保存参数`：把当前路径和所有滑块保存为 JSON。
- `加载参数`：从 JSON 恢复路径和滑块，并自动重新预览。

## 导出

点击 `导出...` 后可以选择：

- `拍摄模式`：保留纸纹、背景、阴影、透视、噪点等拍照效果。
- `打印模式`：生成纯白底页面，适合打印、PDF 或 Word。

支持格式：

- PNG：逐页图片。
- JPG：逐页图片，白底保存。
- WEBP：逐页图片。
- PDF：多页 PDF。
- DOCX：把每页图片插入 Word 文档，每页一张。

高清导出相关滑块：

- `导出重渲染倍率`：真正按倍率重新排版和渲染，默认 4x，不是低清图后期放大。
- `导出DPI`：写入 PNG/JPG/PDF 的 DPI 元数据，默认 600。
- `有损格式质量`：控制 JPG/WEBP 质量，默认 98。

进度和性能相关：

- 预览渲染和导出渲染都有独立进度条。
- 进度按“总页数 + 当前页页内百分比”累加，而不是只按已完成页数跳变。
- `并行线程数` 可以同时渲染多页，默认按 CPU 核心数取最多 4。
- `GPU后处理(0/1)` 只加速拍摄模式里的照片后处理；需要安装 CuPy 且本机有 CUDA，否则会自动回落到 CPU。

## 命令行

```powershell
python handwriting_app.py --no-gui --md sample.md --output outputs
```

常用参数：

- `--mode shoot` 或 `--mode print`
- `--formats PNG,PDF,DOCX`
- `--font` 指定字体路径
- `--background-dir` 指定背景图片目录
- `--background-index` 指定背景图序号，`-1` 表示随机
- `--seed` 指定随机种子
- `--export-scale` 指定高清导出倍率，默认 4
- `--export-dpi` 指定导出 DPI，默认 600
- `--quality` 指定 JPG/WEBP 质量，默认 98
- `--worker-threads` 指定并行渲染线程数
- `--use-gpu` 在可用时启用 CuPy/CUDA 后处理

示例：

```powershell
python handwriting_app.py --no-gui --md sample.md --mode print --formats PNG,PDF,DOCX --export-scale 4 --export-dpi 600 --output outputs
```

## 数学字体

如果 `to/out` 里有数学字体，程序会自动按下面优先级使用：

- `PhotoHandMathTuned.ttf`
- `PhotoHandMath.ttf`
- `PhotoHandMath.otf`

## 公式策略

优先用手写字体：

- 中文、英文、数字
- `sin/cos/tan/log/ln/lim` 等函数名
- 普通变量、单位和公式里的说明文字

优先用数学专用字体：

- 希腊字母
- 求和、求积、积分、集合、箭头、几何符号等
- `= + - < > \le \ge \leq \geq \times \div \pm \mp \neq \approx \cdot`

优先用程序草绘线：

- 分数线、根号横线、cases 左大括号
- `%` 和字体没有覆盖的结构线
- 公式不再做整张图片的二次加粗；公式文字/符号、草绘符号、分数线/根号线分别按各自滑块控制。

可调滑块：

- `中文粗细`、`英文粗细`、`数学符号粗细`
- `行间公式居中不齐`
- `符号轴线对齐`
- `结构线波浪`
- `英文扰动系数`、`公式扰动系数`

## 待解决

1. 对`<br>`的支持
2. 希腊字符的支持
3. 公式中，括号的位置不对