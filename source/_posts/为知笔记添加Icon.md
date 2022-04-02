title: 美化为知笔记
category: 软件
tags: 
  - 为知笔记
  - 软件
date: <文章日期> [YYYY-MM-DD]
index_img: /images/97vI56lpCl6AZ54duvBPpBtbqYRtcUIjr-p8x7RURbU.png

---

为知笔记通过自定义样式添加Icon

<!--more-->

# 美化为知笔记
为知笔记是目前自己使用过的笔记软件（曾使用过Notion、思源、Trilium、有道云、印象笔记等等）中私有化部署最好用的一款笔记软件，但是相比于Notion，他的样式并不是很好看。

不过自从V0.1.19版本开始，为知笔记将支持自定义样式，这对于了解css的前端er来说无疑是个好消息。具体如何导入自定义样式请看 [这里](https://www.wiz.cn/zh-cn/wiz-new-desktop-custom.html) 。本篇只介绍如何给文章添加Icon。

![image](/images/97vI56lpCl6AZ54duvBPpBtbqYRtcUIjr-p8x7RURbU.png)

Css样式代码

```css
/* 在最左面，将所有“测试”开头的节点背景改为蓝色 */
.wiznote-left-pane [label-text^="测试"] {
  background-color: blue;
}

/*
.wiznote-middle-pane .virtual-list-item .wiznote-note-item [title-text^="迭代"] {
  background-color: #1890ff;
  color: #fefefe !important;
}
*/

/* 在笔记列表，将所有标题为“wiznote.custom"开头的笔记背景改为绿色 */
.wiznote-note-item [title-text^="wiznote.custom"] .wiznote-note-item-container {
  background-color: green;
  border-radius: 8px;
}

/* 在笔记列表，将所有标题为“wiznote.custom"开头的笔记字体改为黄色 */
.wiznote-note-item [title-text^="wiznote.custom"] .wiznote-note-item-container p {
  color: yellow;
}

/* 笔记列表更紧凑 */
.wiznote-note-item-container {
  padding: 2px;
}

.wiznote-note-item div[draggable="true"] {
  padding: 0;
}

.wiznote-note-item-title {
  margin: 0;
}

.editor-main .editor-quote-block .block-content{
  border-radius: 3px 0 0 3px;
}

/* 可用Icon  🔗 📁 🌞 📒 📄 📖 ❤ ✔ ✨ 🎉 🎁 📌 */

/* 链接内部文章Icon */
.editor-main .editor-block .box-wiki-link-name:before {
    content: '🔗';
    margin-right:5px;
}

/* 文件默认Icon*/
.wiznote-note-item-title-text:before{
    content:'📄';
    margin-right:5px;
}

/* 默认文件夹Icon */
p.MuiTypography-root.jss177.jss198.wiznote-label.MuiTypography-body2:before{
     content:'📁';
}

/* 日常 */
.wiznote-folder-tree-item[label-text="日常"] p.MuiTypography-root.jss177.jss198.wiznote-label.MuiTypography-body2:before{
     content:'🌞';
}

```


最终效果

![image](/images/CRjrkKVSV521TXYlUn2gS8F-9Nn0pbBrTk2vRkLF2Wg.png)



