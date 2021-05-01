---
title: fluid主题使用指南
tags:

---

主要使用来记录一些fluid的简单操作

- 博客图片：使用的图片如果是本地图片，目录文件夹可自定义，但必须在source目录下，博客与主题的source目录最终会合并，因此优先选择博客的source，如路径（/image/xxxx.jpg）

- 文章摘要：文章摘要默认开启，若要手动指定摘要，使用`<!-- more -->`在MD文档里划分，例如：
  
  ```md
  正文的一部分作为摘要
  <!-- more -->
  余下的正文
  ```
  
  或者在Front-matter里设置 `excerpt` 字段，如：
  
  ```md
  ---
  title: 这是标题
  excerpt: 这是摘要
  ---
  ```
  
  优先级: 手动摘要 > 自动摘要
  
  如果关闭自动摘要，并且没有设置手动摘要，摘要区域空白
  
  无论哪种摘要都最多显示 3 行，当屏幕宽度不足时会隐藏部分摘要

- 隐藏文章：如果想把某些文章隐藏起来，不在首页和其他分类里展示，可以在文章开头Front-matter中配置 `hide: true` 属性。

- 文章排序：如果想手动将某些文章固定在首页靠前的位置，可以在安装 `hexo-generator-index` >= 2.0.0 版本的情况下，在文章开头Front-matter中配置 `sticky` 属性

- 文章封面图：通过`index_img`指定

- 文章页顶部大图：默认显示**主题配置**中的 `post.banner_img`，如需要设置单个文章的 Banner，在Front-matter中指定 `banner_img` 属性

- 脚注功能：主题内置了脚注语法支持，可以在文章末尾自动生成带有锚点的脚注，该功能在主题配置中默认开启。脚注语法：
  
  ```md
  这是一句话[^1]
  [^1]: 这是对应的脚注
  ```
  
  更优雅的使用方式，是将脚注写在文末，比如：
  
  ```md
  正文
  
  ## 参考
  [^1]: 参考资料1
  [^2]: 参考资料2
  ```

- 便签：在 markdown 中加入如下的代码来使用便签：
  
  ```md
  {% note success %}
  文字 或者 `markdown` 均可
  {% endnote %}
  ```
  
  或者使用 HTML 形式：
  
  ```html
  <p class="note note-primary">标签</p>
  ```
  
  可选标签有7种

- 行内标签：在 markdown 中加入如下的代码来使用 Label：
  
  ```md
  {% label primary @text %}
  ```
  
  或者使用HTML形式：
  
  ```html
  <span class="label label-primary">Label</span>
  ```
  
  有六种可选标签

- 勾选框：在 markdown 中加入如下的代码来使用 Checkbox：
  
  ```md
  {% cb text, checked?, incline? %}
  ```
  
  text：显示的文字
  checked：默认是否已勾选，默认 false 
  incline: 是否内联（可以理解为后面的文字是否换行），默认 false

- 按钮：你可以在 markdown 中加入如下的代码来使用 Button：
  
  ```md
  {% btn url, text, title %}
  ```
  
  或者使用 HTML 形式：
  
  ```html
  <a class="btn" href="url" title="title">text</a>
  ```
  
  url：跳转链接 
  text：显示的文字 
  title：鼠标悬停时显示的文字（可选）

- 组图：如果想把多张图片按一定布局组合显示，你可以在 markdown 中按如下格式：
  
  ```md
  {% gi total n1-n2-... %}
    ![](url)
    ![](url)
    ![](url)
    ![](url)
    ![](url)
  {% endgi %}
  ```
  
  total：图片总数量，对应中间包含的图片 url 数量 
  n1-n2-...：每行的图片数量，可以省略，默认单行最多 3 张图，求和必须相等于 total，否则按默认样式

- `tags`：指定标签
- `keywords`：指定检索关键字
- `description`：指定描述
- `categories`：指定类目
- `date`：指定文件创建时间
- `updated`：指定文章最后更新时间


- secondary用于展示更新日期

- info用于随笔
- success用于转载、阅读
- primary用于项目
