---
title: 一篇文章带你玩转正则表达式
date: 2016-08-12 20:34:28
tags: 正则表达式
categories: JavaScript
---

读这篇文章之前你需要掌握基本的JavaScript知识，并且有安静舒适的环境与宽裕的时间，文章有点长，你可以选择备好coffee或茶来慢慢学习。
文章标题有点夸大了，这是一篇正则表达式的入门文章。如有错误，欢迎指正。
<!-- more -->

# 概念和语法
REGEXP对象

JavaScript 通过内置对象RegExp支持正则表达式，有两种方法实例化RegExp对象

>1.字面量 2.构造函数


## 字面量
  `var reg = /\bis\b /g`  //匹配单词is g是global 进行全文搜索匹配

``` javascript
  没有g：
  var reg = /\bis\b/;
  //只替换第一项
  'He is a boy. This is a dog. Where is she?'.replace(reg,'IS');
  "He IS a boy. This is a dog. Where is she?"
  有g：
  var reg = /\bis\b/g;
  //替换了所有的
  'He is a boy. This is a dog. Where is she?'.replace(reg,'IS');
  "He IS a boy. This IS a dog. Where IS she?"
```

## 构造函数
  `var reg = new RegExp( '\\bis\\b' , 'g');` //同样，如果不加g 则只替换第一项

## 修饰符
>g：global 全文搜索，不添加，搜索到第一个匹配停止
  i：ignore case 忽略大小写，默认大小写敏感
  m：multiple lines 多行搜索

``` javacript
  //未忽略大小写
  'He is a boy. Is he?'.replace(/\bis\b/g, '0');
  "He 0 a boy. Is he?"
  //忽略大小写
  'He is a boy. Is he?'.replace(/\bis\b/gi, '0');
  "He 0 a boy. 0 he?"
```

## 元字符
·正则表达式由两种基本字符类型组成：
- 原义文本字符
- 元字符

·元字符是在正则表达式中有特殊含义的非字母字符
    `· * + ? $ ^ . / \ () {} []`

## 字符类
  我们可以使用元字符 [] 来构建一个简单的类
  所谓类是指符合某些特性的对象，一个泛指，而不是特指某个字符
  表达式[abc] 把字符a或b或c归为一类，表达式可以匹配这类的字符（one of）
``` javascript
  'a1b2c3d4'.replace(/[abc]/g,'X')
  "X1X2X3d4"
```

## 字符类取反
  使用元字符^创建 反向类/负向类
``` javascript
  'a1b2c3d4'.replace(/[^abc]/g,'X')
  "aXbXcXXX"
```

## 范围类
  正则表达式还提供了 范围类
  所以我们可以使用 [a-z] 来连接两个字符表示 从a到z的任意字符
  这是个闭区间，也就是包含a和z本身

``` javascript
  'a1b2d3x4z9'.replace(/[a-z]/g,'Q')
  "Q1Q2Q3Q4Q9"
```

  在[]组成的类内部是可以连写的[a-zA-Z] (one of)

``` javascript
  'a1b2d3x4z9AJHGK'.replace(/[a-zA-Z]/g,'Q')
  "Q1Q2Q3Q4Q9QQQQQ"
```
  中划线：如果是在里面 如[a-z]，就表示范围的意思，如果想匹配中划线，在后面加
``` javascript
  '2016-09-12'.replace(/[0-9-]/g,'A')
  "AAAAAAAAAA"
```

## 预定义类
  正则表达式提供 预定义类 来匹配常见的字符类
> 字符 . 等价类[^\r\n]   除了回车符和换行符之外的所有字符
\d      [0-9]    数字字符
      \D      [^0-9]   非数字字符
      \s 空白符 \S非空白符
      \w 单词字符（字母、数字下划线）
      \W 非单词字符
      ab\d. //ab+数字+任意字符


## 边界  
正则表达式还提供了几个常用的边界匹配字符
  >字符 ^ 以xxx开始
      $ 以xxx结束
      \b 单词边界
      \B 非单词边界

``` javascript
  '@123@abc@'.replace(/@./g, 'Q')
  "Q23Qbc@"
  '@123@abc@'.replace(/^@./g, 'Q')
  "Q23@abc@"
  '@123@abc@'.replace(/.@/g, 'Q')
  "@12QabQ"
  '@123@abc@'.replace(/.@$/g, 'Q')
  "@123@abQ"
```

