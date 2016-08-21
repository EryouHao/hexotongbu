---
title: js不完全入门之数组
date: 2016-08-21 20:31:12
tags: 数组
categories: JavaScript
---

数组是值得有序集合。每个值叫做元素，每个元素在数组中都有数字位置编号，也就是索引。
JS中的数组是弱类型的，数组中可以含有不同类型的元素。
数组元素甚至可以是对象或其它数组。

`var arr = [1,true,null,undefined,{x:1},[1,2,3]];`
<!-- more -->
# 创建数组

## 1.字面量
  注意，数组可以用逗号留空的方式创建，如：
  ``` javascript
   var commasArr1 = [1,,2]; //1,undefined,2
   var commasArr2 = [,,];//undefined * 2
  ```
  数组大小限制
  >size form 0 to 2^23 - 1

## 2.new Array
``` javascript
  var arr = new Array();
  var arrWithLength = new Array(100); //undefined * 100
  var arrLikesLiteral = new Array(true,false,null,1,2,"hi");
  //等价于[true,false,null,1,2,"hi"];
```

# 数组元素读写
``` javascript
  var arr = [1,2,3,4,5];
  arr[1]; //2
  arr.length; //5

  arr[5] = 6;
  arr.length; //6

  delete arr[0]; //表现上只是改写为undefined，但是和直接赋值不同
  arr[0]; //undefined
  arr.length; //6
```

# 数组元素增删
  动态的，无需指定大小，数组的length会根据数组动态变化
``` javascript
  var arr = [];
  arr[0] = 1;
  arr[1] = 2;
  arr.push(3);
  arr; //[1,2,3]

  arr[arr.length] = 4; //equal to arr.push(4);
  arr; //[1,2,3,4]

  arr.unshift(0);
  arr; //[0,1,2,3,4];

  delete arr[2];
  arr; //[0,1,undefined,3,4]
  arr.length; //5
  2 in arr; //判断索引存不存在 false
  注：用delete变成的undefined和直接用arr[2] = undefined,是不同的
     后者 2 in arr; //true;

  arr.length -= 1;
  arr; //[0,1,undefined,3],4 is removed

  arr.pop(); //3 returned by pop
  arr;//[0,1,undefined],3 is removed

  arr.shift(); //0 returned by shift
  arr; //[1,undefined]
```

# 数组迭代
``` javascript
  var i = 0,n = 10;
  var arr = [1,2,3,4,5];

  for(; i < n; i++){
    console.log(arr[i]); //1,2,3,4,5
  }

  for(i in arr){
    console.log(arr[i]); //1,2,3,4,5
  }
```
  注：如果用 for-in 的方式来循环数组的话，
     那么在原型链上的属性也会被迭代出来，如：
``` javascript
  Array.prototype.x = 'inherited';

  for(i in arr){
    console.log(arr[i]); //1,2,3,4,5,inherited
  }
```
  此时可以用hasOwnProperty()方法来判断是否是数组元素
``` javascript
  for(i in arr){
    if(arr.hasOwnProperty(i)){
      console.log(arr[i]);//1,2,3,4,5
    }
  }
```
  注：for-in循环是不保证顺序的！

# 二维数组
``` javascript
  var arr = [[0,1],[2,3],[4,5]];
  var i = 0, j = 0;
  var row;
  for(; i < arr.length; i++){
    row = arr[i];
    console.log('row' + i);
    for(j = 0; j < row.length; j++){
      console.log(row[j]);
    }
  }
```

# 稀疏数组
  稀疏数组并不含有从0开始的连续索引。
  一般length属性值比实际元素个数大。
``` javascript
  var arr1 = [undefined];
  var arr2 = new Array(1);
  0 in arr1; //true
  0 in arr2; //false
  arr1.length = 100;
  arr1[99] = 123;
  99 in arr1; //true
  98 in arr1; //false
  var arr = [,,];
  0 in arr; //false
```

# 数组方法
  数组的原型 [] => Array.prototype

## 1.Array.prototype.join
  >将数组转为字符串

``` javascript
 var arr = [1,2,3];
 arr.join(); //"1,2,3"
 arr.join("_");//"1_2_3"

 function repeatString(str,n){
   return new Array(n+1).join(str);
 }
 repeatString("a",3); //"aaa"
 repeatString("Hi",5); //"HiHiHiHiHi"
```

## 2.Array.prototype.reverse
  >将数组逆序（原数组变）

``` javascript
    var arr = [1,2,3];
    arr.reverse(); //[3,2,1]
    arr; //[3,2,1] 原数组被修改！
```

## 3.Array.prototype.sort
  >排序(默认是按照字母排序，原数组变)

``` javascript
 var arr = ["a","d","c","b"];
 arr.sort(); //["a","b","c","d"]

 arr = [13,24,51,3];
 arr.sort(); //[13,24,3,51] 转换成字符串，按开头数字排序
 arr; //[13,24,3,51] 原数组被修改！

 /* 数字排序 */
 arr.sort(function(a,b){
   return a - b;
 }) //[3, 13, 24, 51]

 /* 对象排序 */
 arr = [{age:25},{age:39},{age:99}];
 arr.sort(function(a,b){
   return a.age - b.age;
 })
 arr.forEach(function(item){
   console.log('age',item.age);
 }) //age 25 age 39 age 99
```

## 4.Array.prototype.concat
  >数组合并（原数组不变）

