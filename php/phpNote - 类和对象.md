# [php 之 类和对象](#aaa)#

### PHP 之 类  ###
````php
	<?php
	class A
	{
	    function foo()
	    {
	        if (isset($this)) {
	            echo '$this is defined (';
	            echo get_class($this);
	            echo ")\n";
	        } else {
	            echo "\$this is not defined.\n";
	        }
	    }
	}
	
	class B
	{
	    function bar()
	    {
	        // Note: the next line will issue a warning if E_STRICT is enabled.
	        A::foo();
	    }
	}
	
	$a = new A();
	$a->foo();
	
	// Note: the next line will issue a warning if E_STRICT is enabled.
	A::foo();
	$b = new B();
	$b->bar();
	
	// Note: the next line will issue a warning if E_STRICT is enabled.
	B::bar();
	?>
````
以上历程会输出：  

	$this is defined (A)
	$this is not defined.
	$this is defined (B)
	$this is not defined.

#### 2、类常量
在定义和使用常量的时候不需要使用 $ 符号
常量的值必须是一个定值，不能是变量，类属性，数学运算的结果或函数调用。
定义和使用一个类常量：

```php
<?php
class MyClass
{
	const constant ="constant value";
    function showConstance(){
    	echo self::constant ."\n";
        echo MyClass::constant . "\n";

    $classname = "MyClass";
    echo $classname::constant . "\n"; // 自 5.3.0 起

    $class = new MyClass();
    $class->showConstant();

    echo $class::constant."\n"; // 自 PHP 5.3.0 起
?>
}
```
#### 3、自动加载类
- __autolaod(）
- spl_autoload_register()

```c
在 5.3.0 版之前，__autoload 函数抛出的异常不能被 catch 语句块捕获并会导致一个致命错	误。从 5.3.0+ 之后，__autoload 函数抛出的异常可以被 catch 语句块捕获，但需要遵循一个条件。如果抛出的是一个自定义异常，那么必须存在相应的自定义异常类。__autoload 函数可以递归的自动加载自定义异常类。
```
自动加载类示例：
```PHP
<?php
function __autoload($class_name) {
    require_once $class_name . '.php';
}

$obj  = new MyClass1();
$obj2 = new MyClass2();
?> 
```
本例抛出一个异常并在 try/catch 语句块中演示：
```PHP
<?php
function __autoload($name) {
    echo "Want to load $name.\n";
    throw new Exception("Unable to load $name.");
}

try {
    $obj = new NonLoadableClass();
} catch (Exception $e) {
    echo $e->getMessage(), "\n";
}
?> 
```
以上例程会输出：
```language
Want to load NonLoadableClass.
Unable to load NonLoadableClass.

```
#### 4、构造函数和析构函数
##### 构造函数
`void __construct ([ mixed $args [, $... ]] )`
>Note: 如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用 parent::__construct()。如果子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（假如没有被定义为 private 的话）。  

##### 析构函数

PHP 5 引入了析构函数的概念，这类似于其它面向对象的语言，如 C++。析构函数会在到某个对象的所有引用都被删除或者当对象被显式销毁时执行。
```php
<?php
class MyDestructableClass {
   function __construct() {
       print "In constructor\n";
       $this->name = "MyDestructableClass";
   }

   function __destruct() {
       print "Destroying " . $this->name . "\n";
   }
}

$obj = new MyDestructableClass();
?> 
```
和构造函数一样，父类的析构函数不会被引擎暗中调用。要执行父类的析构函数，必须在子类的析构函数体中显式调用 parent::__destruct()。此外也和构造函数一样，子类如果自己没有定义析构函数则会继承父类的。 

析构函数即使在使用 exit() 终止脚本运行时也会被调用。在析构函数中调用 exit() 将会中止其余关闭操作的运行。
>** Note **: 
析构函数在脚本关闭时调用，此时所有的 HTTP 头信息已经发出。脚本关闭时的工作目录有可能和在 SAPI（如 apache）中时不同。  

- - -

>**Note**: 
试图在析构函数（在脚本终止时被调用）中抛出一个异常会导致致命错误。

#### 5、访问控制（可见性） 
##### 属性的访问控制 
```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private

/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // 可以对 public 和 protected 进行重定义，但 private 而不能
    protected $protected = 'Protected2';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj2 = new MyClass2();
echo $obj2->public; // 这行能被正常执行
echo $obj2->private; // 未定义 private
echo $obj2->protected; // 这行会产生一个致命错误
$obj2->printHello(); // 输出 Public、Protected2 和 Undefined

?> 
```
##### 方法的访问控制
```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    // 声明一个公有的构造函数
    public function __construct() { }

    // 声明一个公有的方法
    public function MyPublic() { }

    // 声明一个受保护的方法
    protected function MyProtected() { }

    // 声明一个私有的方法
    private function MyPrivate() { }

    // 此方法为公有
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}

$myclass = new MyClass;
$myclass->MyPublic(); // 这行能被正常执行
$myclass->MyProtected(); // 这行会产生一个致命错误
$myclass->MyPrivate(); // 这行会产生一个致命错误
$myclass->Foo(); // 公有，受保护，私有都可以执行


/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // 此方法为公有
    function Foo2()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate(); // 这行会产生一个致命错误
    }
}

$myclass2 = new MyClass2;
$myclass2->MyPublic(); // 这行能被正常执行
$myclass2->Foo2(); // 公有的和受保护的都可执行，但私有的不行

class Bar 
{
    public function test() {
        $this->testPrivate();
        $this->testPublic();
    }

    public function testPublic() {
        echo "Bar::testPublic\n";
    }
    
    private function testPrivate() {
        echo "Bar::testPrivate\n";
    }
}

class Foo extends Bar 
{
    public function testPublic() {
        echo "Foo::testPublic\n";
    }
    
    private function testPrivate() {
        echo "Foo::testPrivate\n";
    }
}

$myFoo = new foo();
$myFoo->test(); // Bar::testPrivate 
                // Foo::testPublic
?> 
```
##### 其他对象的访问控制
同一个类的对象即使不是同一个实例也可以互相访问对方的**私有与受保护成员**。这是由于在这些对象的内部具体实现的细节都是已知的。
#### 5、对象继承
>**Note** **: 
除非使用了自动加载，否则一个类必须在使用之前被定义。如果一个类扩展了另一个，则父类必须在子类之前被声明。此规则适用于类继承其它类与接口。  

```php
<?php
class foo
{
    public function printItem($string) 
    {
        echo 'Foo: ' . $string . PHP_EOL;
    }
    public function printPHP()
    {
        echo 'PHP is great.' . PHP_EOL;
    }
}
class bar extends foo
{
    public function printItem($string)
    {
        echo 'Bar: ' . $string . PHP_EOL;
    }
}
$foo = new foo();
$bar = new bar();
$foo->printItem('baz'); // Output: 'Foo: baz'
$foo->printPHP();       // Output: 'PHP is great'
$bar->printItem('baz'); // Output: 'Bar: baz'
$bar->printPHP();       // Output: 'PHP is great'

?> 
```
#### 6、范围解析操作符 （::） 
范围解析操作符（也可称作 Paamayim Nekudotayim）或者更简单地说是一对冒号，可以用于访问静态成员，类常量，还可以用于覆盖类中的属性和方法。
当在类定义之外引用到这些项目时，要使用类名。
自 PHP 5.3.0 起，可以通过变量来引用类，该变量的值不能是关键字（如 `self`，`parent` 和 `static`）。
当一个子类覆盖其父类中的方法时，PHP 不会调用父类中已被覆盖的方法。是否调用父类的方法取决于子类。这种机制也作用于`构造函数`和`析构函数`，`重载`以及`魔术方法`。 
#### 7、Static（静态）关键字 
声明类属性或方法为静态，就可以不实例化类而直接访问。*静态属性不能通过一个类已实例化的对象来访问*（但静态方法可以）。 
就像其他的PHP静变量一样，静态属性只能被初始化为 文字或常量，不能使用表达式。所以可以把属性初始化为整数或数组，但不能初始化为另一个变量或函数返回值，也不能指向一个对象。
静态属性示例：
>自从PHP 5.3.0 起，可以使用一个变量动态调用类  

```php
<?php
/*静态属性使用*/
class Foo
{
	public static $my_staitc = 'foo';
    public function staticValue(){
    	return self::$my_static;
    }
}
class Bar extends Foo
{
	public function fooStatic(){
    	return parent::$my_staitc;
    }
}
print Foo::$my_static;

$foo = new Foo();
print $foo->staticValue();
print $foo->my_static;//undefined "Property" my_static

$bar = new Bar();
$bar->fooStatic();
/*静态方法使用*/
class Foo {
    public static function aStaticMethod() {
        // ...
    }
}

Foo::aStaticMethod();
$classname = 'Foo';
$classname::aStaticMethod(); // 自 PHP 5.3.0 起
?>
```
#### 8、抽象类
继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的访问控制必须和父类中一样（或者更为宽松）。
```php
<?php
abstract class AbstractClass
{
 // 强制要求子类定义这些方法
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);

    // 普通方法（非抽象方法）
    public function printOut() {
        print $this->getValue() . "\n";
    }
}

class ConcreteClass1 extends AbstractClass
{
    protected function getValue() {
        return "ConcreteClass1";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass1";
    }
}

class ConcreteClass2 extends AbstractClass
{
    public function getValue() {
        return "ConcreteClass2";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass2";
    }
}

$class1 = new ConcreteClass1;
$class1->printOut();
echo $class1->prefixValue('FOO_') ."\n";

$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') ."\n";
?> 
```
#### 9、对象接口
使用接口（interface），可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容。 

接口是通过 interface 关键字来定义的，就像定义一个标准的类一样，但其中定义所有的方法都是空的。 

接口中定义的所有方法都必须是公有，这是接口的特性。 

要实现一个接口，使用 implements 操作符。类中必须实现接口中定义的所有方法，否则会报一个致命错误。类可以实现多个接口，用逗号来分隔多个接口的名称。 
> **Note**
> 实现多个接口时，接口中的方法不能有重名。  

- - -

> **Note**
> 接口也可以继承，通过使用 extends 操作符。  

- - -

>**Note**: 
类要实现接口，必须使用和接口中所定义的方法完全一致的方式。否则会导致致命错误。

- - -

##### 常量
> 接口中也可以定义常量。接口常量和类常量的使用完全相同，但是不能被子类或子接口所覆盖。  

```php
<?php
interface a
{
    public function foo();
}

interface b
{
    public function bar();
}

interface c extends a, b
{
    public function baz();
}

class d implements c
{
    public function foo()
    {
    }

    public function bar()
    {
    }

    public function baz()
    {
    }
}
?> 
```
#### 10、Trait 
Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。 

Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。

>**Trait优先级**
>优先顺序是来自当前类的成员覆盖了 trait 的方法，而 trait 则覆盖了被继承的方法。

**Trait**基本用法：
```php
<?php
class Base {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait SayWorld {
    public function sayHello() {
        parent::sayHello();
        echo 'World!';
    }
}

class MyHelloWorld extends Base {
    use SayWorld;
}

$o = new MyHelloWorld();
$o->sayHello();		//输出：Hello World!

?> 
```  
##### 冲突的解决 
如果两个 trait 都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。 

为了解决多个 trait 在同一个类中的命名冲突，需要使用 `insteadof` 操作符来明确指定使用冲突方法中的哪一个。 

以上方式仅允许排除掉其它方法，`as` 操作符可以将其中一个冲突的方法以另一个名称来引入。 

Example 冲突的解决

在本例中 Talker 使用了 trait A 和 B。由于 A 和 B 有冲突的方法，其定义了使用 trait B 中的 smallTalk 以及 trait A 中的 bigTalk。 

Aliased_Talker 使用了 `as` 操作符来定义了 talk 来作为 B 的 bigTalk 的别名。 

**使用` as `语法还可以用来调整方法的访问控制。**

```php
<?php
trait A {
    public function smallTalk() {
        echo 'a';
    }
    public function bigTalk() {
        echo 'A';
    }
}

trait B {
    public function smallTalk() {
        echo 'b';
    }
    public function bigTalk() {
        echo 'B';
    }
}

class Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
    }
}

class Aliased_Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        B::bigTalk as protected talk;
        B::smallTalk as private;
    }
}
?> 
```
##### 从trait组成trait
```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World!';
    }
}

trait HelloWorld {
    use Hello, World;
}

class MyHelloWorld {
    use HelloWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
?> 
```
##### Trait 的抽象成员
```php
<?php
trait Hello {
    public function sayHelloWorld() {
        echo 'Hello'.$this->getWorld();
    }
    abstract public function getWorld();
}

class MyHelloWorld {
    private $world;
    use Hello;
    public function getWorld() {
        return $this->world;
    }
    public function setWorld($val) {
        $this->world = $val;
    }
}
?> 
```
##### Trait 的静态成员
静态变量的使用：
```php
<?php
trait Counter{
	public function inc(){
    	static $c = 0;
        $c = $c+1;
        echo $c . "\n";
    }
}
class C1{
	use Counter;
}
class C2{
	use Counter;
}

$o = new C1();
$o->inc();  //echo 1;
$p = new C2;//echo 1;
$p->inc();

?>
```

静态方法的使用：
```php

<?php
trait StaticExample {
    public static function doSomething() {
        return 'Doing something';
    }
}

class Example {
    use StaticExample;
}

Example::doSomething();
?> 
```
##### 属性
```php
<?php
trait PropertiesTrait {
    public $x = 1;
}

class PropertiesExample {
    use PropertiesTrait;
}

$example = new PropertiesExample;
$example->x;
?> 
```

> ** Note:**
> 如果 trait 定义了一个属性，那类将不能定义同样名称的属性，否则会产生一个错误。如果该属性在类中的定义与在 trait 中的定义兼容（同样的可见性和初始值）则错误的级别是 E_STRICT，否则是一个致命错误。 

#### 11、匿名类

PHP 7 开始支持匿名类。 匿名类很有用，可以创建一次性的简单对象。

可以传递参数到匿名类的构造器，也可以扩展（extend）其他类、实现接口（implement interface），以及像其他普通的类一样使用 trait：  

```php
<?php

class SomeClass {}
interface SomeInterface {}
trait SomeTrait {}

var_dump(new class(10) extends SomeClass implements SomeInterface {
    private $num;

    public function __construct($num)
    {
        $this->num = $num;
    }

    use SomeTrait;
}); 
?>
```
匿名类被嵌套进普通 Class 后，不能访问这个外部类（Outer class）的 private（私有）、protected（受保护）方法或者属性。 为了访问外部类（Outer class）protected 属性或方法，匿名类可以 extend（扩展）此外部类。 为了使用外部类（Outer class）的 private 属性，必须通过构造器传进来!

#### 12 、重载

PHP所提供的"重载"（overloading）是指*动态*地"创建"类属性和方法。我们是通过魔术方法（magic methods）来实现的。 

当调用当前环境下未定义或不可见的类属性或方法时，重载方法会被调用。本节后面将使用"不可访问属性（inaccessible properties）"和"不可访问方法（inaccessible methods）"来称呼这些未定义或不可见的类属性或方法。 

所有的重载方法都必须被声明为 public。

>**Note: **
这些魔术方法的参数都不能通过引用传递。 

##### 属性重载
	public void __set ( string $name , mixed $value )

	public mixed __get ( string $name )

	public bool __isset ( string $name )

	public void __unset ( string $name )

在给不可访问属性赋值时，`__set()` 会被调用。 

读取不可访问属性的值时，`__get()` 会被调用。 

当对不可访问属性调用` isset()` 或` empty() `时，`__isset()` 会被调用。 

当对不可访问属性调用 `unset() `时，`__unset()` 会被调用。 

参数 `$name `是指要操作的变量名称。`__set()` 方法的` $value` 参数指定了` $name `变量的值。 

属性重载只能在对象中进行。在静态方法中，这些魔术方法将不会被调用。所以这些方法都不能被 声明为 `static`。从 PHP 5.3.0 起, 将这些魔术方法定义为 static 会产生一个警告。

**使用` __get()`，`__set()`，`__isset()` 和 `__unset()` 进行属性重载：**

```php
<?php
class PropertyTest {
     /**  被重载的数据保存在此  */
    private $data = array();

     /**  重载不能被用在已经定义的属性  */
    public $declared = 1;

     /**  只有从类外部访问这个属性时，重载才会发生 */
    private $hidden = 2;

    public function __set($name, $value) 
    {
        echo "Setting '$name' to '$value'\n";
        $this->data[$name] = $value;
    }

    public function __get($name) 
    {
        echo "Getting '$name'\n";
        if (array_key_exists($name, $this->data)) {
            return $this->data[$name];
        }

        $trace = debug_backtrace();
        trigger_error(
            'Undefined property via __get(): ' . $name .
            ' in ' . $trace[0]['file'] .
            ' on line ' . $trace[0]['line'],
            E_USER_NOTICE);
        return null;
    }

    /**  PHP 5.1.0之后版本 */
    public function __isset($name) 
    {
        echo "Is '$name' set?\n";
        return isset($this->data[$name]);
    }

    /**  PHP 5.1.0之后版本 */
    public function __unset($name) 
    {
        echo "Unsetting '$name'\n";
        unset($this->data[$name]);
    }

    /**  非魔术方法  */
    public function getHidden() 
    {
        return $this->hidden;
    }
}

echo "<pre>\n";

$obj = new PropertyTest;

$obj->a = 1;
echo $obj->a . "\n\n";

var_dump(isset($obj->a));
unset($obj->a);
var_dump(isset($obj->a));
echo "\n";

echo $obj->declared . "\n\n";

echo "Let's experiment with the private property named 'hidden':\n";
echo "Privates are visible inside the class, so __get() not used...\n";
echo $obj->getHidden() . "\n";
echo "Privates not visible outside of class, so __get() is used...\n";
echo $obj->hidden . "\n";
?> 
```
以上例程会输出：
```
Setting 'a' to '1'
Getting 'a'
1
Is 'a' set?
bool(true)
Unsetting 'a'
Is 'a' set?
bool(false)
1
Let's experiment with the private property named 'hidden':
Privates are visible inside the class, so __get() not used...
2
Privates not visible outside of class, so __get() is used...
Getting 'hidden'

Notice:  Undefined property via __get(): hidden in <file> on line 70 in <file> on line 29

```
##### 方法重载

	public mixed __call ( string $name , array $arguments )

	public static mixed __callStatic ( string $name , array $arguments )

在对象中调用一个不可访问方法时，`__call()` 会被调用。 

在静态上下文中调用一个不可访问方法时，`__callStatic()` 会被调用。 

`$name` 参数是要调用的方法名称。`$arguments` 参数是一个枚举数组，包含着要传递给方法 `$name` 的参数。

```php
<?php
class MethodTest 
{
    public function __call($name, $arguments) 
    {
        // 注意: $name 的值区分大小写
        echo "Calling object method '$name' "
             . implode(', ', $arguments). "\n";
    }

    /**  PHP 5.3.0之后版本  */
    public static function __callStatic($name, $arguments) 
    {
        // 注意: $name 的值区分大小写
        echo "Calling static method '$name' "
             . implode(', ', $arguments). "\n";
    }
}

$obj = new MethodTest;
$obj->runTest('in object context');

MethodTest::runTest('in static context');  // PHP 5.3.0之后版本
?> 
```

以上例程会输出：

	Calling object method 'runTest' in object context
	Calling static method 'runTest' in static context

#### 13、遍历对象

PHP 5 提供了一种定义对象的方法使其可以通过单元列表来遍历，例如用 foreach 语句。默认情况下，所有可见属性都将被用于遍历。 

```php
<?php
class MyClass
{
    public $var1 = 'value 1';
    public $var2 = 'value 2';
    public $var3 = 'value 3';

    protected $protected = 'protected var';
    private   $private   = 'private var';

    function iterateVisible() {
       echo "MyClass::iterateVisible:\n";
       foreach($this as $key => $value) {
           print "$key => $value\n";
       }
    }
}

$class = new MyClass();

foreach($class as $key => $value) {
    print "$key => $value\n";
}
echo "\n";


$class->iterateVisible();

?> 
```
以上历程会输出:
```
var1 => value 1
var2 => value 2
var3 => value 3

MyClass::iterateVisible:
var1 => value 1
var2 => value 2
var3 => value 3
protected => protected var
private => private var
```

#### 14、魔术方法

`__construct()， __destruct()， __call()， __callStatic()， __get()， __set()， __isset()， __unset()， __sleep()， __wakeup()， __toString()， __invoke()， __set_state()， __clone()` 和` __debugInfo() `等方法在 PHP 中被称为"魔术方法"（Magic methods）。在命名自己的类方法时不能使用这些方法名，除非是想使用其魔术功能。

serialize() 函数会检查类中是否存在一个魔术方法 __sleep()。如果存在，该方法会先被调用，然后才执行序列化操作。此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组。如果该方法未返回任何内容，则 NULL 被序列化，并产生一个 E_NOTICE 级别的错误。 


>Note: 
__sleep() 不能返回父类的私有成员的名字。这样做会产生一个 E_NOTICE 级别的错误。可以用 Serializable 接口来替代。 


`__sleep() `方法常用于提交未提交的数据，或类似的清理操作。同时，如果有一些很大的对象，但不需要全部保存，这个功能就很好用。 

与之相反，`unserialize()` 会检查是否存在一个 `__wakeup()` 方法。如果存在，则会先调用 `__wakeup` 方法，预先准备对象需要的资源。 

`__wakeup()` 经常用在反序列化操作中，例如重新建立数据库连接，或执行其它初始化操作。

```php
<?php
class Connection 
{
    protected $link;
    private $server, $username, $password, $db;
    
    public function __construct($server, $username, $password, $db)
    {
        $this->server = $server;
        $this->username = $username;
        $this->password = $password;
        $this->db = $db;
        $this->connect();
    }
    
    private function connect()
    {
        $this->link = mysql_connect($this->server, $this->username, $this->password);
        mysql_select_db($this->db, $this->link);
    }
    
    public function __sleep()
    {
        return array('server', 'username', 'password', 'db');
    }
    
    public function __wakeup()
    {
        $this->connect();
    }
}
?> 
```

当尝试以调用函数的方式调用一个对象时，`__invoke() `方法会被自动调用。

```php
<?php
class CallableClass 
{
    function __invoke($x) {
        var_dump($x);
    }
}
$obj = new CallableClass;
$obj(5);									//int(5)
var_dump(is_callable($obj));				//bool(true)
?> 
```

##### ** __set_state() **

自 PHP 5.1.0 起当调用` var_export()` 导出类时，此静态方法会被调用。 

本方法的唯一参数是一个数组，其中包含按 `array('property' => value, ...)` 格式排列的类属性。

##### **__debugInfo() **

```php
array __debugInfo ( void )
```

This method is called by `var_dump()` when dumping an object to get the properties that should be shown. If the method isn't defined on an object, then all public, protected and private properties will be shown. 

This feature was added in PHP 5.6.0. 

```php
<?php
class C {
    private $prop;

    public function __construct($val) {
        $this->prop = $val;
    }

    public function __debugInfo() {
        return [
            'propSquared' => $this->prop ** 2,
        ];
    }
}

var_dump(new C(42));
?> 
```
以上例程会输出：
```
object(C)#1 (1) {
  ["propSquared"]=>
  int(1764)
}
```

#### 15、Final 关键字 

PHP 5 新增了一个` final` 关键字。如果父类中的方法被声明为 `final`，则子类无法覆盖该方法。如果一个类被声明为` final`，则不能被继承。

> **Note**: 属性不能被定义为 final，只有类和方法才能被定义为 final。 

```
<?php
class BaseClass {
   public function test() {
       echo "BaseClass::test() called\n";
   }
   
   final public function moreTesting() {
       echo "BaseClass::moreTesting() called\n";
   }
}

class ChildClass extends BaseClass {
   public function moreTesting() {
       echo "ChildClass::moreTesting() called\n";
   }
}
// Results in Fatal error: Cannot override final method BaseClass::moreTesting()
?> 
```
#### 16、对象复制

对象复制可以通过 clone 关键字来完成（如果可能，这将调用对象的 __clone() 方法）。对象中的 __clone() 方法不能被直接调用。 

	$copy_of_object = clone $object;

当对象被复制后，PHP 5 会对对象的所有属性执行一个浅复制（shallow copy）。所有的引用属性 仍然会是一个指向原来的变量的引用。

当复制完成时，如果定义了 __clone()  __ 方法，则新创建的对象（复制生成的对象）中的 __clone() __方法会被调用，可用于修改属性的值（如果有必要的话）。

#### 17、对象比较

当使用比较运算符（==）比较两个对象变量时，比较的原则是：如果两个对象的属性和属性值 都相等，而且两个对象是同一个类的实例，那么这两个对象变量相等。 

而如果使用全等运算符（===），这两个对象变量一定要指向某个类的同一个实例（即同一个对象）。 

#### 18、类型约束

PHP 5 可以使用类型约束。函数的参数可以指定必须为**对象**（在函数原型里面指定类的名字），**接口**，**数组**（PHP 5.1 起）或者 **callable**（PHP 5.4 起）。不过如果使用 `NULL `作为参数的默认值，那么在调用函数的时候依然可以使用` NULL` 作为实参。 

如果一个类或接口指定了类型约束，则其所有的子类或实现也都如此。 

类型约束不能用于标量类型如 `int` 或 `string`。`Traits` 也不允许。 

```php
<?php
//如下面的类
class MyClass
{
    /**
     * 测试函数
     * 第一个参数必须为 OtherClass 类的一个对象
     */
    public function test(OtherClass $otherclass) {
        echo $otherclass->var;
    }


    /**
     * 另一个测试函数
     * 第一个参数必须为数组 
     */
    public function test_array(array $input_array) {
        print_r($input_array);
    }
}

    /**
     * 第一个参数必须为递归类型
     */
    public function test_interface(Traversable $iterator) {
        echo get_class($iterator);
    }
    
    /**
     * 第一个参数必须为回调类型
     */
    public function test_callable(callable $callback, $data) {
        call_user_func($callback, $data);
    }
}

// OtherClass 类定义
class OtherClass {
    public $var = 'Hello World';
}
?> 
```

函数调用的参数与定义的参数类型不一致时，会抛出一个可捕获的致命错误。

类型约束不只是用在类的成员函数里，也能使用在函数里： 

#### 19、后期静态绑定

自 PHP 5.3.0 起，PHP 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。 

准确说，后期静态绑定工作原理是存储了在上一个"非转发调用"（non-forwarding call）的类名。当进行静态方法调用时，该类名即为明确指定的那个（通常在 :: 运算符左侧部分）；当进行非静态方法调用时，即为该对象所属的类。所谓的"转发调用"（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call()。可用 get_called_class() 函数来得到被调用的方法所在的类名，static:: 则指出了其范围。 

该功能从语言内部角度考虑被命名为"后期静态绑定"。"后期绑定"的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。也可以称之为"静态绑定"，因为它可以用于（但不限于）静态方法的调用。 

使用 self:: 或者 __CLASS__ 对当前类的静态引用，取决于定义当前方法所在的类： 

```php
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();      //输出：A
?> 
```

#####  20、后期静态绑定的用法

后期静态绑定本想通过引入一个新的关键字表示运行时最初调用的类来绕过限制。简单地说，这个关键字能够让你在上述例子中调用 test() 时引用的类是 B 而不是 A。最终决定不引入新的关键字，而是使用已经预留的` static `关键字。 

static:: 简单用法
```php
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who(); // 后期静态绑定从这里开始
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();		//输出：B
?> 
```
>**Note: **
在非静态环境下，所调用的类即为该对象实例所属的类。由于 $this-> 会在同一作用范围内尝试调用私有方法，而 static:: 则可能给出不同结果。另一个区别是 static:: 只能用于静态属性。 

非静态环境下使用 static::

```php

<?php
class A {
    private function foo() {
        echo "success!\n";
    }
    public function test() {
        $this->foo();
        static::foo();
    }
}

class B extends A {
   /* foo() will be copied to B, hence its scope will still be A and
    * the call be successful */
}

class C extends A {
    private function foo() {
        /* original method is replaced; the scope of the new one is C */
    }
}

$b = new B();        //success!
$b->test();			   //success!
$c = new C();		 //success!
$c->test();   //fails
?> 

```

后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。另一方面，如果静态调用使用 parent:: 或者 self:: 将转发调用信息。

转发和非转发调用

```php
<?php
class A {
    public static function foo() {
        static::who();
    }

    public static function who() {
        echo __CLASS__."\n";
    }
}

class B extends A {
    public static function test() {
        A::foo();
        parent::foo();
        self::foo();
    }

    public static function who() {
        echo __CLASS__."\n";
    }
}
class C extends B {
    public static function who() {
        echo __CLASS__."\n";
    }
}

C::test();
?> 
```

以上例程会输出：

	A
	C
	C

#### 21、对象和引用 

php的引用是别名，就是两个不同的变量名字指向相同的内容。在php5，一个对象变量已经不再保存整个对象的值。只是保存一个标识符来访问真正的对象内容。 当对象作为参数传递，作为结果返回，或者赋值给另外一个变量，另外一个变量跟原来的不是引用的关系，只是他们都保存着同一个标识符的拷贝，这个标识符指向同一个对象的真正内容。 

#### 22、对象序列化

所有php里面的值都可以使用函数`serialize()`来返回一个包含字节流的字符串来表示。`unserialize()`函数能够重新把字符串变回php原来的值。 序列化一个对象将会保存对象的所有变量，但是不会保存对象的方法，只会保存类的名字。 

为了能够`unserialize()`一个对象，这个对象的类必须已经定义过。如果序列化类A的一个对象，将会返回一个跟类A相关，而且包含了对象所有变量值的字符串。 如果要想在另外一个文件中解序列化一个对象，这个对象的类必须在解序列化之前定义，可以通过包含一个定义该类的文件或使用函数`spl_autoload_register()`来实现。

```php
<?php
// classa.inc:
  
  class A {
      public $one = 1;
    
      public function show_one() {
          echo $this->one;
      }
  }
  
// page1.php:

  include("classa.inc");
  
  $a = new A;
  $s = serialize($a);
  // 把变量$s保存起来以便文件page2.php能够读到
  file_put_contents('store', $s);

// page2.php:
  
  // 要正确了解序列化，必须包含下面一个文件
  include("classa.inc");

  $s = file_get_contents('store');
  $a = unserialize($s);

  // 现在可以使用对象$a里面的函数 show_one()
  $a->show_one();
?> 
```
