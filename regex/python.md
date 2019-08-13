

---
#### 正则语法

正则语法            |  描述
---                 |-
\d{m,n}             | 匹配[m, n]个数字，注意m与n之间不可以有空格；
\d{m,}?             | 其中的?表示最小匹配模式；
 (?:for\|while)?if  | 无捕获组，有时某些地方需要整体处理，但不希望被捕获；
*？ +？ ??          | 最小匹配
r'<(\w)+>.*<\1>'    | back reference

---
#### 非捕获组


字符        | 描述  | 示例
---         |---    |--
(?:pattern) | 匹配pattern，但不捕获匹配结果。 | 'industr(?:y\|ies) 匹配'industry'或'industries'。
(?=pattern) | 零宽度正向预查，不捕获匹配结果。| 'Windows (?=95\|98\|NT\|2000)' 匹配 "Windows2000" 中的 "Windows", 不匹配 "Windows3.1" 中的 "Windows"。
(?!pattern) | 零宽度负向预查，不捕获匹配结果。| 'Windows (?!95\|98\|NT\|2000)', 匹配 "Windows3.1" 中的 "Windows", 不匹配 "Windows2000" 中的 "Windows"。
(?<=pattern)| 零宽度正向回查，不捕获匹配结果。| '2000 (?<=Office\|Word\|Excel)' 匹配 " Office2000" 中的 "2000", 不匹配 "Windows2000" 中的 "2000"。
(?<!pattern)| 零宽度负向回查，不捕获匹配结果。| '2000 (?<!Office\|Word\|Excel)', 匹配 " Windows2000" 中的 "2000", 不匹配 " Office2000" 中的 "2000"。


1. [正则表达式之捕获组/非捕获组](http://www.cnblogs.com/wuhong/archive/2011/02/18/1957017.html)
2. [.NET正则表达式提取HTML元素所有属性的方法](http://blog.useasp.net/archive/2013/06/14/use-regular-expression-to-parse-html-tags-attributes-method-with-csharp.aspx)
3. [正则基础之——捕获组（capture group）](http://www.cnblogs.com/pmars/archive/2011/12/30/2307507.html)

---
#### 函数列表

1. pattern = re.compile( r'<li>(.*?)<\li>', re.DOTALL)
    - pattern= r'<li>(.*?)<\li>'，是一个raw字符串；
    - 该pattern匹配<li><\li>之间的任意字符（包括newline）；
    - 其中的？表示使用最小匹配模式（python默认使用最大匹配模式）；
    - re.DOTALL表示(.)将匹配newline，这个参数可以省略
2. match = re.search(pattern, text)
    - 扫描整个字符串，返回匹配pattern的MatchObject，如果找不到则返回None ；
    - 注意与re.match(pattern, text)的区别，即使pattern以^开始或指定re.MULTILINE模式，match从是从字符串最开始的位置进行匹配并与模式无关；
    - match = pattern.search(text)
3. for match in re.finditer(pattern, text, re.I): print(match.group(0))
    - finditer 返回iterator，循环匹配；
    - re.I表示忽略大小写；
    - match.group()是匹配元组字符串，参数默认为0；
4. re.sub(pattern, repl, string)
    - 将string中的pattern替换成repl；
    - pattern与repl都是正则表达式；