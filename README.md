# All In RegEx

> 正则表达式对程序猿来讲是很常见的，但这种非人类的言语表达起来，真的一点都不“正经”。但实质上常见的表达式并不复杂。我目前碰到的应用场景，无非是在某一长串字符串中，是否匹配你想要的内容，或者抠出你想要的内容。

## 语法

最简单的正则表达式就是从左到右逐字匹配，并且大小写敏感。如`am`，则会去字符串中搜索`a`，挨着搜索`m`。

``` javascript
var regex = /am/g;
var string = "I am palmer";
console.log( string.match(regex) ); 
// => ["am"]
```

### 元字符

> 在正则表达式中，经常会看到一些莫名其妙的字符，其实是有特殊的含义，这就是元字符。

|元字符|描述|
|:----:|----|
|.|句号匹配任意单个字符除了换行符.|
|[ ]|字符种类. 匹配方括号内的任意字符.|
|[^ ]|否定的字符种类. 匹配除了方括号里的任意字符|
|{n,m}|匹配num个大括号之前的字符 (n <= num <= m).|
|*|匹配>=0个重复的在*号之前的字符.|
|+|匹配>=1个重复的+号前的字符.
|?|标记?之前的字符为可选.|
|(xyz)|字符集, 匹配与 xyz 完全相等的字符串.|
|&#124;|或运算符,匹配符号前或后的字符.|
|&#92;|转义字符,用于匹配一些保留的字符 <code>[ ] ( ) { } . * + ? ^ $ \ &#124;</code>|
|^|从开始行开始匹配.|
|$|从末端开始匹配.|

#### 点运算符`.`

点运算符`.`，通配符，表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外。

``` javascript
var regex = /.m/g;
var string = "I am palmer";
console.log( string.match(regex) );
// => ["am", "lm"]
```

#### 字符集`[ ]`

方括号用来指定一个字符集，且不关心顺序。如`/[al]m/g`和`/[la]m/g`的匹配结果是一样的。

``` javascript
var regex = /[al]m/g;
var string = "I am palmer";
console.log( string.match(regex) );
// => ["am", "lm"]
```

##### 否定字符集`[^ ]`

`^`用在一个方括号的开头的时候, 它表示这个字符集是否定的，如表示后面跟着`m`的除`l`以外的任意字符

``` javascript
var regex = /[^l]m/g;
var string = "I am palmer";
console.log( string.match(regex) );
// => ["am"]
```

#### 重复

##### 量词`{}`

用来一个或一组字符可以重复出现的次数.

``` javascript
var regex = /a{1,2}m/g;
var string = "I am aam aaam";
console.log( string.match(regex) );
// => ["am", "aam", "aam"]
```

- `{m,n}` 表示出现m次到n次
- `{m,}` 表示至少出现m次
- `{m}`等价于`{m,m}` 表示出现m次

##### 贪婪匹配和惰性匹配

贪婪匹配意味着尽可能多的匹配，像下面的正则会匹配2位数、3位数、4位数、5位数的数字。

``` javascript
var regex = /\d{2,5}/g;
var string = "123 1234 12345 123456";
console.log( string.match(regex) );
// => ["123", "1234", "12345", "12345"]
```

惰性匹配意味着尽可能少的匹配，像下面的正则当匹配2位数的时候，就不会继续匹配3位数4位数5位数了。

``` javascript
var regex = /\d{2,5}?/g;
var string = "123 1234 12345 123456";
console.log( string.match(regex) );
// => ["12", "12", "34", "12", "34", "12", "34", "56"]
```

所以惰性匹配的语法是
- `{m,n}?` 
- `{m,}?`
- `??`
- `+?`
- `*?`


##### `*` 出现 >= 0次(等价于`{0,}`)

`*`号匹配在`*`之前的字符出现大于等于0次.

``` javascript
var regex = /[A-Z]*/g;
var string = "I Am Palmer";
console.log( string.match(regex) );
// => ["I", "", "A", "", "", "P", "", "", "", "", "", ""]
// 这里为什么长度为12， 而`I Am Palmer`长度为11？

var regex = /.*/g;
var string = "";
console.log( string.match(regex) );
// => 结果居然是[""]，string的长度为0，但match结果是1？
```

##### `+` 出现 >= 1次(等价于`{1,}`)

`+`号匹配`+`号之前的字符出现大于等于1次.

``` javascript
var regex = /[A-Z]+/g;
var string = "I Am Palmer";
console.log( string.match(regex) );
// => ["I", "A", "P"]
```

##### `?` 出现0次或者1次(等价于`{0,1}`)

`?`号匹配`?`号之前的字符出现0次或者1次.

``` javascript
var regex = /[A]?m/g;
var string = "I Am Palmer";
console.log( string.match(regex) );
// => ["Am", "m"]
```

#### 分组 `()`

`()`其实相对于是子模式，提供了分组，可以被js或者正则引用。

``` javascript
var regex = /(ab)+/g;
var string = "ababa abbb ababab";
console.log( string.match(regex) ); 
// => ["abab", "ab", "ababab"]
```

`()`也可以用来提取数据, 看下面的例子, 加上`()`的时候结果中可以提取括号里的内容.

``` javascript
var regex1 = /\d{4}-\d{2}-\d{2}/;
var regex2 = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2016-05-15";
console.log( string.match(regex1) );
console.log( string.match(regex2) );
// => ["2016-05-15", index: 0, input: "2016-05-15", groups: undefined]
// => ["2016-05-15", "2016", "05", "15", index: 0, input: "2016-05-15", groups: undefined]
```

在一些替换场景的时候, `()`也很强大. 看下面的例子, 需要将`yyyy-mm-dd`格式，替换成`mm/dd/yyyy`, 就可以用到构造函数的全局属性$1至$9.

``` javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2016-05-15";
console.log( string.replace(regex, "$2/$3/$1"));
// => 05/15/2016

等价于
string.replace(regex, function(match, year, month, day) {
	return month + "/" + day + "/" + year;
});
```

`()`也可以反向引用, 比如我们要匹配以下三种格式.

```
2016-05-12
2016/05/12
2016.05.12
```

想当然的写法是这样, 但这样也会匹配到类似`2016/12.4`这样的字符串.这个时候可以用`\1` `\2` `\3`来引用第n个括号里的模式.比如

``` javascript
var regex = /\d{4}(-|\/|\.)\d{2}(-|\/|\.)\d{2}/;
var regex1 = /\d{4}(-|\/|\.)\d{2}\1\d{2}/; // 这里的 \1 就代表引用之前的第1个分组, 不管第1个分组匹配到什么, \1 这里也匹配相同的字符.
var string = "2016-05-15";
var string1 = "2016-05.15";
console.log( regex.test(string) ); // => true
console.log( regex.test(string1) ); // => true
console.log( regex1.test(string) ); // => true
console.log( regex1.test(string1) ); // => false
```


#### 或运算符`|`

顾名思义, 就代表或.

``` javascript
var regex = /(a|l)m|I/g;
var string = "I am Palmer";
console.log( string.match(regex) ); 
// => ["I", "am", "lm"]
```

#### 转义字符`\`

转义紧随其后的字符, 用来转码`{ } [ ] / \ + * . $ ^ | ?`这些特殊字符.

#### 开头`^`结尾`$`

`^`用来检查匹配的字符串(后)是否在所匹配字符串的开头

``` javascript
var regex = /^I/g;
var string = "I am In.";
console.log( string.match(regex) ); 
// => ["I"]
```

`$`用来检查匹配的字符串(前)是否在所匹配字符串的结尾

``` javascript
var regex = /r\.$/g;
var string = "I ar Palmer.";
console.log( string.match(regex) ); 
// => ["r."]
```

### 简写字符集

> 

|简写|描述|
|:----:|----|
|.|除换行符外的所有字符|
|\w|匹配所有字母数字, 等同于 `[a-zA-Z0-9_]`|
|\W|匹配所有非字母数字, 即符号, 等同于: `[^\w]`|
|\d|匹配数字: `[0-9]`|
|\D|匹配非数字: `[^\d]`|
|\s|匹配所有空格字符, 等同于: `[\t\n\f\r\p{Z}]`|
|\S|匹配所有非空格字符: `[^\s]`|
|\f|匹配一个换页符|
|\n|匹配一个换行符|
|\r|匹配一个回车符|
|\t|匹配一个制表符|
|\v|匹配一个垂直制表符|
|\p|匹配 CR/LF (等同于 `\r\n`)，用来匹配 DOS 行终止符|


## 应用

### 手机号

``` javascript
正则：手机号（精确）
* <p>移动：134(0-8)、135、136、137、138、139、147、150、151、152、157、158、159、178、182、183、184、187、188、198</p>
* <p>联通：130、131、132、145、155、156、175、176、185、186、166</p>
* <p>电信：133、153、173、177、180、181、189、199</p>
* <p>全球星：1349</p>
* <p>虚拟运营商：170</p>

var regex = /^((13[0-9])|(14[5,7])|(15[0-3,5-9])|(17[0,3,5-8])|(18[0-9])|166|198|199|(147))\\d{8}$/
```

### 邮箱

``` javascript
var regex = /^[a-z0-9]+([._\\-]*[a-z0-9])*@([a-z0-9]+[-a-z0-9]*[a-z0-9]+\.){1,63}[a-z0-9]+$/
```

### 邮编

``` javascript
var regex = /^[1-9][0-9]{5}$/
```

### 日期

``` javascript
yyyy-mm-dd
var regex = /\d{4}-(0?[1-9]|1[012])-(0?[1-9]|[12][0-9]|3[01])/
```

### URL

``` javascript
var regex = /(http|ftp|https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&:/~\+#]*[\w\-\@?^=%&/~\+#])?/
```

### 去除文本首尾空白字符

``` javascript
const str = '  hello world  '
str.replace(/^\s+|\s$/g, '')
```

## RegExp对象方法

| 方法 | 说明 | 返回 |
| ------ | ------ | ------ |
| `pattern.test(str)` | 判断str是否包含匹配结果 | 包含返回true，不包含返回false |
| `pattern.exec(str)` | 根据pattern对str进行正则匹配 | 返回匹配结果数组, 如匹配不到返回null, 特殊数组 |
| `str.match(pattern)` | 根据pattern对str进行正则匹配 | 返回匹配结果数组, 如匹配不到返回null, 非全局 /g 下为特殊数组 |
| `str.replace(pattern, replacement)` | 根据pattern进行正则匹配, 把匹配结果替换为replacement | 新字符串 |

> exec 是 RegExp 对象的方法, match 是 String 对象的方法; 而且在非全局 /g 下, match 和 exec 的返回是一样的

``` javascript
var a = 'aaaa'.match(/\w/g)
console.log(a) // ["a", "a", "a", "a"]

var a = 'aaaa'.match(/\w/)
console.log(a) // ["a", index: 0, input: "aaaa", groups: undefined]

var a = /\w/.exec('aaaa')
console.log(a) // ["a", index: 0, input: "aaaa", groups: undefined]

```

[参考文献]

[https://github.com/zeeshanu/learn-regex](https://github.com/zeeshanu/learn-regex)

[工具]

[https://regexper.com](https://regexper.com)
