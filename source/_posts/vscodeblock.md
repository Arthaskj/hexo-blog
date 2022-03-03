<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:29:41
 * @Description: 
 -->
VS Code 代码块

![演示用例](/images/assets/vscode/snippet.png)

```
添加当前文件名 => $TM_FILENAME
添加当前时间 => $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND


变量
使用$ name或$ {name：default}，您可以插入变量的值。如果未设置变量，则会插入其默认值或空字符串。当变量未知（即，未定义其名称）时，将插入变量的名称并将其转换为占位符。

可以使用以下变量：

TM_SELECTED_TEXT当前选定的文本或空字符串
TM_CURRENT_LINE当前行的内容
TM_CURRENT_WORD光标下的单词或空字符串的内容
TM_LINE_INDEX基于零索引的行号
TM_LINE_NUMBER基于单索引的行号
TM_FILENAME当前文档的文件名
TM_FILENAME_BASE没有扩展名的当前文档的文件名
TM_DIRECTORY当前文档的目录
TM_FILEPATH当前文档的完整文件路径
CLIPBOARD剪贴板的内容
用于插入当前日期和时间：

CURRENT_YEAR当前年份
CURRENT_YEAR_SHORT当前年份的最后两位数字
CURRENT_MONTH月份为两位数（例如'02'）
CURRENT_MONTH_NAME月份的全名（例如“七月”）
CURRENT_MONTH_NAME_SHORT月份的简称（例如'Jul'）
CURRENT_DATE月份的日期
CURRENT_DAY_NAME天的名称（例如'星期一'）
CURRENT_DAY_NAME_SHORT当天的简称（例如'Mon'）
CURRENT_HOUR 24小时制格式的当前小时
CURRENT_MINUTE当前分钟
CURRENT_SECOND当前秒
```