## 量词
  我们希望匹配一个连续出现20次数字的字符串

>字符 \？出现零次或一次（最多出现一次）
  \+ 出现一次或多次（至少出现一次）
  \* 出现零次或多次（任意次）
  {n} 出现n次
  {n,m}出现n到m次
  {n,}至少出现n次


## 贪婪模式
  >\d{3,6}   12345678

``` javascript
  //尽可能多的匹配
  '12345678'.replace(/\d{3,6}/g,'X')
  "X78"
```

## 非贪婪模式
  让正则表达式尽可能少的匹配，也就是说一旦成功匹配不再继续尝试就是非贪婪模式
``` javascript
  //在量词后加上？即可 如下面的为3个一组
  '12345678'.replace(/\d{3,6}?/g,'X')
  "XX78"
```

## 分组
  匹配字符串Byron连续出现3次的场景
  `Byron{3}` 只是将n重复三次
  使用()可以达到分组的功能，使量词作用于分组
  `(Byron){3}` 是将Byron重复三次
```javascript
  //错误的
  'a1b2c3d4'.replace(/[a-z]\d{3}/g,'X')
  "a1b2c3d4"
  //正确的
  'a1b2c3d4'.replace(/([a-z]\d){3}/g,'X')
  "Xd4"
```

## 或
  使用 | 可以达到或的效果
``` javascript
  //示例一
  'ByronCasper'.replace(/Byron|Casper/g,'X')
  "XX"
  //示例二(分组和或配合使用)
  'ByronsperByrCasper'.replace(/Byr(on|Ca)sper/g,'X')
  "XX"
```

## 反向引用
  2016-08-12 => 08/12/2016
``` javascript
  //分组捕获
  '2016-08-12'.replace(/(\d{4})-(\d{2})-(\d{2})/g,'$1')
  "2016"
  '2016-08-12'.replace(/(\d{4})-(\d{2})-(\d{2})/g,'$2/$3/$1')
  "08/12/2016"
```

## 忽略分组
  不希望捕获某些分组，只需要在分组内加上？：就可以
  `(?:Byron).(ok)`

## 前瞻
  正则表达式从文本头部向尾部开始解析，文本尾部方向，称为“前”
  前瞻就是在正则表达式匹配到规则的时候，向前检查是否符合断言，
  后顾/后瞻方向相反

  正向前瞻 exp(?=assert)
  负向前瞻 exp(?!assert)
``` javascript
  //单词后面是数字
  'a2*3'.replace(/\w(?=\d)/g,'X')
  "X2*3"
  'a2*34v8'.replace(/\w(?=\d)/g,'X')
  "X2*X4X8"
  //单词后面不是数字
  'a2*34vv'.replace(/\w(?!\d)/g,'X')
  "aX*3XXX"
```

## 对象属性
  对象属性是只读的
  >global：是否全文搜索，默认false
  ignore case：是否大小写敏感，默认是false
  multiline：多行搜索，默认值是false
  lastIndex：是当前表达式匹配内容的最后一个字符的下一个位置
  source：正则表达式的文本字符串

# test和exec方法
  *RegExp.prototype.test(str)*
  用于测试字符串参数中是否存在匹配正则表达式模式的字符串
  如果存在则返回true，否则返回false
``` javascript
  var reg1 = /\w/;
  reg1.test('a')
  true
  reg1.test('$')
  false

  但是如果这样：
  var reg2 = /\w/g;
  //第一次
  reg1.test('ab')
  true
  //第二次
  reg1.test('ab')
  true
  //第三次
  reg1.test('ab')
  false
```

  这是lastIndex在作怪！
  第一次匹配的时候当前匹配内容为a，最后一个字符也是a本身，下一个位置为b前面；
  第二次匹配的时候当前匹配内容为b，最后一个字符也是b本身，下一个位置为b后面；
  第三次匹配的时候当前匹配内容为空，最后一个字符也是空本身，下一个位置重置为a前面。

  解决方法：
  ①每次都实例化一个新对象
    (/\w/g).test('ab') 这样就能每次都是true，但是会产生内存开销，不采用！
  ②如果想用test本意的话，不用加g，只需检测有没有就可以了。

  *RegExp.prototype.exec(str)*
  使用正则表达式模式对字符串执行搜索，并将更新全局RegExp对象的属性以反映匹配结果

  如果没有匹配的文本则返回null，否则返回一个结果数组：
  \- index 声明匹配文本的第一个字符的位置
  \- input 存放被检索的字符串 string

  非全局调用

  调用非全局的RegExp对象的exec()时，返回数组
  第一个元素是与正则表达式相匹配的文本
  第二个元素是与RegExpObject的第一个子表达式相匹配的文本（如果有的话）
  第三个元素是与RegExp对象的第二个子表达式相匹配的文本（如果有的话），以此类推
