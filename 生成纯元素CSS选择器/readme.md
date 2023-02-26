# 1. 用途

常见的 CSS 选择器：

```
#gatsby-focus-wrapper > div > div > div.Box-nv15kw-0.Flex-arghxi-0.layout___StyledFlex-sc-1qhwq3g-0.iUtsKT.fNYvIR > div.Box-nv15kw-0.iiMOdu > div > div > div > div:nth-child(8) > div > div
```

经过脚本转换后的 CSS 选择器：

```
body > div > div:nth-child(1) > div > div > div:nth-child(2) > div:nth-child(1) > div > div > div > div:nth-child(8) > div > div
```

可以发现转换后的 CSS 选择器只包含元素，这就是这个脚本的目的。

**注意：** 

1. 准备转换的 CSS 选择器不能包含找不到实际元素的伪类和伪元素，如:hover、::after 等，因为脚本需要用 JS 的`document.querySelector`函数在网页中找到对应的元素。只要是正常利用浏览器复制得到的 CSS 选择器都行。CSS 选择器种类参见：https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors
2. 网页中 iframe 里的元素不适用，因为每个嵌入的浏览上下文（embedded browsing context）都有自己的会话历史记录 (session history)和 DOM 树。而 CSS 的作用域是相同的 document，因此不能直接用 CSS 选择器获取到 iframe 里的元素。参见：

  - https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe
  - https://stackoverflow.com/questions/21842373

**补充**：利用浏览器复制 CSS 选择器

1. 首先打开开发者模式
  - Windows 和 Linux：ctrl+ shift+i
  - Mac：command + ⌘ + i

2. 点击下图标识的图标
   ![get-selector-1](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/get-selector-1.png)
3. 鼠标移动到需要选择的网页元素上，可以发现右边的开发者工具中会出现你选中的元素。
   ![get-selector-2](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/get-selector-2.png)
4. 右键点击开发者工具中出现的元素然后按照图中顺序选择复制 selector
   ![get-selector-3](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/get-selector-3.png)

# 2. 安装地址

脚本发布在 greasyfork：https://greasyfork.org/zh-CN/scripts/460714

脚本 Github 仓库文件地址：https://github.com/coycs/Greasy-Fork/blob/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/main.js

如果你只是需要用这个脚本就忽略这个地址，去greasyfork安装就可以用了。

# 3. 使用

（1）有一定的编程基础并且不想安装油猴脚本

获取到目标元素的选择器后，作为函数调用参数填入指定位置，复制全部代码粘贴在浏览器的控制台：

```javascript
const genTagSelector = selector => {
  let targetNode = document.querySelector(selector);
  let tagSelector = "";

  while (targetNode.nodeName != "HTML") {
    const parentNode = targetNode.parentNode;
    const childNodes = Array.from(parentNode.childNodes).filter(
      node => node.nodeName != "#text" && node.nodeName != "#comment"
    );
    const tagName = targetNode.tagName.toLowerCase();
    let nthIndex = 0;

    // 判断父元素下目标元素的标签名是否唯一
    if (childNodes.filter(node => node.tagName.toLowerCase() == tagName).length == 1) {
      if (parentNode.nodeName != "HTML") {
        tagSelector = ` > ${tagName}` + tagSelector;
      } else {
        tagSelector = `html > ${tagName}` + tagSelector;
      }
    } else {
      // 获取nthIndex的序号
      for (let i = 0; i < childNodes.length; i++) {
        if (childNodes[i] === targetNode) {
          nthIndex = i + 1;
          break;
        }
      }

      if (parentNode.nodeName != "HTML") {
        tagSelector = ` > ${tagName}:nth-child(${nthIndex})` + tagSelector;
      } else {
        tagSelector = `html > ${tagName}:nth-child(${nthIndex})` + tagSelector;
      }
    }

    targetNode = parentNode;
  }

  return tagSelector;
};

// 函数传入选择器字符串
// 如：genTagSelector("body > div > p")
genTagSelector("");
```

![use-1](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/use-1.png)

（2）直接使用油猴脚本

按照图中步骤先点击油猴脚本图标（对应 1），然后点击该脚本菜单的按钮“输入选择器”（对应 2）。
![use-2](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/use-2.png)
出现对话框后在输入框中输入想要转换的 CSS 选择器，然后点击“确定”按钮。
![use-3](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/use-3.png)
随后出现一个新的对话框，复制对话框中的内容然后点击“确定”按钮关闭对话框。
![use-4](https://raw.githubusercontent.com/coycs/Greasy-Fork/main/%E7%94%9F%E6%88%90%E7%BA%AF%E5%85%83%E7%B4%A0CSS%E9%80%89%E6%8B%A9%E5%99%A8/imgs/use-4.png)
