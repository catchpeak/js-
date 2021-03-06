###手写js，面试常考题
			
			/*
			 **  1.debounce防抖函数
			 */
			function debounce(fn,wait = 100){
				let timer = 0;
				return function(...args){
					if (timer) {clearTimeout(timer)}
					timer = setTimeout(()=>{
						fn.apply(this,args)
					},wait)	
				}	
			}
			
			/*
			 **  2.throttle节流函数
			 */
			function throttle(fn,wait = 100){
				let lastTime = 0
				return function(...args){
					let now = +new Date() //转化为数字
					if(now - lastTime > wait){
						lastTime = now;
						fn.apply(this,args)
					}	
				}	
			}
			
			
			/*
			 **  3.深克隆
			 */
			const oldObj = {a:2,b:[1,2,3]}
			const newObj = JSON.parse(JSON.stringify(oldObj))
			//上面这种方法有局限性，无法实现对函数 、RegExp等特殊对象的克隆
			//会抛弃对象的constructor,所有的构造函数会指向Object
			//对象有循环引用,会报错
			
			function deepCopy(obj){
				if(typeof obj === 'object'){
					var res = obj.constructor == Array ? [] : {};
					for(let i in obj){
						res[i] = typeof obj[i] == 'object' ? deepCopy(obj[i]) : obj[i]
						//递归实现
					}
					
				}else {
					var res = obj;
				}
				return res;
			}
			
			
			/*
			 **  4.instanceOf
			 */
			function myInstanceOf(left,right){
				if (typeof right !== 'function') throw new Error('instance error')
  			if (!left || (typeof left !== 'object' && typeof left !== 'function')) return false
				let proto = left.__proto__;//Object.getPrototypeOf(left)
				while(proto){
					if(proto == right.prototype) return true;
					proto = proto.__proto__
				}
				return false;
			}  //myInstanceOf({a:3},Object)
			
			
			/*
			 **  5.模拟new
			 */
			function myNew(fn,...args){
				let instance = Object.create(fn.prototype)
				let res = fn.apply(this,args)
				return typeof res === 'object' ? res : instance;	
			}
			
			
			/*
			 **  6.实现call
			 */
			Function.prototype.myCall = function(context = window){
				context.fn = this; 
				// let fn = Symbol('fn'); context[fn] = this;delete context[fn];
				let args = arguments.slice(1);
				let res = context.fn(...args);
				delete context.fn;
				return res	
			}
			
			
			
			/*
			 **  7.实现apply
			 */
			Function.prototype.myApply = function(context = window,args){
				context.fn = this;
				let res = context.fn(...args)
				delete context.fn
				return res	
			}
			
			
			/*
			 **  8.实现bind
			 */
			Function.prototype.myBind = function(context){
				if(typeof this !== 'function'){ throw new Error('error')}
				const _this = this;
				const args = [...arguments].slice(1)
				return F(){
					if(this instanceof F){
						return new _this(...args,...arguments)
					}
					return _this.apply(context,args.concat(...arguments))
				}
			}
			
			
			/*
			 **  9.实现Object.create
			 */
			function create(proto){
				function F(){}
				F.prototype = proto;
				return new F();
			}
			
			
			/*
			 **  10.实现JSON.stringify
			 */
			function jsonStringify(obj){
				let type = typeof obj;
				if(type !== 'object'){
					if(/string|undefined|function/.test(type)){
						obj = '"' + obj + '"'
					}
					return String(obj)
				}else {
					let json = []
					let arr = Array.isArray(obj)
					for(let k in obj){
						let v = obj[k]
						let type = typeof v;
						if(/string|undefined|function/.test(type)){
							v = '"' + v + '"'
						}else if(type === 'object'){
							v = jsonStringify(v);
						}
						json.push((arr ? "" : '"' + k + '":') + String(v))
					}
					return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}")
				}	
			}
			
			
			/*
			 **  11.实现JSON.parse
			 */
			function jsonParse(opt) {
    		return eval('(' + opt + ')');
			}
			
			/*
			 **  12.实现jsonp
			 */
			var newscript = document.createElement('script');
			newscript.src = 'https://www.adb.com?callback=fn'
			document.body.appendChild(newscript);
			function fn(data) {
  			console.log(data);
			}
			
			
			/*
			 **  13.实现Promise.all
			 */
			function myPromiseAll(arr){
				return new Promise((resolve,reject)=>{
					let len = arr.length;
					let count = 0;
					let res = [];
					for(let i = 0; i < len; i++){
						Promise.resolve(arr[i]).then(val=>{
							res[i] = val;
							count ++;
							if(count === len){
								resolve(res)
							}
						}).catch(error=>{ reject(error)})
					}
				})	
			}
			
			
			/*
			 **  14.实现Promise.race
			 */
			function myPromiseRace(arr){
				return new Promise((resolve,reject)=>{
					arr.forEach(item =>{
						Promise.resolve(item).then(val => resolve(val), err => reject(err))
					})
				})
			}
			
			
			/*
			 **  15.转化为驼峰命名
			 */
			var f = function(s){
				return s.replace(/-\w/g, function (x){
					return x.slice(1).toUpperCase()
				})
			}
			
			
			/*
			 **  16.实现数组扁平化
			 */
			let ary = [1, [2, [3, [4, 5]]], 6];
			let str = JSON.stringify(ary);
			arr = str.replace(/(\[|\])/g,'').split(',')
			
			
			/*
			 **  17.实现函数柯里化
			 */
			function sum(...args1){
				let x = args1.reduce((pre,next)=>{ return pre + next;})
				return function(...args2){
					if(args2.length == 0) return x;
					let y = args2.reduce((pre,next)=>{ return pre + next;})
					return sum(x+y)
				}
			}
			
			
			/*
			 **  18.实现千位分隔符，保留3位小数
			 */
			function parseToMoney(num) {
  			num = parseFloat(num.toFixed(3));
 				let [integer, decimal] = String.prototype.split.call(num, '.');
 				integer = integer.replace(/\d(?=(\d{3})+$)/g, '$&,');
 				return integer + '.' + (decimal ? decimal : '');
			}