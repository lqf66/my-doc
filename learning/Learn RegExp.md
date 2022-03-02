###### 一、字符匹配
1. 模糊匹配
	1. 横向 `/ab{2, 5}/`
	2. 纵向 `/a[123]b/`
2. 字符组
	1. 范围 `[a-z]`
	2. 排除字符组 `[^a-b]`
	3. 常见简写 `\d \D \w \W \s \S .`  tips: `\s = [ \t\v\n\r\f]`
3. 量词
	1. `{m,} {m} ? + *`
	2. 默认贪婪匹配，惰性匹配`?`
4. 多选分支`|`

###### 二、位置匹配
1.  ^ $ 开头与结尾
2.  `\b(\B)`单词边界：\w与\W、\w与^、\w与$
3. `(?=p)`正向先行断言：该位置后的字符s需要匹配p
4. `(?!p)`反向先行断言：该位置后的字符不能匹配p
5. 不匹配任何的字符 \.^\
6. 千位分隔符表示：
`/(?=\d{3}$)/g -> /(?=\d{3})+$/g -> /(?!^)(?=(\d{3}))+$/g -> /\B(?=(\d{3})+\b)/g`
7. 验证密码：
 `/((?=.*[0-9])(?=.*[a-z])|(?=.*[0-9])(?=.*[A-Z])|(?=.*[a-z])(?=.*[AZ]))^[
0-9A-Za-z]{6,12}$/`
`/(?!^[0-9]{6,12}$)(?!^[a-z]{6,12}$)(?!^[A-Z]{6,12}$)^[0-9A-Za-z]{6,12}$/`

###### 三、括号作用
1. 分组和分支：分支结构为惰性
2. 分组引用
	1. 提取数据：`$1`
	2. 替换：`'2017-06-12'.replace(/(\d{4})-(\d{2})-(\d{2})/, "$2/$3/$1")`
3. 反向引用：`/\d{4}(-|\/|\.)\d{2}(-|\/|\.)\d{2}/`
	1. 括号嵌套：`/^((\d)(\d(\d)))\1\2\3\4$/.test('1231231233') -- RegExp.$1: 123`
	2. `\10`表示第十个分组，如果匹配`\1和0`，使用`(?:\1)0`或`\1(?:0)`
	3. 引用不存在的数组匹配字符本身
	4. 分组后有量词，获取最后一次匹配`'12345 5'.test(/(\d)+ \1/)`
4. 非捕获括号`(?:)`：只保留括号的分组分支功能
5. trim方法：
	1. 匹配空白符`str.replace(/^\s+|\s+$/g, '')`
	2. 匹配字符串`str.replace(/^\s*(.*?)\s*$/g, '$1')`需要惰性匹配
6. 首字母大写：`str.replace(/(?:^|\s)\w/g, (c)=> c.toUpperCase())`
7. 驼峰化：`str.replace(/[_-\s]+(.)?)/g, (match, c) => c ? c.toUpperCase() : '')`
8. 中划线化：`str.replace(/([A-Z])/g, '-$1').toLowerCase()`
9. 匹配成对标签：`/<([^>]+)[\d\D]*<\/\1>/`
10. html转义与反转义：
```js
// 转义
var escapeChars = {
	'<' : 'lt',
	'>' : 'gt',
	'"' : 'quot',
	'&' : 'amp',
	'\'' : '#39'
}
str.replace(new RegExp('[' + Object.keys(escapeChars).join('') + ']', 'g'), 
	match => return '$' + escapeChars[match] + ';'
)

// 反转义
var htmlEntities = {
	nbsp: ' ',
	lt: '<',
	gt: '>',
	quot: '"',
	amp: '&',
	apos: '\''
}
return str.replace(/\&([^;]+);/g, function (match, key) {
	if (key in htmlEntities) {
			return htmlEntities[key];
	}
	return match;
})
```

###### 四、正则方法与属性
**String方法**
1. search：返回正则匹配到的第一个子串在目标字符串中的下标位置
	1. 传入的字符串会转化为正则
2. split：以正则匹配到的子串，对目标字符串进行切分。返回一个数组
	1. 第二个参数时数组长度
	2. 正则使用分组时，结果数组中包含分隔符
3. match：对目标字符串执行正则匹配操作，返回的匹配结果数组中包含具体的匹配信息
	1. 传入的字符串会转化为正则
	2. 返回格式与修饰符g有关
4. replace：对目标字符串进行替换操作。正则是其第一个参数。返回替换后的字符串
**RegExp相关实例方法**
1. test：判断目标字符串中是否有满足正则匹配的子串。返回布尔值
2. exec：比 match 更强大的正则匹配操作。返回结果与 match 一致

**构造函数属性**
1. `RegExp["$_"]` RegExp.input：最近一次目标字符串
2. `RegExp["$&"]` RegExp.lastMatch：最近一次匹配的文本
3. `RegExp["$+"]` RegExp.lastParen：最近一次捕获的文本
4. `RegExp["$``"]` RegExp.leftContext：目标字符串中lastMatch之前的文本
5. `RegExp["$'"]` RegExp.rightContext：目标字符串中lastMatch之后的文本