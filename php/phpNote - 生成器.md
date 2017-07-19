# phpNote - 生成器


## 生成器语法  

一个生成器函数看起来像一个普通的函数，不同的是普通函数返回一个值，而一个生成器可以`yield`生成**许多**它所需要的值。 

当一个生成器被调用的时候，它返回一个可以被遍历的对象.当你遍历这个对象的时候(例如通过一个foreach循环)，PHP 将会在每次需要值的时候调用生成器函数，并在产生一个值之后保存生成器的状态，这样它就可以在需要产生下一个值的时候恢复调用状态。 

一旦不再需要产生更多的值，生成器函数可以简单退出，而调用生成器的代码还可以继续执行，就像一个数组已经被遍历完了。 

> ** Note: **
一个生成器不可以返回值： 这样做会产生一个编译错误。然而return空是一个有效的语法并且它将会终止生成器继续执行。

## `yield`关键字 

生成器函数的核心是`yield`关键字。它最简单的调用形式看起来像一个`return`申明，不同之处在于普通`return`会返回值并终止函数的执行，而`yield`会返回一个值给循环调用此生成器的代码并且只是暂停执行生成器函数。 

```php
<?php
function gen_one_to_three() {
    for ($i = 1; $i <= 3; $i++) {
        //注意变量$i的值在不同的yield之间是保持传递的。
        yield $i;
    }
}

$generator = gen_one_to_three();
foreach ($generator as $value) {
    echo "$value\n";
}
?> 
```
以上例程会输出：

	1
	2
	3
    

>**警告**： 
如果在一个表达式上下文(例如在一个赋值表达式的右侧)中使用yield，你必须使用圆括号把yield申明包围起来。

## 指定键名来生成值

PHP的数组支持关联键值对数组，生成器也一样支持。所以除了生成简单的值，你也可以在生成值的时候指定键名。 

```php
<?php
/* 
 * 下面每一行是用分号分割的字段组合，第一个字段将被用作键名。
 */

$input = <<<'EOF'
1;PHP;Likes dollar signs
2;Python;Likes whitespace
3;Ruby;Likes blocks
EOF;

function input_parser($input) {
    foreach (explode("\n", $input) as $line) {
        $fields = explode(';', $line);
        $id = array_shift($fields);

        yield $id => $fields;
    }
}

foreach (input_parser($input) as $id => $fields) {
    echo "$id:\n";
    echo "    $fields[0]\n";
    echo "    $fields[1]\n";
}
?> 
以上例程会输出：
1:
    PHP
    Likes dollar signs
2:
    Python
    Likes whitespace
3:
    Ruby
    Likes blocks

```

## 使用引用来生成值

生成函数可以像使用值一样来使用引用生成。这个和`returning references from functions`（从函数返回一个引用）一样：通过在函数名前面加一个引用符号

```php

<?php
function &gen_reference() {
    $value = 3;

    while ($value > 0) {
        yield $value;
    }
}

/* 
 * 我们可以在循环中修改$number的值，而生成器是使用的引用值来生成，所以gen_reference()内部的$value值也会跟着变化。
 */
foreach (gen_reference() as &$number) {
    echo (--$number).'... ';       //输出：2... 1... 0... 
}
?> 
```