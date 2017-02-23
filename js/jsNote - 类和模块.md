# [JavaSript 之 类和模块](#私有状态-封装)#

### 定义和使用类  ###
```javascript
	//通过构造函数定义类
	function Set(){
		this.value = {};
		this.n = 0;
		this.add.apply(this,arguments);
	}
	//扩展类的原型
	Set.prototype.add = function(){
		for(var i=0;i<arguments.length;i++){
			var val = arguments[i];
			if(!this.value.hasOwnProperty(val)){
				this.value[val] = val;
				this.n++;
			}
		}
		return this;
	};
	Set.prototype.toString = function(){
		for(var p in this.value){
			console.log(this.value[p]);
		}
	};
	Set.prototype.delete = function(val){
		delete this.value[val];
	};

	console.log("s:");
	var s = new Set("tom","jack","liquanxin");
	s.add("tom1");
	s.toString();
	if( s instanceof Set){
		throw new Error("s is not instance of Set!");
	}
	console.log(s instanceof Set);
	console.log(s.constructor);
```
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
