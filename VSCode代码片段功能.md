
[官方文档](https://code.visualstudio.com/docs/editor/userdefinedsnippets)

有几个入门的：

### [制表位](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_tabstops)

使用制表位，您可以使编辑器光标在代码片段内移动。使用`$1`,`$2`指定光标位置。数字是访问制表符的顺序，而`$0`表示最终的光标位置。同一制表符的多次出现会被链接并同步更新。

### [占位符](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_placeholders)

占位符是带有值的制表位，例如`${1:foo}`. 将插入并选择占位符文本，以便可以轻松更改。占位符可以嵌套，例如`${1:another ${2:placeholder}}`.

### [选择](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_choice)

占位符可以有选择作为值。语法是以逗号分隔的值枚举，用管道字符括起来，例如`${1|one,two,three|}`。插入代码片段并选择占位符后，选项将提示用户选择其中一个值。
