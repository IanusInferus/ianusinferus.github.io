---
layout: default
---

# 如何在Aegisub中实现ruby功能

Ianus Inferus

2021-05-22

[Aegisub](https://github.com/Aegisub/Aegisub)是一个开源ASS字幕编辑软件。

有时候我们会遇到在字幕中实现ruby功能的需求，比如说日文中常见的<ruby><rb>振</rb><rt>ふ</rt></ruby>り<ruby><rb>仮</rb><rt>が</rt></ruby><ruby><rb>名</rb><rt>な</rt></ruby>或者中文中的<ruby><rb>拼</rb><rt>pīn</rt></ruby><ruby><rb>音</rb><rt>yīn</rt></ruby>。

Aegisub本身是支持ruby功能的，但是只支持在卡拉OK功能中使用，如果需要单独使用，仍然需要理解卡拉OK功能。

![aegisub_01.jpg](aegisub_01.jpg)

本文简述Aegisub中卡拉OK功能的使用方法和如何将其用于单纯的ruby功能。

## 卡拉OK功能

![aegisub_02.jpg](aegisub_02.jpg)

Aegisub的[卡拉OK功能](https://aegi.vmoe.info/docs/3.0/Furigana_karaoke/)是为显示歌词节拍时进行文字变色而设计的。

使用方法为，首先设置syl和furi模板注释，然后填写卡拉OK文本注释，最后运行Apply karaoke template生成对应的fx特效文本。

### 设置syl和furi模板注释

![aegisub_03.jpg](aegisub_03.jpg)

首先添加两行，并设置为注释。

在样式处填入目标样式(如Karaoke)。

在特效处分别填入template noblank syl和template noblank furi，分别表示正常文本模板和ruby文本模板。template后可以加syl、furi、noblank或者其组合，noblank表示不会自动将特效为空的文本改为karaoke文本。模板的详细文档可参考[Karaoke Templater](https://aegi.vmoe.info/docs/3.1/Automation/Karaoke_Templater/)。

在文本中填入如下命令，其中\an5表示将行的对齐方式改为上下左右都居中，\pos(...)表示修改字幕位置，\kf...表示设置卡拉OK的两种颜色从次要颜色逐渐扫描填充为主要颜色的显示时间(如果后面不跟文字，则只停留时间没有效果，另有\k...表示等待一段时间文字整体变色，总称为\k tag)，!...!为计算表达式，如果需要实现更复杂的效果，需要修改这个命令，详细可参考[Aegisub ASS Tags](https://aegi.vmoe.info/docs/3.1/ASS_Tags/)。

    {\an5\pos($center,$middle)\kf!syl.start_time/10!\kf$kdur}

### 填写卡拉OK文本注释

![aegisub_04.jpg](aegisub_04.jpg)

首先将对应文本的起止时间设置后，在频谱图上鼠标左键设置起始时间、右键设置终止时间。

将文本改为注释。

将样式设置为和模板的样式相同。如果不存在模板的样式，将无法显示。

将特效设置为karaoke。

填入歌词，如下。

    回|ま#|わる空|そ#|らうさぎ

选择切换卡拉OK模式(工具栏最右边的麦克风和音符的图标)，在每个音的位置点击鼠标左键进行切分，再点击一次取消该位置。

在上方频谱图中左右拖动黄色虚线标定音节位置。鼠标左键选择音节，空格播放改音节。

点击卡拉OK栏中的绿色勾提交音节。

![aegisub_05.jpg](aegisub_05.jpg)

可以得到如下文本，其中{\k...}表示后面的音节长度为...厘秒，“\|”用于分隔汉字和假名(或主要文本和ruby文本，另有变种“\|\<”用于阻止两个汉字的假名连接在一起，需要标注在汉字的第一个假名上)，“#”用于标注一个汉字的第二个及以后的音节。

    {\k33}回|ま{\k71}#|わ{\k56}る{\k87}空|そ{\k84}#|ら{\k57}う{\k90}さ{\k49}ぎ

点击工具栏最下方的绿色勾提交文本。

### 运行Apply karaoke template

点击菜单-自动化-Apply karaoke template，即可生成带fx特效的文本。

![aegisub_06.jpg](aegisub_06.jpg)

可以看到每个\k tag和它后面跟的字都已经拆分并设置好执行时间了。

![aegisub_07.jpg](aegisub_07.jpg)

如果想要修改字体颜色或其他样式，可以在菜单-字幕-样式管理器中修改。主要颜色在音节开始后显示，次要颜色在音节开始前显示。运行Apply karaoke template后会自动生成每个样式的furigana版本，为原始样式将字体大小改为一半得到，生成后可以单独修改样式。

## 如何实现非卡拉OK的ruby文本

非卡拉OK的文本，可以看成是不变色的卡拉OK文本。因此我们可以将syl和furi模板内容修改为如下命令即不会再变色。

    {\an5\pos($center,$middle)}

另外卡拉OK文本注释中的每个音节时长也不再需要，可以全部改为{\k1}。多个字可以连接到一起，但文本的最开头以及假名和下一个汉字分隔的时候仍然必须有{\k1}。以下为三条带ruby文本的例子。

    {\k1}振|ふ{\k1}り{\k1}仮|が{\k1}名|<な
    {\k1}连写的{\k1}拼|pīn{\k1}音|yīn{\k1}  断开的{\k1}拼|pīn{\k1}音|<yīn
    {\k1}とある科学の{\k1}超電磁砲|レールガン

效果如下。

![aegisub_08.jpg](aegisub_08.jpg)

## 示例

下载地址 [aegisub_ruby_example.ass](aegisub_ruby_example.ass)

## 修改记录

2021-10-12 将模板中的\k改为更常用的\kf，增加对“\|\<”的说明，更新对应的图片。
