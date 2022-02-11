原文链接：[https://juejin.im/post/5cbffe386fb9a0322564c0e5?utm\_medium=hao.caibaojian.com](https://juejin.im/post/5cbffe386fb9a0322564c0e5?utm_medium=hao.caibaojian.com)


CSS3 和 HTML5 新特性一览
==================

CSS3新特性
-------

### CSS3 选择器

| 选择器 | 示例 | 示例说明 | CSS |
| --- | --- | --- | --- |
| [._class_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-class.html) | .intro | 选择所有class="intro"的元素 | 1 |
| [#_id_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-id.html) | #firstname | 选择所有id="firstname"的元素 | 1 |
| [\*](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-all.html) | \* | 选择所有元素 | 2 |
| _[element](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-element.html)_ | p | 选择所有<p>元素 | 1 |
| _[element,element](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-element-comma.html)_ | div,p | 选择所有<div>元素和<p>元素 | 1 |
| [_element_ _element_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-element-element.html) | div p | 选择<div>元素内的所有<p>元素 | 1 |
| [_element_\>_element_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-element-gt.html) | div>p | 选择所有父级是 <div> 元素的 <p> 元素 | 2 |
| [_element_+_element_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-element-pluss.html) | div+p | 选择所有紧接着<div>元素之后的<p>元素 | 2 |
| [\[_attribute_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attribute.html) | \[target\] | 选择所有带有target属性元素 | 2 |
| [\[_attribute_\=_value_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attribute-value.html) | \[target=-blank\] | 选择所有使用target="-blank"的元素 | 2 |
| [\[_attribute_~=_value_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attribute-value-contains.html) | \[title~=flower\] | 选择标题属性包含单词"flower"的所有元素 | 2 |
| [\[_attribute_|=_language_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attribute-value-lang.html) | \[lang|=en\] | 选择一个lang属性的起始值="EN"的所有元素 | 2 |
| [:link](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-link.html) | a:link | 选择所有未访问链接 | 1 |
| [:visited](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-visited.html) | a:visited | 选择所有访问过的链接 | 1 |
| [:active](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-active.html) | a:active | 选择活动链接 | 1 |
| [:hover](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-hover.html) | a:hover | 选择鼠标在链接上面时 | 1 |
| [:focus](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-focus.html) | input:focus | 选择具有焦点的输入元素 | 2 |
| [:first-letter](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-firstletter.html) | p:first-letter | 选择每一个<P>元素的第一个字母 | 1 |
| [:first-line](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-firstline.html) | p:first-line | 选择每一个<P>元素的第一行 | 1 |
| [:first-child](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-firstchild.html) | p:first-child | 指定只有当<p>元素是其父级的第一个子级的样式。 | 2 |
| [:before](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-before.html) | p:before | 在每个<p>元素之前插入内容 | 2 |
| [:after](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-after.html) | p:after | 在每个<p>元素之后插入内容 | 2 |
| [:lang(_language_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-lang.html) | p:lang(it) | 选择一个lang属性的起始值="it"的所有<p>元素 | 2 |
| [_element1_~_element2_](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-gen-sibling.html) | p~ul | 选择p元素之后的每一个ul元素 | 3 |
| [\[_attribute_^=_value_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attr-begin.html) | a\[src^="https"\] | 选择每一个src属性的值以"https"开头的元素 | 3 |
| [\[_attribute_$=_value_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attr-end.html) | a\[src$=".pdf"\] | 选择每一个src属性的值以".pdf"结尾的元素 | 3 |
| [\[_attribute_\*=_value_\]](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-attr-contain.html) | a\[src\*="44lan"\] | 选择每一个src属性的值包含子字符串"44lan"的元素 | 3 |
| [:first-of-type](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-first-of-type.html) | p:first-of-type | 选择每个p元素是其父级的第一个p元素 | 3 |
| [:last-of-type](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-last-of-type.html) | p:last-of-type | 选择每个p元素是其父级的最后一个p元素 | 3 |
| [:only-of-type](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-only-of-type.html) | p:only-of-type | 选择每个p元素是其父级的唯一p元素 | 3 |
| [:only-child](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-only-child.html) | p:only-child | 选择每个p元素是其父级的唯一子元素 | 3 |
| [:nth-child(_n_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-nth-child.html) | p:nth-child(2) | 选择每个p元素是其父级的第二个子元素 | 3 |
| [:nth-last-child(_n_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-nth-last-child.html) | p:nth-last-child(2) | 选择每个p元素的是其父级的第二个子元素，从最后一个子项计数 | 3 |
| [:nth-of-type(_n_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-nth-of-type.html) | p:nth-of-type(2) | 选择每个p元素是其父级的第二个p元素 | 3 |
| [:nth-last-of-type(_n_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-nth-last-of-type.html) | p:nth-last-of-type(2) | 选择每个p元素的是其父级的第二个p元素，从最后一个子项计数 | 3 |
| [:last-child](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-last-child.html) | p:last-child | 选择每个p元素是其父级的最后一个子级。 | 3 |
| [:root](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-root.html) | :root | 选择文档的根元素 | 3 |
| [:empty](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-empty.html) | p:empty | 选择每个没有任何子级的p元素（包括文本节点） | 3 |
| [:target](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-target.html) | #news:target | 选择当前活动的#news元素（包含该锚名称的点击的URL） | 3 |
| [:enabled](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-enabled.html) | input:enabled | 选择每一个已启用的输入元素 | 3 |
| [:disabled](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-disabled.html) | input:disabled | 选择每一个禁用的输入元素 | 3 |
| [:checked](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-checked.html) | input:checked | 选择每个选中的输入元素 | 3 |
| [:not(_selector_)](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-not.html) | :not(p) | 选择每个并非p元素的元素 | 3 |
| [::selection](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-selection.html) | ::selection | 匹配元素中被用户选中或处于高亮状态的部分 | 3 |
| [:out-of-range](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-out-of-range.html) | :out-of-range | 匹配值在指定区间之外的input元素 | 3 |
| [:in-range](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-in-range.html) | :in-range | 匹配值在指定区间之内的input元素 | 3 |
| [:read-write](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-read-write.html) | :read-write | 用于匹配可读及可写的元素 | 3 |
| [:read-only](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-read-only.html) | :read-only | 用于匹配设置 "readonly"（只读） 属性的元素 | 3 |
| [:optional](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-optional.html) | :optional | 用于匹配可选的输入元素 | 3 |
| [:required](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-required.html) | :required | 用于匹配设置了 "required" 属性的元素 | 3 |
| [:valid](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-valid.html) | :valid | 用于匹配输入值为合法的元素 | 3 |
| [:invalid](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fsel-invalid.html) | :invalid | 用于匹配输入值为非法的元素 |

### CSS3 边框（Borders）

**用 CSS3 ，你可以创建圆角边框，添加阴影框，并作为边界的形象而不使用设计程序**  

| 属性 | 说明 | CSS |
| --- | --- | --- |
| [border-image](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-border-image.html) | 设置所有边框图像的速记属性。 | 3 |
| [border-radius](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-border-radius.html) | 一个用于设置所有四个边框- \*-半径属性的速记属性 | 3 |
| [box-shadow](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-box-shadow.html) | 附加一个或多个下拉框的阴影 | 3 |

    div{ 
        border:2px solid; 
        border-radius:25px; 
        box-shadow: 10px 10px 5px #888888; 
        border-image:url(border.png) 30 30 round; 
    }复制代码

### CSS3 背景

CSS3中包含几个新的背景属性，提供更大背景元素控制。

| 顺序 | 描述 | CSS |
| --- | --- | --- |
| [background-clip](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-background-clip.html) | 规定背景的绘制区域。 | 3 |
| [background-origin](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-background-origin.html) | 规定背景图片的定位区域。 | 3 |
| [background-size](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-background-size.html) | 规定背景图片的尺寸。 | 3 |

    div{ 
        background:url(img_flwr.gif); 
        background-repeat:no-repeat; 
        background-size:100% 100%; 
        background-origin:content-box;
    } 
    多背景 
    body{ 
        background-image:url(img_flwr.gif),url(img_tree.gif); 
    }复制代码

### CSS3 渐变

CSS3 定义了两种类型的渐变（gradients）：

*   线性渐变（Linear Gradients）- 向下/向上/向左/向右/对角方向
*   **
    
        background: linear-gradient(direction, color-stop1, color-stop2, ...);复制代码
    
    
    
    **
*   径向渐变（Radial Gradients）- 由它们的中心定义
*   **
    
        background: radial-gradient(center, shape size, start-color, ..., last-color);复制代码
    
    
    
    **

  

### CSS3 文本效果

| 属性 | 描述 | CSS |
| --- | --- | --- |
| [hanging-punctuation](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-hanging-punctuation.html "CSS3 hanging-punctuation 属性") | 规定标点字符是否位于线框之外。 | 3 |
| [punctuation-trim](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-punctuation-trim.html "CSS3 punctuation-trim 属性") | 规定是否对标点字符进行修剪。 | 3 |
| text-align-last | 设置如何对齐最后一行或紧挨着强制换行符之前的行。 | 3 |
| text-emphasis | 向元素的文本应用重点标记以及重点标记的前景色。 | 3 |
| [text-justify](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-text-justify.html "CSS3 text-justify 属性") | 规定当 text-align 设置为 "justify" 时所使用的对齐方法。 | 3 |
| [text-outline](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-text-outline.html "CSS3 text-outline 属性") | 规定文本的轮廓。 | 3 |
| [text-overflow](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-text-overflow.html "CSS3 text-overflow 属性") | 规定当文本溢出包含元素时发生的事情。 | 3 |
| [text-shadow](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-text-shadow.html "CSS3 text-shadow 属性") | 向文本添加阴影。 | 3 |
| [text-wrap](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-text-wrap.html "CSS3 text-wrap 属性") | 规定文本的换行规则。 | 3 |
| [word-break](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-word-break.html "CSS3 word-break 属性") | 规定非中日韩文本的换行规则。 | 3 |
| [word-wrap](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-word-wrap.html "CSS3 word-wrap 属性") | 允许对长的不可分割的单词进行分割并换行到下一行。 | 3 |

### CSS3 字体

以前CSS3的版本，网页设计师不得不使用用户计算机上已经安装的字体。使用CSS3，网页设计师可以使用他/她喜欢的任何字体。当你发现您要使用的字体文件时，只需简单的将字体文件包含在网站中，它会自动下载给需要的用户。您所选择的字体在新的CSS3版本有关于@font-face规则描述。您"自己的"的字体是在 CSS3 @font-face 规则中定义的。

    <style>@font-face{font-family: myFirstFont;src: url(sansation_light.woff);}div{font-family:myFirstFont;}</style>复制代码

  

### CSS3 转换和变形

**2D新转换属性**

以下列出了所有的转换属性:

| Property | 描述 | CSS |
| --- | --- | --- |
| [transform](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transform.html) | 适用于2D或3D转换的元素 | 3 |
| [transform-origin](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transform-origin.html) | 允许您更改转化元素位置 |

**2D 转换方法**

| 函数 | 描述 |
| --- | --- |
| matrix(_n_,_n_,_n_,_n_,_n_,_n_) | 定义 2D 转换，使用六个值的矩阵。 |
| translate(_x_,_y_) | 定义 2D 转换，沿着 X 和 Y 轴移动元素。 |
| translateX(_n_) | 定义 2D 转换，沿着 X 轴移动元素。 |
| translateY(_n_) | 定义 2D 转换，沿着 Y 轴移动元素。 |
| scale(_x_,_y_) | 定义 2D 缩放转换，改变元素的宽度和高度。 |
| scaleX(_n_) | 定义 2D 缩放转换，改变元素的宽度。 |
| scaleY(_n_) | 定义 2D 缩放转换，改变元素的高度。 |
| rotate(_angle_) | 定义 2D 旋转，在参数中规定角度。 |
| skew(_x-angle_,_y-angle_) | 定义 2D 倾斜转换，沿着 X 和 Y 轴。 |
| skewX(_angle_) | 定义 2D 倾斜转换，沿着 X 轴。 |
| skewY(_angle_) | 定义 2D 倾斜转换，沿着 Y 轴。 |

**3D转换属性**

下表列出了所有的转换属性：

| 属性 | 描述 | CSS |
| --- | --- | --- |
| [transform](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transform.html "CSS3 transform 属性") | 向元素应用 2D 或 3D 转换。 | 3 |
| [transform-origin](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transform-origin.html "CSS3 transform-origin 属性") | 允许你改变被转换元素的位置。 | 3 |
| [transform-style](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transform-style.html "CSS3 transform-style 属性") | 规定被嵌套元素如何在 3D 空间中显示。 | 3 |
| [perspective](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-perspective.html "CSS3 perspective 属性") | 规定 3D 元素的透视效果。 | 3 |
| [perspective-origin](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-perspective-origin.html "CSS3 perspective-origin 属性") | 规定 3D 元素的底部位置。 | 3 |
| [backface-visibility](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-backface-visibility.html "CSS3 backface-visibility 属性") | 定义元素在不面对屏幕时是否可见。 | 3 |

**3D 转换方法**

| 函数 | 描述 |
| --- | --- |
| matrix3d(_n_,_n_,_n_,_n_,_n_,_n_,  
_n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_,_n_) | 定义 3D 转换，使用 16 个值的 4x4 矩阵。 |
| translate3d(_x_,_y_,_z_) | 定义 3D 转化。 |
| translateX(_x_) | 定义 3D 转化，仅使用用于 X 轴的值。 |
| translateY(_y_) | 定义 3D 转化，仅使用用于 Y 轴的值。 |
| translateZ(_z_) | 定义 3D 转化，仅使用用于 Z 轴的值。 |
| scale3d(_x_,_y_,_z_) | 定义 3D 缩放转换。 |
| scaleX(_x_) | 定义 3D 缩放转换，通过给定一个 X 轴的值。 |
| scaleY(_y_) | 定义 3D 缩放转换，通过给定一个 Y 轴的值。 |
| scaleZ(_z_) | 定义 3D 缩放转换，通过给定一个 Z 轴的值。 |
| rotate3d(_x_,_y_,_z_,_angle_) | 定义 3D 旋转。 |
| rotateX(_angle_) | 定义沿 X 轴的 3D 旋转。 |
| rotateY(_angle_) | 定义沿 Y 轴的 3D 旋转。 |
| rotateZ(_angle_) | 定义沿 Z 轴的 3D 旋转。 |
| perspective(_n_) | 定义 3D 转换元素的透视视图。 |

### CSS3 过渡

过渡属性

下表列出了所有的过渡属性:

| 属性 | 描述 | CSS |
| --- | --- | --- |
| [transition](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transition.html "CSS3 transition 属性") | 简写属性，用于在一个属性中设置四个过渡属性。 | 3 |
| [transition-property](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transition-property.html "CSS3 transition-property 属性") | 规定应用过渡的 CSS 属性的名称。 | 3 |
| [transition-duration](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transition-duration.html "CSS3 transition-duration 属性") | 定义过渡效果花费的时间。默认是 0。 | 3 |
| [transition-timing-function](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transition-timing-function.html "CSS3 transition-timing-function 属性") | 规定过渡效果的时间曲线。默认是 "ease"。 | 3 |
| [transition-delay](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-transition-delay.html "CSS3 transition-delay 属性") | 规定过渡效果何时开始。默认是 0。 | 3 |

    div{transition-property: width;transition-duration: 1s;transition-timing-function: linear;transition-delay: 2s;/* Safari */-webkit-transition-property:width;-webkit-transition-duration:1s;-webkit-transition-timing-function:linear;-webkit-transition-delay:2s;}复制代码

### CSS3 动画

要创建CSS3动画，你需要了解@keyframes规则。@keyframes规则是创建动画。 @keyframes规则内指定一个CSS样式和动画将逐步从目前的样式更改为新的样式。

### 实例

当动画为 25% 及 50% 时改变背景色，然后当动画 100% 完成时再次改变：

    @keyframes myfirst{0% {background: red;}25% {background: yellow;}50% {background: blue;}100% {background: green;}}复制代码

下面的表格列出了 @keyframes 规则和所有动画属性：

| 属性 | 描述 | CSS |
| --- | --- | --- |
| [@keyframes](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-keyframes.html "CSS3 @keyframes 规则") | 规定动画。 | 3 |
| [animation](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation.html "CSS3 animation 属性") | 所有动画属性的简写属性，除了 animation-play-state 属性。 | 3 |
| [animation-name](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-name.html "CSS3 animation-name 属性") | 规定 @keyframes 动画的名称。 | 3 |
| [animation-duration](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-duration.html "CSS3 animation-duration 属性") | 规定动画完成一个周期所花费的秒或毫秒。默认是 0。 | 3 |
| [animation-timing-function](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-timing-function.html "CSS3 animation-timing-function 属性") | 规定动画的速度曲线。默认是 "ease"。 | 3 |
| [animation-delay](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-delay.html "CSS3 animation-delay 属性") | 规定动画何时开始。默认是 0。 | 3 |
| [animation-iteration-count](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-iteration-count.html "CSS3 animation-iteration-count 属性") | 规定动画被播放的次数。默认是 1。 | 3 |
| [animation-direction](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-direction.html "CSS3 animation-direction 属性") | 规定动画是否在下一周期逆向地播放。默认是 "normal"。 | 3 |
| [animation-play-state](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-animation-play-state.html "CSS3 animation-play-state 属性") | 规定动画是否正在运行或暂停。默认是 "running"。 | 3 |

  

    div{
        animation-name: myfirst;
        animation-duration: 5s;
        animation-timing-function: linear;
        animation-delay: 2s;
        animation-iteration-count: infinite;
        animation-direction: alternate;
        animation-play-state: running;
        /* Safari and Chrome: */
        -webkit-animation-name: myfirst;
        -webkit-animation-duration: 5s;
        -webkit-animation-timing-function: linear;
        -webkit-animation-delay: 2s;
        -webkit-animation-iteration-count: infinite;
        -webkit-animation-direction: alternate;
        -webkit-animation-play-state: running;
    }复制代码

### CSS3 多列

下表列出了所有 CSS3 的多列属性：

| 属性 | 描述 |
| --- | --- |
| [column-count](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-count.html) | 指定元素应该被分割的列数。 |
| [column-fill](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-fill.html) | 指定如何填充列 |
| [column-gap](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-gap.html) | 指定列与列之间的间隙 |
| [column-rule](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-rule.html) | 所有 column-rule-\* 属性的简写 |
| [column-rule-color](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-rule-color.html) | 指定两列间边框的颜色 |
| [column-rule-style](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-rule-style.html) | 指定两列间边框的样式 |
| [column-rule-width](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-rule-width.html) | 指定两列间边框的厚度 |
| [column-span](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-span.html) | 指定元素要跨越多少列 |
| [column-width](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-column-width.html) | 指定列的宽度 |
| [columns](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-columns.html) | 设置 column-width 和 column-count 的简写 |

### CSS3 盒模型

在 CSS3 中, 增加了一些新的用户界面特性来调整元素尺寸，框尺寸和外边框，主要包括以下用户界面属性：

*   resize：none | both | horizontal | vertical | inherit
*   box-sizing: content-box | border-box | inherit
*   outline:outline-color outline-style outline-width outine-offset

resize属性指定一个元素是否应该由用户去调整大小。

box-sizing 属性允许您以确切的方式定义适应某个区域的具体内容。  

outline-offset 属性对轮廓进行偏移，并在超出边框边缘的位置绘制轮廓。  

### CSS3伸缩布局盒模型(弹性盒)

CSS3 弹性盒（ Flexible Box 或 flexbox），是一种当页面需要适应不同的屏幕大小以及设备类型时确保元素拥有恰当的行为的布局方式。

引入弹性盒布局模型的目的是提供一种更加有效的方式来对一个容器中的子元素进行排列、对齐和分配空白空间。

下表列出了在弹性盒子中常用到的属性:

| 属性 | 描述 |
| --- | --- |
| [display](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fpr-class-display.html) | 指定 HTML 元素盒子类型。 |
| [flex-direction](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-flex-direction.html) | 指定了弹性容器中子元素的排列方式 |
| [justify-content](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-justify-content.html) | 设置弹性盒子元素在主轴（横轴）方向上的对齐方式。 |
| [align-items](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-align-items.html) | 设置弹性盒子元素在侧轴（纵轴）方向上的对齐方式。 |
| [flex-wrap](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-flex-wrap.html) | 设置弹性盒子的子元素超出父容器时是否换行。 |
| [align-content](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-align-content.html) | 修改 flex-wrap 属性的行为，类似 align-items, 但不是设置子元素对齐，而是设置行对齐 |
| [flex-flow](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-flex-flow.html) | flex-direction 和 flex-wrap 的简写 |
| [order](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-order.html) | 设置弹性盒子的子元素排列顺序。 |
| [align-self](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-align-self.html) | 在弹性子元素上使用。覆盖容器的 align-items 属性。 |
| [flex](https://link.juejin.im?target=http%3A%2F%2Fwww.runoob.com%2Fcssref%2Fcss3-pr-flex.html) | 设置弹性盒子的子元素如何分配空间。 |

### CSS3 多媒体查询

从 CSS 版本 2 开始，就可以通过媒体类型在 CSS 中获得媒体支持。如果您曾经使用过打印样式表，那么您可能已经使用过媒体类型。清单 1 展示了一个示例。

**清单 1. 使用媒体类型**  

    <link rel="stylesheet" type="text/css" href="site.css" media="screen" /><link rel="stylesheet" type="text/css" href="print.css" media="print" />复制代码

**清单 2. 媒体查询规则**  

    @media all and (min-width: 800px) { ... }复制代码

*   `@media all` 是媒体类型，也就是说，将此 CSS 应用于所有媒体类型。

*   `(min-width:800px)` 是包含媒体查询的表达式，如果浏览器的最小宽度为 800 像素，则会告诉浏览器只运用下列 CSS。

**清单 3.** `**and**` **条件**

    @media (min-width:800px) and (max-width:1200px) and (orientation:portrait) { ... }复制代码

  

**清单 4.** `**or**` **关键词**  

    @media (min-width:800px) or (orientation:portrait) { ... }复制代码

  

**清单 5. 使用** `**not**`  

    @media (not min-width:800px) { ... }复制代码

  

  

HTML5 的新特性：
-----------

为了更好地处理今天的互联网应用，HTML5添加了很多新元素及功能，比如: 图形的绘制，多媒体内容，更好的页面结构，更好的形式 处理，和几个api拖放元素，定位，包括网页 应用程序缓存，存储，网络工作者，等

### （一） 语义标签

　语义化标签使得页面的内容结构化，见名知义

<table><tbody><tr><td>标签</td><td>描述</td></tr><tr><td>&lt;hrader&gt;&lt;/header&gt;</td><td>定义了文档的头部区域</td></tr><tr><td>&lt;footer&gt;&lt;/footer&gt;</td><td>定义了文档的尾部区域</td></tr><tr><td>&lt;nav&gt;&lt;/nav&gt;</td><td>定义文档的导航</td></tr><tr><td>&lt;section&gt;&lt;/section&gt;</td><td>定义文档中的节（section、区段）</td></tr><tr><td>&lt;article&gt;&lt;/article&gt;</td><td>定义页面独立的内容区域</td></tr><tr><td>&lt;aside&gt;&lt;/aside&gt;</td><td>定义页面的侧边栏内容</td></tr><tr><td>&lt;detailes&gt;&lt;/detailes&gt;</td><td>用于描述文档或文档某个部分的细节</td></tr><tr><td>&lt;summary&gt;&lt;/summary&gt;</td><td>标签包含 details 元素的标题</td></tr><tr><td>&lt;dialog&gt;&lt;/dialog&gt;</td><td>定义对话框，比如提示框</td></tr></tbody></table>

### （二）增强型表单

　HTML5 拥有多个新的表单 Input 输入类型。这些新特性提供了更好的输入控制和验证。

<table><tbody><tr><td><p>输入类型</p></td><td>描述</td></tr><tr><td><p>color</p></td><td>主要用于选取颜色</td></tr><tr><td><p>date</p></td><td>从一个日期选择器选择一个日期</td></tr><tr><td><p>datetime</p></td><td>选择一个日期（UTC 时间）</td></tr><tr><td><p>datetime-local</p></td><td>选择一个日期和时间 (无时区)</td></tr><tr><td><p>email</p></td><td>包含 e-mail 地址的输入域</td></tr><tr><td><p>month</p></td><td>选择一个月份</td></tr><tr><td><p>number</p></td><td>数值的输入域</td></tr><tr><td><p>range</p></td><td>一定范围内数字值的输入域</td></tr><tr><td><p>search</p></td><td>用于搜索域</td></tr><tr><td><p>tel</p></td><td>定义输入电话号码字段</td></tr><tr><td><p>time</p></td><td>选择一个时间</td></tr><tr><td><p>url</p></td><td>URL 地址的输入域</td></tr><tr><td><p>week</p></td><td>选择周和年</td></tr></tbody></table>

　　　HTML5 也新增以下表单元素

<table><tbody><tr><td>表单元素</td><td>描述</td></tr><tr><td><p>&lt;datalist&gt;</p></td><td><p>元素规定输入域的选项列表</p><p>使用 &lt;input&gt; 元素的 list 属性与 &lt;datalist&gt; 元素的 id 绑定</p></td></tr><tr><td><p>&lt;keygen&gt;</p></td><td><p>提供一种验证用户的可靠方法</p><p>标签规定用于表单的密钥对生成器字段。</p></td></tr><tr><td><p>&lt;output&gt;</p></td><td><p>用于不同类型的输出</p><p>比如计算或脚本输出</p></td></tr></tbody></table>

  

HTML5 新增的表单属性

*   placehoder 属性，简短的提示在用户输入值前会显示在输入域上。即我们常见的输入框默认提示，在用户输入后消失。  
    
*   required 属性，是一个 boolean 属性。要求填写的输入域不能为空  
    
*   pattern 属性，描述了一个正则表达式用于验证<input> 元素的值。  
    
*   min 和 max 属性，设置元素最小值与最大值。  
    
*   step 属性，为输入域规定合法的数字间隔。  
    
*   height 和 width 属性，用于 image 类型的 <input> 标签的图像高度和宽度。  
    
*   autofocus 属性，是一个 boolean 属性。规定在页面加载时，域自动地获得焦点。  
    
*   multiple 属性 ，是一个 boolean 属性。规定<input> 元素中可选择多个值。　　　

### （三）视频和音频

*   HTML5 提供了播放音频文件的标准，即使用 <audio> 元素
    
    <table><tbody><tr><td><div>1</div><div>2</div><div>3</div><div>4</div><div>5</div></td><td><div><div><code>&lt;audio controls&gt;</code></div><div><code></code><code>&lt;source src=</code><code>"horse.ogg"</code> <code>type=</code><code>"audio/ogg"</code><code>&gt;</code></div><div><code></code><code>&lt;source src=</code><code>"horse.mp3"</code> <code>type=</code><code>"audio/mpeg"</code><code>&gt;</code></div><div><code>您的浏览器不支持 audio 元素。</code></div><div><code>&lt;/audio&gt;</code></div></div></td></tr></tbody></table>
    

　control 属性供添加播放、暂停和音量控件。

　在<audio> 与 </audio> 之间你需要插入浏览器不支持的<audio>元素的提示文本 。

　<audio> 元素允许使用多个 <source> 元素. <source> 元素可以链接不同的音频文件，浏览器将使用第一个支持的音频文件

　目前, <audio>元素支持三种音频格式文件: MP3, Wav, 和 Ogg

*   HTML5 规定了一种通过 video 元素来包含视频的标准方法。
    
    <table><tbody><tr><td><div>1</div><div>2</div><div>3</div><div>4</div><div>5</div></td><td><div><div><code>&lt;video width=</code><code>"320"</code> <code>height=</code><code>"240"</code> <code>controls&gt;</code></div><div><code></code><code>&lt;source src=</code><code>"movie.mp4"</code> <code>type=</code><code>"video/mp4"</code><code>&gt;</code></div><div><code></code><code>&lt;source src=</code><code>"movie.ogg"</code> <code>type=</code><code>"video/ogg"</code><code>&gt;</code></div><div><code>您的浏览器不支持Video标签。</code></div><div><code>&lt;/video&gt;</code></div></div></td></tr></tbody></table>
    
    control 提供了 播放、暂停和音量控件来控制视频。也可以使用dom操作来控制视频的播放暂停，如 play() 和 pause() 方法。
    
    同时 video 元素也提供了 width 和 height 属性控制视频的尺寸.如果设置的高度和宽度，所需的视频空间会在页面加载时保留。如果没有设置这些属性，浏览器不知道大小的视频，浏览器就不能再加载时保留特定的空间，页面就会根据原始视频的大小而改变。
    
    与 标签之间插入的内容是提供给不支持 video 元素的浏览器显示的。
    
    video 元素支持多个source 元素. 元素可以链接不同的视频文件。浏览器将使用第一个可识别的格式（ MP4, WebM, 和 Ogg）
    

### （四）Canvas绘图

　标签只是图形容器，必须使用脚本来绘制图形。

**Canvas - 图形**  

1.  创建一个画布，一个画布在网页中是一个矩形框，通过 <canvas> 元素来绘制。默认情况下 元素没有边框和内容。
    
    <table><tbody><tr><td><div>1</div></td><td><div><div><code>&lt;canvas id=</code><code>"myCanvas"</code> <code>width=</code><code>"200"</code> <code>height=</code><code>"100"</code> <code>style=</code><code>"border:1px solid #000000;"</code><code>&gt;&lt;/canvas&gt;</code></div></div></td></tr></tbody></table>
    
    　　标签通常需要指定一个id属性 (脚本中经常引用), width 和 height 属性定义的画布的大小，使用 style 属性来添加边框。你可以在HTML页面中使用多个 <canvas> 元素
    
2.  使用Javascript来绘制图像，canvas 元素本身是没有绘图能力的。所有的绘制工作必须在 JavaScript 内部完成
    
    <table><tbody><tr><td><div>1</div><div>2</div><div>3</div><div>4</div><div>5</div><div>6</div></td><td><div><div><code>&lt;script&gt;</code></div><div><code>　　</code><code>var</code> <code>c=document.getElementById(</code><code>"myCanvas"</code><code>);</code></div><div><code>　　</code><code>var</code> <code>ctx=c.getContext(</code><code>"2d"</code><code>);</code></div><div><code>　　ctx.fillStyle=</code><code>"#FF0000"</code><code>;</code></div><div><code>　　ctx.fillRect(0,0,150,75);</code></div><div><code>&lt;/script&gt;</code></div></div></td></tr></tbody></table>
    
    　　getContext("2d") 对象是内建的 HTML5 对象，拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。
    
    　　设置 fillStyle 属性可以是CSS颜色，渐变，或图案。fillStyle默认设置是#000000（黑色）。fillRect(x,y,width,height) 方法定义了矩形当前的填充方式。意思是：在画布上绘制 150x75 的矩形，从左上角开始 (0,0)。　
    

**Canvas - 路径**  

在Canvas上画线，我们将使用以下两种方法：

moveTo(x,y) 定义线条开始坐标

lineTo(x,y) 定义线条结束坐标

绘制线条我们必须使用到 "ink" 的方法，就像stroke()

    var c=document.getElementById("myCanvas");
    var ctx=c.getContext("2d");
    ctx.moveTo(0,0);
    ctx.lineTo(200,100);
    ctx.stroke();复制代码

定义开始坐标(0,0), 和结束坐标 (200,100). 然后使用 stroke() 方法来绘制线条

**Canvas - 文本**  

使用 canvas 绘制文本，重要的属性和方法如下：

　　font - 定义字体

　　fillText(text,x,y) - 在 canvas 上绘制实心的文本

　　strokeText(text,x,y) - 在 canvas 上绘制空心的文本

     使用 fillText():
    var c=document.getElementById("myCanvas");
    var ctx=c.getContext("2d");ctx.font="30px Arial";
    ctx.fillText("Hello World",10,50);复制代码

使用 "Arial" 字体在画布上绘制一个高 30px 的文字（实心）

**Canvas - 渐变**  

渐变可以填充在矩形, 圆形, 线条, 文本等等, 各种形状可以自己定义不同的颜色。

以下有两种不同的方式来设置Canvas渐变：

createLinearGradient(x,y,x1,y1) - 创建线条渐变

createRadialGradient(x,y,r,x1,y1,r1) - 创建一个径向/圆渐变

当我们使用渐变对象，必须使用两种或两种以上的停止颜色。

addColorStop()方法指定颜色停止，参数使用坐标来描述，可以是0至1.

    使用渐变，设置fillStyle或strokeStyle的值为渐变，然后绘制形状，如矩形，文本，或一条线。
    var c=document.getElementById("myCanvas");
    var ctx=c.getContext("2d");
    // Create gradient
    var grd=ctx.createLinearGradient(0,0,200,0);
    grd.addColorStop(0,"red");
    grd.addColorStop(1,"white");
    // Fill with gradientctx.fillStyle=grd;
    ctx.fillRect(10,10,150,80);复制代码

　　创建了一个线性渐变，使用渐变填充矩形

**Canvas - 图像**

    把一幅图像放置到画布上, 使用 drawImage(image,x,y) 方法
    var c=document.getElementById("myCanvas");
    var ctx=c.getContext("2d");
    var img=document.getElementById("scream");
    ctx.drawImage(img,10,10); 复制代码

把一幅图像放置到了画布上

### （五）SVG绘图

SVG是指可伸缩的矢量图形

**SVG 与 Canvas两者间的区别**

SVG 是一种使用 XML 描述 2D 图形的语言。

Canvas 通过 JavaScript 来绘制 2D 图形。

SVG 基于 XML，这意味着 SVG DOM 中的每个元素都是可用的。您可以为某个元素附加 JavaScript 事件处理器。

在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

Canvas 是逐像素进行渲染的。在 canvas 中，一旦图形被绘制完成，它就不会继续得到浏览器的关注。如果其位置发生变化，那么整个场景也需要重新绘制，包括任何或许已被图形覆盖的对象。

### （六）地理定位

HTML5 Geolocation（地理定位）用于定位用户的位置。

    window.navigator.geolocation {
        getCurrentPosition:  fn  用于获取当前的位置数据
        watchPosition: fn  监视用户位置的改变
        clearWatch: fn  清除定位监视
    }　　　复制代码

　　获取用户定位信息：

    navigator.geolocation.getCurrentPosition(    
        function(pos){
            console.log('用户定位数据获取成功')　　　　
            console.log(arguments);　　　　
            console.log('定位时间：',pos.timestamp)　　　　
            console.log('经度：',pos.coords.longitude)　　　　
            console.log('纬度：',pos.coords.latitude)　　　　
            console.log('海拔：',pos.coords.altitude)　　　　
            console.log('速度：',pos.coords.speed)
        },   
         //定位成功的回调function(err){ console.log('用户定位数据获取失败')　　　　
         //console.log(arguments);
    }        
    //定位失败的回调复制代码

### （七）拖放API

　拖放是一种常见的特性，即抓取对象以后拖到另一个位置。在 HTML5 中，拖放是标准的一部分，任何元素都能够拖放。

　　拖放的过程分为源对象和目标对象。源对象是指你即将拖动元素，而目标对象则是指拖动之后要放置的目标位置。

**拖放****的源对象****(可能****发生移动的****)****可以触发的事件****——3个**：

dragstart：拖动开始

drag：拖动中

dragend：拖动结束

整个拖动过程的组成： dragstart\*1 + drag\*n + dragend\*1

**拖放****的****目标****对象****(不会****发生移动****)****可以触发的事件****——4个**：

dragenter：拖动着进入

dragover：拖动着悬停

dragleave：拖动着离开

drop：释放

整个拖动过程的组成1： dragenter\*1 + dragover\*n + dragleave\*1

整个拖动过程的组成2： dragenter\*1 + dragover\*n + drop\*1

**dataTransfer：用于数据传递的“拖拉机”对象；**

在拖动源对象事件中使用e.dataTransfer属性保存数据：

e.dataTransfer.setData( k, v )

在拖动目标对象事件中使用e.dataTransfer属性读取数据：

var value = e.dataTransfer.getData( k )

### （八） WebWorker

　当在 HTML 页面中执行脚本时，页面的状态是不可响应的，直到脚本已完成。

　　web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。您可以继续做任何愿意做的事情：点击、选取内容等等，而此时 web worker 在后台运行。

　　首先检测浏览器是否支持 Web Worker

    if(typeof(Worker)!=="undefined"){
        // 是的! Web worker 支持!
        // 一些代码.....
    }else{
        // //抱歉! Web Worker 不支持
    }复制代码

　　下面的代码检测是否存在 worker，如果不存在，- 它会创建一个新的 web worker 对象，然后运行 "demo\_workers.js" 中的代码

    if(typeof(w)=="undefined"){w=new Worker("demo_workers.js");}复制代码

　　然后我们就可以从 web worker 发送和接收消息了。向 web worker 添加一个 "onmessage" 事件监听器：

    w.onmessage=function(event){document.getElementById("result").innerHTML=event.data;};复制代码

　　当 web worker 传递消息时，会执行事件监听器中的代码。event.data 中存有来自 event.data 的数据。当我们创建 web worker 对象后，它会继续监听消息（即使在外部脚本完成之后）直到其被终止为止。

如需终止 web worker，并释放浏览器/计算机资源，使用 terminate() 方法。

　**完整的 Web Worker 实例代码**

    <!DOCTYPE html>
    <html>
    <body>
    
    <p>Count numbers: <output id="result"></output></p>
    <button onclick="startWorker()">Start Worker</button> 
    <button onclick="stopWorker()">Stop Worker</button>
    <br><br>
    
    <script>
    var w;
    
    function startWorker()
    {
    if(typeof(Worker)!=="undefined")
    {
      if(typeof(w)=="undefined")
        {
        w=new Worker("demo_workers.js");
        }
      w.onmessage = function (event) {
        document.getElementById("result").innerHTML=event.data;
      };
    }
    else
    {
    document.getElementById("result").innerHTML="Sorry, your browser does not support Web Workers...";
    }
    }
    
    function stopWorker()
    { 
    w.terminate();
    }
    </script>
    
    </body>
    </html>复制代码

　　创建的计数脚本，该脚本存储于 "demo\_workers.js" 文件中

    var i=0;
    
     function timedCount()
     {
     i=i+1;
     postMessage(i);
     setTimeout("timedCount()",500);
     }
    
     timedCount(); 复制代码

### （九） WebStorage

　使用HTML5可以在本地存储用户的浏览数据。早些时候,本地存储使用的是cookies。但是Web 存储需要更加的安全与快速. 这些数据不会被保存在服务器上，但是这些数据只用于用户请求网站数据上.它也可以存储大量的数据，而不影响网站的性能。数据以 键/值 对存在, web网页的数据只允许该网页访问使用。

客户端存储数据的两个对象为：

*   localStorage - 没有时间限制的数据存储  
    
*   sessionStorage - 针对一个 session 的数据存储, 当用户关闭浏览器窗口后，数据会被删除。  
    

　　在使用 web 存储前,应检查浏览器是否支持 localStorage 和sessionStorage

    if(typeof(Storage)!=="undefined")
       {
       // 是的! 支持 localStorage  sessionStorage 对象!
       // 一些代码.....
       }
     else
       {
       // 抱歉! 不支持 web 存储。
       }复制代码

不管是 localStorage，还是 sessionStorage，可使用的API都相同，常用的有如下几个（以localStorage为例）：

*   保存数据：localStorage.setItem(key,value);
*   读取数据：localStorage.getItem(key);
*   删除单个数据：localStorage.removeItem(key);
*   删除所有数据：localStorage.clear();
*   得到某个索引的key：localStorage.key(index);

  

### （十）WebSocket

　WebSocket是HTML5开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。在WebSocket API中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。浏览器通过 JavaScript 向服务器发出建立 WebSocket 连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。当你获取 Web Socket 连接后，你可以通过 **send()** 方法来向服务器发送数据，并通过 **onmessage** 事件来接收服务器返回的数据。

    <!DOCTYPE HTML>
    <html>
       <head>
       <meta charset="utf-8">
       <title>W3Cschool教程(w3cschool.cn)</title>
        
          <script type="text/javascript">
             function WebSocketTest()
             {
                if ("WebSocket" in window)
                {
                   alert("您的浏览器支持 WebSocket!");
                   
                   // 打开一个 web socket
                   var ws = new WebSocket("ws://localhost:9998/echo");
                    
                   ws.onopen = function()
                   {
                      // Web Socket 已连接上，使用 send() 方法发送数据
                      ws.send("发送数据");
                      alert("数据发送中...");
                   };
                    
                   ws.onmessage = function (evt) 
                   { 
                      var received_msg = evt.data;
                      alert("数据已接收...");
                   };
                    
                   ws.onclose = function()
                   { 
                      // 关闭 websocket
                      alert("连接已关闭..."); 
                   };
                }
                
                else
                {
                   // 浏览器不支持 WebSocket
                   alert("您的浏览器不支持 WebSocket!");
                }
             }
          </script>
            
       </head>
       <body>
       
          <div id="sse">
             <a href="javascript:WebSocketTest()">运行 WebSocket</a>
          </div>
          
       </body>
    </html>复制代码