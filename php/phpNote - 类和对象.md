# [php 之 类和对象](#aaa)#

### $this 伪变量的示例  ###

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
以上历程会输出：  

	$this is defined (A)
	$this is not defined.
	$this is defined (B)
	$this is not defined.

### 私有状态-封装 {#私有状态-封装 .jack}  
```javascript
	function Range(from,to){
		this.from = function(){return from};
		this.to = function(){return to};
	}
	//定义类的原型
	Range.prototype = {
		constructor:Range,
		includes:function(x){return this.from() <=x && this.to() >=x;},
		foreach:function(f){
			for(var x= Math.ceil(this.from()),max = this.to();x<=max;x++)f(x);
		},
		toString:function(){return "(" + this.from() + "..." + this.to() + ")";}
	};
	var r = new Range(1,5);
	r.from = function(){return -1};
	print(r.toString());	
```
### 子类  
```css  
#aaa{
	color:red;
}
```

- tomcat
- liquanxin
- my name is louis  
[有本事点我](#tomcat)  
我们都有一个家 ,`名字叫中国` 