``` javascript
 var arr = [1,2,3];
 arr.concat(4,5); //[1,2,3,4,5]
 arr; //[1,2,3] 原数组未被修改！

 arr.concat([10,11],13); //[1,2,3,10,11,13]

 arr.concat([1,[2,3]]); //[1,2,3,1[2,3]]
```
  注：当合并的为数组时，会拉平成元素，但合并的数组中还有数组的话，不能拉平数组中的数组

## 5.Array.prototype.slice
  >返回部分数组（原数组不变）

``` javascript
    var arr = [1,2,3,4,5];
    arr.slice(1,3); //[2,3] 左闭右开
    arr.slice(1); //[2,3,4,5] 一个参数，从索引开始到最后
    arr.slice(1,-1); //[2,3,4] 负数索引，从后往前数，最后一个为-1
    arr.slice(-4,-3); //[2]
```

## 6.Array.prototype.splice
  >数组拼接(原数组变)

``` javascript
 var arr = [1,2,3,4,5];
 /* 一个参数：索引 */
 arr.splice(2); //returns [3,4,5]
 arr; //[1,2]

 arr = [1,2,3,4,5];
 /* 两个参数：索引 | 个数 */
 arr.splice(2,2); //returns [3,4]
 arr; //[1,2,5];

 arr = [1,2,3,4,5];
 /* 多个参数：索引 | 个数 | 待插入元素 */
 arr.splice(1,1,'a','b'); //returns [2]
 arr; //[1,"a","b",3,4,5]
```
## 7.Array.prototype.forEach
  >数组遍历

``` javascript
    var arr = [1,2,3,4,5];
    arr.forEach(function(x, index, a){
      console.log(x + '|' + index + '|' + (a === arr));
    })
```

## 8.Array.prototype.map
  >数组映射（原数组不变）

``` javascript
    var arr = [1,2,3];
    arr.map(function(x){
      return x + 10;
    }); //[11,12,13]
    arr; //[1,2,3]
```

## 9.Array.prototype.filter
  >数组过滤（原数组不变）

``` javascript
    var arr = [1,2,3,4,5,6,7,8,9,10];
    arr.filter(function(x, index){
      return index % 3 === 0 || x >= 8;
    });// returns [1,4,7,8,9,10]
    arr; //[1,2,3,4,5,6,7,8,9,10]
```
## 10.Array.prototype.every & some
  >数组判断

``` javascript
    var arr = [1,2,3,4,5];
    arr.every(function(x){
      return x < 10;
    }); //true
    arr.every(function(x){
      return x < 3;
    }); //false
    arr.some(function(x){
      return x === 3;
    }); //true
    arr.some(function(x){
      return x === 100;
    }); //false
```
## 11.Array.prototype.reduce & reduceRight
``` javascript
 var arr = [1,2,3];
 var sum = arr.reduce(function(x, y){
   return x + y;
 },0); //6
 arr; //[1,2,3]

 arr = [3,9,6];
 var max = arr.reduce(function(x, y){
   console.log(x + "|" + y);
   return x > y?x:y;
 });
 // 3 | 9
 // 9 | 6
 max; //9
```

注：计算逻辑，当有第二个可选参数时，也就是上面的0，
       第一次计算 0+1，第二次将上一次结果1和数组中第二个元素相加，
       第三次将上次结果和下一个元素相加;
       若没有第二个参数，则第一次取前两个，所得结果和下一个元素进行下一次运算

``` javascript
     max = arr.reduceRight(function(x, y){
      console.log(x + "|" +y);
      return x > y?x:y;
     });
     // 6 | 9
     // 9 | 3
     max; //9
```

 注：从右向左传参

## 12.Array.prototype.indexOf & lastIndexOf
  >数组检索

``` javascript
    var arr = [1,2,3,2,1];
    //查找元素2
    arr.indexOf(2); //1
    //查找元素99
    arr.indexOf(99); //-1
    //查找元素1，从第二个元素开始
    arr.indexOf(1,1); //4
    //查找元素1，从倒数第三个元素开始
    arr.indexOf(1,-3); //4
    //查找元素2，从最后一个元素开始
    arr.indexOf(2,-1); //-1
    //查找元素2，从右向左找
    arr.lastIndexOf(2); //3
    //查找元素2，从右向左找，从倒数第二个元素开始
    arr.lastIndexOf(2,-2); //3
    //查找元素2，从右向左找，从倒数第三个元素开始
    arr.lastIndexOf(2,-3); //1
```
注：不知道看到上面这8个示例是不是晕了，理一理：
      ①两个方法都可以接收两个参数，第一个为要查找的元素（必选），第二个为开始位置索引（可选）
      ②所得结果为元素的索引（也称下标），正数即索引（无论怎么找，都是从左向右，从0开始！），-1即未找到
      ③方法的第二个参数：若为正数，从前往后索引（0开始）；若为负数，从后往前索引（-1开始）

## 13.Array.isArray
  >判断是否为数组

``` javascript
    Array.isArray([]); //true
    //另外几种
    [] instanceof Array; //true
    ({}).toString.apply([]) === '[object Array]'; //true
    [].constructor === Array; //true (这种不可靠，如继承)
```

# 数组小结
## 数组 VS 一般对象
  相同：都可以继承。数组是对象，对象不一定是数组。都可以当做对象添加删除属性。
  不同：数组自动更新length。按索引访问数组常常比访问一般对象属性明显迅速。
   数组对象继承Array.prototype上的大量数组操作方法

## 字符串和数组
  字符串可以理解为类数组，但它不是数组
``` javascript
 var str = "hello world";
 str.charAt(0); //"h"
 str[1]; //e

 Array.prototype.join.call(str,"_");
 //"h_e_l_l_o_ _w_o_r_l_d"
```
