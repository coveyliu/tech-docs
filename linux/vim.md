

<!-- TOC -->

- [.1. 快捷键](#1-%E5%BF%AB%E6%8D%B7%E9%94%AE)
    - [.1.1. 一般模式（commond mode](#11-%E4%B8%80%E8%88%AC%E6%A8%A1%E5%BC%8Fcommond-mode)
    - [.1.2. 编辑模式](#12-%E7%BC%96%E8%BE%91%E6%A8%A1%E5%BC%8F)

<!-- /TOC -->

  
## .1. 快捷键
<a id="markdown-%E5%BF%AB%E6%8D%B7%E9%94%AE" name="%E5%BF%AB%E6%8D%B7%E9%94%AE"></a>


### .1.1. 一般模式（commond mode)
<a id="markdown-%E4%B8%80%E8%88%AC%E6%A8%A1%E5%BC%8F%EF%BC%88commond-mode" name="%E4%B8%80%E8%88%AC%E6%A8%A1%E5%BC%8F%EF%BC%88commond-mode"></a>

==移动光标的快捷键==

| 命令      | 说明                               |
| --------- | ---------------------------------- |
| ctrl + f  | 向下翻一页，相当于 [page donw] 键  |
| ctrl + b  | 向上翻一页，相当于 page up 键      |
| 0 或 Home | 光标所在列，移动到最前面一个字符处 |
| $ 或 End  | 移动到光标所在列的最后面一个字符处 |
| G         | 移动到文件最后一列                 |
| gg        | 移动到文件的第一列                 |

==查找和替换==

| 命令  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| /word | 向光标之下查找 word 字符串                                   |
| ?word | 向光标之上查找 word 字符串                                   |
| n     | 重复前一个查询动作。比如，如果上一个查询动作是 /word，则继续向下查找<br />如果前一个查询动作是 ?word，则继续向上查找 |
| N     | 和 n 相反                                                    |

<img width=90% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220522111505.png'>



==删除、复制和粘贴==

| 命令     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| dd       | 删除光标所在的一列                                           |
| ndd      | 删除光标向下的 n 列                                          |
| yy       | 复制光标所在列                                               |
| nyy      | 复制光标向下的 n 列                                          |
| p, P     | p: 将复制的数据在光标下一列贴上，<br />P: 将复制的数据在光标上一列贴上 |
| u        | undo，复原上一个动作                                         |
| ctrl + r | 重复上一个动作                                               |



### .1.2. 编辑模式
<a id="markdown-%E7%BC%96%E8%BE%91%E6%A8%A1%E5%BC%8F" name="%E7%BC%96%E8%BE%91%E6%A8%A1%E5%BC%8F"></a>

<img width=70% src='https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220522112347.png'>

