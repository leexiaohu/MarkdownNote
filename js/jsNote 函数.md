# [JavaSript 之 函数]()#

###关键字*`this`*的作用域
和变量不同，关键字 **this** 没有作用域的限制，嵌套的函数不会从调用它的函数继承**this** 。如果嵌套函数作为方法调用，其**this**指向调用它的对象。如果嵌套函数作为函数调用，其 **this** 不是全局对象（`非严格模式下`）就是**undefined**(`严格模式下`)。如果你想访问外部函数的 **this**值，需要将 **this** 的值保存在一个变量里，这个变量和内部函数同在一个作用域中。例如:
  
	var o = {
		m: function(){
			var self = this;
			console.log(self == o);//true
			f();
		}
		function f(){
			console.log(this == o);//false, this不是全局对象就是undefined
			console.log(self == o);//true, self 指向外部函数的this值
		}
	}

### 作为命名空间的函数 ###
	//匿名函数，声明同时调用
	var extend = (function(o){
		//模块代码实现
		for(var p in o){
			console.log("the index is " + p);
		}
	}());

### 闭包 ###
- **函数作用域**和声明提前（没有**块级作用域**）
- 作为属性的变量
#### 作用域
 当声明一个全局变量时，实际上是定义了一个全局变量的属性。当使用`var`声明一个变量时，创建的这个属性是不可配置的，也就是说这个属性不可以通过`delete`删除。但是如果在非严格模式下并给一个未声明的变量赋值的话，`Javascript` 会自动创建一个全局变量，这种全局变量是可以删除的。  

	var truevar = 1;//声明一个不可删除的全局变量
	fakervar = 2;//创建全局对象的一个可删除的属性
	this.fakervar2 = 3;//同上
	delete truevar;//false 变量没有被删除
	delete fakervar;//true
	delete fakervar2;//true
	//Javascript 全局变量是全局对象的属性。  
- 作用域链  

**函数定义时的作用域链到函数执行时依然有效。**  

    //利用闭包实现的私有属性存取器方法
    function addPrivateProperty(o,name,predicate){
    	var value;
		var self = this;//保存临时变量，供内部嵌套函数使用
		var outerArguments = arguments;//保存临时变量，供内部嵌套函数使用
    	//getter 方法
    	o["get" + name] = function (){return value;}
		//setter 方法
    	o["set" + name] = function(v){
    		if(predicate && !predicate(v))
    			throw Error("set" + name + ":invalid value " + v);
    		else
    			value = v;
    	}
    }
    //函数addPrivateProperty()的使用
    var o = {}
    addPrivateProperty(o,"Name",function(x){return typeof x == "string";})
    
    o.setName("Frank");//设置属性
    o.getName();//得到属性
    o.setName(o);//试图设置一个错误类型的值

## 函数的方法和构造函数    
### call()方法和apply()方法 ###
call()和applay()可以看做是某个对象的方法，通过调用方法的形式来间接调用函数。  

	f.call(0,1,2)
	f.call(o,[1,2])
	var biggest = Math.max.apply(Math,array_of_nums);//计算数组中的最大值



	