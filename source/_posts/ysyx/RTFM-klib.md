---
title: RTFM-klib
category:
  - 一生一芯
tags:
  - 一生一芯
abbrlink: 7ce4a0fa
date: 2022-12-24 08:37:30
---
# scanf manual

scanf(const char \*format, ...);
format:一系列用于描述如何处理字符序列的转换指令(directive)
...：pointer arguments，用于指向存放转换指令所需的输出位置

## format string
一系列用于描述如何处理字符序列的指令(directive)
当**fail**时停止输入并返回
faliure: 
1. *input faliure*输入不可用
2. *matching faliure*输入不匹配

## 指令(derective)
1. 空白字符匹配任意数量的空白字符
2. 普通字符，不含空白字符和"%"，从输入匹配该字符
3. 转换说明：由%开头的字符，用于说明如何转换输入并将转换结果置于该位置，若输入与转换说明不匹配，则发生 *matching failure*

每个位于*format*内的转换说明必须由"%"开始或者是字符序列"%n$"，后面跟随
- 可选的(\*)：按照转换指令的说明读取输入，但丢弃输入，不会存放到pointer
- 可选的(')，对小数转换：表示输入包括千分符，根据当前地区的*LC_NUMERIC*设置，该字符可能在(\*)先后出现
- 可选的(m)：与字符转换连用(%s, %c, %[)，使调用者无需为传入的指针分配空间，scanf将分配一块足够大的空间并返回给pointer参数，调用者应负责*free*该空间
- 可选的十进制数字：用于制定最大宽度(maximum field width)，当字符序列结束或计数到达最大宽度时停止，大多数转换将忽略掉开头的空白字符(特例见下说明)，字符串的结束符('\0')不算在计数内
- 可选的类型调整字符：例如*l*用于整数转换 %d 时，说明与之匹配的pointer是long类型
- 必选的转换说明符：说明需要执行的转换类型

## 转换(Conversions)
这部分比较多，所以先实现常用部分
### 类型调整(type modifier characters)
- l：
当转换类型为 d, i, o,  u,  x, X, or n，对应指针是*long*或者 *unsigned long*
当转换类型为 e, f, or g，对应指针是 *double*
- L：
与 *ll* 等效，对应指针类型为 *long long* *long double*
### 转换标识符(conversion specifiers)
- %：
匹配(%)
- d：
匹配十进制数，对应(int \*)
- u:
匹配无符号数，对应(unsigned int \*)
- x/X：
匹配十六进制数字，对应(unsigned int \*)
- f：
匹配小数，对应(float \*)
- s：
匹配一串非空字符，当到达输入结尾或*maximum field width* 指定数量时结束，对应(char \*)
- c：
匹配 *maximum field width* 指定数量的字符，默认为1，对应(char \*)
## 返回值(Return value)
当成功时，返回匹配并赋值成功的字符数量，遇到错误时提前返回

---

# sprintf manual
int sprintf(char *str, const char *format, ...);
str:指向存放处理结果的数组
format:一系列用于描述如何处理字符序列的转换指令(directive)
...：pointer arguments，用于指向待处理的输入数据

## 转换说明(conversion specification)
每个转换说明由%开头，由转换标识符(conversion specifier)结束
中间为可选的标识(flags)
最小位宽(field width) 精度(precision) 长度调整(length modifier)
普通字符将按照原样输出
## Flag characters
% 后跟随的标识
- \#:
根据转换标识符不同而变化
o:前缀加0
x/X:前缀加0x/0X
a, A, e, E, f, F, g, G:带小数点
- 0:
用0填充至指定长度(未设置该flag时使用空格)当设置了-时无效
- \-:
左对齐
- 空格:
正数前留一个空格
- +:
总是保留数字的符号

## Field width
可选的十进制数，用空格(右对齐且有0时用0)填充到指定位宽，当真实长度大于位宽，以真实长度为准
## Precision
可选，由.开头，紧随一个可选的十进制数，当不含该数，精度0位，当未设置精度，默认为负
## Length modifier
这部分暂时只实现常见部分
Here,  "integer  conversion" stands for d, i, o, u, x, or X conversion.
- l:
对应long或unsigned long
- ll:
对应long long 或unsigned long long
## Conversion specifiers
暂时只实现常见部分
- d, i:
int，默认精度为-1
- o, u, x, X:
unsigned int，八进制、无符号数、十六进制(小写，大写)
- f, F:
double，浮点数，默认精度为6，当精度为0，无小数点
- c:
int，转换成unsigned char，字符型
- s:
const char \*，字符串
## Return value
成功时，返回打印成功的字符数(不含结尾'\0')
对于snprintf，最大写入n个字符(含结尾'\0')
当遇到输出错误，返回负值

