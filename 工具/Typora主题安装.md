# typora主题安装

## 第一步：打开Typora官网

[Typora 官方中文站 (typoraio.cn)](https://typoraio.cn/)

* 打开右上角主题

* 找到`Drake`

* 点击Download

阿里云盘备份路径：备份盘/工具/Typora/主题+字体/DrakeTyporaTheme-master

## 第二步：解压

* 解压 DrakeTyporaTheme-master
* 将`drake` and `*.css`放入typora主题文件夹中

## 第三步：修改字体

| 推荐字体                                                     | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [JetBrains Mono](https://www.jetbrains.com/zh-cn/lp/mono/)   | 英文字体, 适合开发人员的字体. 作者修改版本: [JetBrainsMono Patch](https://github.com/liangjingkanji/JetBrainsMono-patch) |
| [Fira Code](https://github.com/tonsky/FiraCode)              | 英文字体, 前端开发人员喜欢用的字体                           |
| [Cascadia Code](https://github.com/microsoft/cascadia-code)  | 英文字体, 微软官方字体, Windows Terminal的默认字体. 作者修改版本: [Cascadia Code Patch](https://github.com/liangjingkanji/cascadia-code-patch) |
| [PTCode](https://github.com/liangjingkanji/PTCode)           | PT Mono 增加连字特性(Ligatures)                              |
| [PlexMono](https://github.com/liangjingkanji/PlexMono)       | IBM Plex Mono 增加连字特性(Ligatures)                        |
| [Iosevka](https://github.com/be5invis/Iosevka)/[Sarasa Gothic](https://github.com/be5invis/Sarasa-Gothic) | Iosevka/更紗黑體, 超窄字间距(稍微过窄)<br />Windows渲染效果明显(希望各位敦促作者将Iosevka其他风格整和进去) |
| [HYYouYuan](https://www.hanyi.com.cn/productdetail?id=10875)/[HYZhengYuan](http://www.hanyi.com.cn/productdetail?id=2915) | 中文字体, 汉仪有圆/汉仪正圆                                  |

* JetBrains Mono: 适合显示代码

  fonts/variable/*.tty

* Cascadia Code: 适合显示命令

  source/tty_data/*.ttf

* HYZhengYuan: 适合显示中文

> tty文件->右键安装
>
> 打开系统字体设置,选择要安装的字体拖安装

阿里云盘备份路径:备份盘/工具/Typora/主题+字体/...

## 第四步：修改font.css

打开Typora主题文件夹

编辑...\Typora\themes\drake\font.css

```css
/*快速自定义配置*/
:root {
    --monospace: "Cascadia Code", "JetBrains Mono", HYZhengYuan;
    /* 字体会根据顺序依次应用, 比如第一个是仅英文字体, 那么就会应用第二个中文字体. 如果第一个字体支持中英文那么就不会应用第二个字体 */
    /* 请保证你有安装你自定义的字体到系统中 */
    --text-font: var(--monospace); /*正文字 体*/
    --title-font: var(--monospace); /*标题字体*/
    --latex-font: var(--monospace); /*LaTeX字体(不含英语)*/
    --text-line-height: 1.6; /*正文行间距*/
    --code-line-height: 1.6; /*代码块行间距*/
    --p-spacing: 0.8rem; /*段间距*/
    --file-tree-text-size: 1.1rem; /*文件树大小*/
    --toc-text-size: 1rem; /*大纲大小*/
    --text-size: 14px; /*字体大小 推荐配置: 应用设置-外观-字体大小*/
}
```

