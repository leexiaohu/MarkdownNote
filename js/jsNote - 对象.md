# [JavaSript 之 对象]()#

###对象的创建
- 对象直接量  
#
	var o = {x:1,y:null}
- 通过**new**关键字
#
	var o = new Object();
	var a = new Array();
	var d = new Date();
- Object.create()
#
	var o1 = Object.create({x:1,y:2});
	var o2 = Object.create(null); //不继承任何属性和方法
	var o3 = Object.create(Object.prototype); //创建普通的空对象,类似于：new Object()
###属性的查询和设置
	var o = {x:1,y:2}
	o.x = 2;
	o["y"] = 4;

###删除属性
	delete book.author
	delete book["main titile"]
###检测属性
	var o = {x:1}
	"x" in o //true
	"y" in o //false
	"toString" in o //true toString 是继承属性 
###
	var o = {x:1}
	o.hasOwnProperty("x");//true
	o.hasOwnProperty("y");//false
	o.hasOwnProperty("toString"); //false toString 是继承属性  
###
- **propertyIsEnumerable()**是**hasOwnProperty()**的增强版，只有检测到是自有属性，且这个属性可枚举性为true时才返回true  
- 除了使用**in**来判断，另外一种简单的方法是使用"!=="来判断属性是否为**undefined**:
###  
	var o = {x:undefined}
	o.x !== undefined //false
	o.y !== undefined //false
    "x" in o //true
	"y" in o //false
	delete o.x
	"x" in o //false

###属性getter和setter方法

	var serialNum ={
		//这个数据属性包含下一个序列号
		//$符号暗示这个属性是一个私有属性
		$n:0,
		get next(){return this.$n++;}//返回这个值，然后自增
		set next(n){
			if(n > this.$n) this.$n = n;
			else throw "序列号的值不能比当前值小";
		}
	};
#
###属性的四个特性
- 值
- 可写性
- 可枚举性
- 可配置性
####查询属性的特性
	//返回 {value:1,writable:true,enumerable:true,configurable:true}
	Object.getOwnPropertyDescriptor({x:1},"x");

	/* 对于继承属性和不存在的属性，则返回undefined */
	Object.getOwnPropertyDescriptor({x:1},"y");//返回 undefined
####设置属性的特性
调用**Object.defineProperty()**方法

	var o = {}
	Object.defineProperty(o,"x",{value:1,writable:true,enemutable:false,
								 configurable:true})
	o.x;			//=>1
	Object.keys(o);	//=>[]
#
###对象的三个属性

- 原型属性
#
	var p= {x:1}
	var o = Object.create(p);
	p.isPrototypeOf(o);   // =>true       o继承自p
	Object.prototype.isPrototypeOf(o);   // =>true:p 继承自Object.prototype
- 类
#
	function classOf(o){
		if(o===null) return "NULL";
		if(o===undefined) return "Undefined";
		return Object.prototype.toString.call(o).slice(8,-1);
	}
- 可扩展性  
阻止可扩展性的三个方法  
>- Object.preventExtentions();  
>- Object.seal();  
>- Object.freeze();

###对象序列化和反序列化
*JSON.stringify()*只能序列化对象的可枚举的自有属性，不能序列化的属性在序列化的输出字符串中将吧这个属性省略掉

	o = {x:1,z:{y:["null",false,""]}};
	s = JSON.stringify(o);   //对象转化为字符串
	p = JSON.parse(s);       //p是o的深拷贝

###对象方法
- toString() 
- toLocaleString()
- toJSON()
- valueOf()	//数字对象比较有用

