# [JavaSript 之 数组]()#

数组包含一个特殊的**length**属性，数组对象自动维护该属性。

###创建数组
	var a = [1.2,2,false,null,{x:1,y:2},]
	var undefs = [,,]   //创建了三个元素，都是undefined
	var a = new Array(20);
###稀疏数组
	var a = new Array();
	var a1 = [];
	a1[1000] = 12;

###数组元素添加和删除
	a=[];
	a[0] = "one";
	a[1] = "two";

	a.push("three");
	a.push("four",false);
	
	delete a[1];
	"two" in a;//false
###数组遍历

	var keys = Object.keys(o);
	var values = [];

	//常规查询
	for(var i=0,len = keys.length;i<len;i++){
		var key = keys[i];
		values[i] = o[key];
	}

	//排除null、undefined和不存在的元素
	for(var i=0,len = a.length;i<len;i++){
		if(!a[i]) continue;
	}

	//排除undefined和不存在的元素
	for(var i=0,len = a.length;i<len;i++){
		if(a[i] === undefined) continue;
	}

	//排除不存在的元素
	for(var i=0,len = a.length;i<len;i++){
		if(!(i in a)) continue;
	}

	//使用 for/in 循环处理稀疏数组，每次将一个可枚举的属性名赋值给循环变量，但是 for/in 能够枚举继承的属性名
	for(var index in sparceArr){
		if(!a.hasOwnProperty(index)) continue;//排除继承的属性
	}
###数组方法

- join()  
Array.join()方法将数组中的所有方法转化为字符串并连接到一起，返回生成的字符串，可以自己指定分隔符，它是split()方法的逆向操作  
######
	var a = [1,2,3]
	a.join();//"1,2,3"
	a.join(" "); //"1 2 3"
- reverse()    
- sort()
- concat()
#
	var a =[1,2,3]
	a.concact(4,5);//1，2，3，4，5
	a.concact({4,5]);//1，2，3，4，5
	a.concact({4,5],[6,7]);//1，2，3，4，5,6,7
	a.concact({4,[5,[6,7]]);//1，2，3，4，5,[6,7]
- slice()  
返回两个参数之间的数组元素，包含第一个但不包含第二个，-1：倒数第一个，-2：倒数第二个
- splice()  
它的第一个参数：指定删除（插入）元素的位置，第二个参数：指定删除元素的个数，后面的参数：指定插入的元素，返回删除的元素组成的数组，此函数会修改数组
- push() 和 pop()  
在**尾部**插入或删除元素，数组当做*栈*使用
- unshift() 和 shift()  
在**头部**插入或删除元素
- toString() 和 toLocaleString()
###ECMAScript 5中的函数
- forEach()
- map() 
# 
	//该函数有返回值
	a = [1,2,3]
	b = a.map(function(x){return x*x;})//b是1，4，9
- fileter()  
该函数是返回调用调用的数组的一个子集，传递的函数参数是用来逻辑判定的，该函数返回true或false
#
	//该函数有返回值
	a = [1,2,3]
	b = a.map(function(x){return x<2;})//b是1
- every() 和 some()  
这两个函数是数组的逻辑判定，对数组应用指定的函数进行判断，返回true或false.
every()相当于“所有”量词，some相当于“存在”量词
- reduce() 和 reduceRight()  
使用指定的函数将数组元素组合，生成单个值。  
#
	//第一个参数是执行简化参数操作的函数，第二个是传递给函数的初始值。
	a = [1,2,3]
	var sum = a.reduce(function(x,y){return x+y},0);//数组求和
	var product = a.reduce(function(x,y){return x*y},1);//数组求积
	var sum = a.reduce(function(x,y){return (x>y)?x:y;});//数组求最大值
- indexOf 和 lastIndexOf()  
返回具有给定元素值得索引位置，找不到就返回-1

![](http://i.imgur.com/lIL74MN.jpg)