``` javascript
  var reg3 = /\d(\w)(\w)\d/;
  var reg4 = /\d(\w)(\w)\d/g;
  var ts = '$1az2bb3cy4dd5ee';

  var ret = reg3.exec(ts);
  //在非全局下 lastIndex失效
  console.log(reg3.lastIndex + '\t' + ret.index + '\t' + ret.toString());

  console.log(reg3.lastIndex + '\t' + ret.index + '\t' + ret.toString());

  while(ret = reg4.exec(ts)){
     console.log(reg4.lastIndex + '\t' + ret.index + '\t' + ret.toString());
  }
  //结果：
  "0  1 1az2,a,z"
  "0  1 1az2,a,z"
  "5  1 1az2,a,z"
  "11 7 3cy4,c,y"
```

# 字符串对象方法
  *Stringl.protatype.search(reg)*
  search()方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串
  方法返回第一个匹配结果index，查找不到返回 -1
  search()方法不执行全局匹配，它将忽略标志g，并且总是从字符串的开始进行检索

  *String.prototype.match(reg)*
  match()方法将检索字符串，以找到一个或多个与regexp匹配的文本
  regexp是否具有标志g对结果影响很大
非全局调用
  如果regexp没有标志g，那么match()方法就只能在字符串中执行一次匹配
  如果没有找到任何匹配的文本，将返回null
  否则它将返回一个数组，其中存放了与它找到的匹配文本有关的信息

  返回数组的第一个元素存放的是匹配文本，而其余的元素存放的是与正则表达式的子表达式匹配的文本
  除了常规的数组元素之外，返回的数组还含有2个对象属性
  - index 声明匹配文本的起始字符在字符串的位置
  - input 声明对stringObject的引用
``` javascript
      var reg3 = /\d(\w)\d/;
      var reg4 = /\d(\w)\d/g;
      var ts = '$1a2b3c4d5e';

      var ret = ts.match(reg3);

      console.log(ret);
      console.log(ret.index + '\t' + reg3.lastIndex);

      //结果
      ["1a2", "a"]
      "1  0"
```

全局调用
      如果regexp具有标志g则match()方法将执行全局检索，找到字符串中的所有匹配字符串
        没有找到任何匹配的子串，则返回null
        如果找到了一个或多个匹配子串，则返回一个数组
      数组元素中存放的是字符串中所有的匹配子串，而且也没有index属性或input属性
``` javascript
      ret = ts.match(reg4);
      console.log(ret);
      console.log(ret.index + '\t' + reg3.lastIndex);

      //结果
      ["1a2", "3c4"]
      "undefined  0"
```

  *String.prototype.split(reg)*
  我们经常使用split方法把字符串分割为字符数组
  `'a,b,c,d'.split(','); //["a","b","c","d"]`
  在一些复杂的分割情况下我们可以使用正则表达式解决
  `'a1b2c3d'.split(/\d/); //["a","b","c","d"]`

  *String.prototype.replace*
  >String.prototype.replace(str,replaceStr)
    String.prototype.replace(reg,replaceStr)
    String.prototype.replace(reg,function)

  >function参数含义
    function会在每次匹配替换的时候调用，有四个参数
    ①匹配字符串
    ②正则表达式分组内容，没有分组则没有该参数
    ③匹配项在字符串中的index
    ④原字符串

``` javascript
    'a1b2c3d4e5'.replace(/\d/g, function(match,index,origin){
      console.log(index);
      return parseInt(match) + 1;
    });
    //结果
    1
    3
    5
    7
    9
    "a2b3c4d5e6"

    'a1b2c3d4e5'.replace(/(\d)(\w)(\d)/g, function(match,group1,group2,group3,index,origin){
      console.log(match);
      return group1 + group3;
    });
    //结果
    "1b2"
    "3d4"
    "a12c34e5"
```

希望能对你有所帮助，更好的运用正则来解决问题！
附：
>1.正则表达式检测网站：(https://regexper.com/)
 2.JSBin：(http://jsbin.com/?js,console)