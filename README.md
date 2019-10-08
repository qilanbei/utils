### 分享一些常用的工具类函数，方便在项目中快速开发

设置cookie

```angular2html
 setCookie (cname, cvalue, exdays) {
	let d = new Date()
	d.setTime(d.getTime() + (exdays * 24 * 60 * 60 * 1000))
	let expires = 'expires=' + d.toUTCString()
	document.cookie = cname + '=' + cvalue + '; ' + expires
},
```

获取cookie

```angular2html
getCookie (name) {
	var arr = document.cookie.match(new RegExp("(^| )" + name + "=([^;]*)(;|$)"));
	if (arr != null) return encodeURIComponent(arr[2]); return null;
},

```

判断有效值

```angular2html
objectKeys (object) {
	return object ? Object.keys(object).length : 0
},
isValidObject (object) {
	return utils.objectKeys(object) !== 0
},
isNumber (num) {
	return typeof num === 'number'
},
isValidNumber (num) {
  return utils.isNumber(num) && num !== 0
},
isArray (arr) {
	return typeof arr === 'object' && arr instanceof Array
},
isValidArray (arr) {
	return utils.isArray(arr) && arr.length > 0
},
isString (str) {
	return typeof str === 'string'
},
isValidString (str) {
	return utils.isString(str) && str !== ''
},
```

数组求和

```angular2html
1. 
arrSum (arr) {
	return arr.reduce(function (prev, curr) {
		return parseInt(prev) + parseInt(curr)
	})
}

2. 
arrSum (array) {
	let sum = 0
    array.forEach(function(entry) {
        sum += entry;
    });
	return sum
}
```

数组取交集（交集元素由既属于集合A又属于集合B的元素组成）
```javascript
function intersect(arr1, arr2) {
    if(isArray(arr1) && isArray(arr2)) {
        return arr1.filter(function(v){  
          return arr2.indexOf(v)!==-1  
        })  
    }
}
// 使用方式
intersect([1,2,3,4], [3,4,5,6]); // [3,4]
```

数组取并集（并集元素由集合A和集合B中所有元素去重组成）
```javascript
function union (arr1, arr2) {
    if(isArray(arr1) && isArray(arr2)) {
        return arr1.concat(arr2).unique()
    }
}
// 使用方式
union([1,2,3,4], [1,3,4,5,6]); // [1,2,3,4,5,6]
```

数组取差集 (A的差集：属于A集合不属于B集合的元素；B的差集：属于B集合不属于A集合的元素)
```javascript
function minus (arr1, arr2) {
     if(isArray(arr1) && isArray(arr2)) {
        var interArr = intersect(arr1, arr2);// 交集数组
        return arr1.filter(function(v){
            return interArr.indexOf(v) === -1
        })
    }
}
// 使用方式
minus([1,2,3,4], [2,4]); // [1,3]
```

时间格式化

```angular2html
formatTime: function (nS, format) {
	let val = parseInt(nS) > 10000000000 ? parseInt(nS) : parseInt(nS) * 1000
	let stamp = new Date(val)
	let year = stamp.getFullYear()
	let month = (stamp.getMonth() + 1) > 9 ? (stamp.getMonth() + 1) : '0' + (stamp.getMonth() + 1)
	let day = stamp.getDate() > 9 ? stamp.getDate() : '0' + stamp.getDate()
	let hour = stamp.getHours() > 9 ? stamp.getHours() : '0' + stamp.getHours()
	let minute = stamp.getMinutes() > 9 ? stamp.getMinutes() : '0' + stamp.getMinutes()
	let sec = stamp.getSeconds() > 9 ? stamp.getSeconds() : '0' + stamp.getSeconds()
	if (format) {
		format = format.replace('yyyy', year)
		format = format.replace('MM', month)
		format = format.replace('dd', day)
		format = format.replace('hh', hour)
		format = format.replace('mm', minute)
		format = format.replace('ss', sec)
	} else {
		format = year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + sec
	}
	return format
}
<!--调用：-->
formatTime第一个参数接收时间戳形式

如果 date 是"2019-04-01T11:16:37.249932+08:00"

utils.formatTime(Date.parse(date), 'MM/dd hh:mm')

如果 date 是时间戳的形式 

utils.formatTime(date, 'MM/dd hh:mm')

```

秒时间格式化为h、m、s

```angular2html
<!--value 指秒数-->
formatSeconds(value) {
	let secondTime = parseInt(value);// 秒
	let minuteTime = 0;// 分
	let hourTime = 0;// 小时
	if (secondTime > 60) {
		minuteTime = parseInt(secondTime / 60);
		secondTime = parseInt(secondTime % 60);
		if (minuteTime > 60) {
			hourTime = parseInt(minuteTime / 60);
			minuteTime = parseInt(minuteTime % 60);
		}
	}
	let result = "" + parseInt(secondTime) + "s";

	result = "" + parseInt(minuteTime) + "min " + result;
	if (hourTime > 0) {
		result = "" + parseInt(hourTime) + "h " + result;
	}
	return result;
}
formatSeconds(3670) 
<!--输出"1h 1min 10s"-->

```

格式化为带单位的数据（字节，bps等）

参数介绍： 
- size 为需要格式化的数值
- sizeSuffix 为需要格式化单位的数组 Array
- divisor 为需要整除的单位差值 number 默认1000
- nospace 为输出的数值去掉空格
- places 小数点位数 默认为1

```angular2html
prettySize (size, sizeSuffix, divisor, nospace, one, places) {
	// divisor number
	// sizeSuffix Array  单位
	if (typeof nospace === 'object') {
		const opts = nospace
		nospace = opts.nospace
		one = opts.one
		places = opts.places || 1
	} else {
		places = places || 1
	}

	let mysize
	sizeSuffix.forEach((unit, id) => {
		if (one) {
			unit = unit.slice(0, 1)
		}
		const s = Math.pow(divisor || 1000, id)
		let fixed
		if (size >= s) {
			fixed = String((size / s).toFixed(places))
			if (fixed.indexOf('.0') === fixed.length - 2) {
				fixed = fixed.slice(0, -2)
			}
			mysize = fixed + (nospace ? '' : ' ') + unit
		}
	})
	if (!mysize) {
		let unit = (one ? sizeSuffix[0].slice(0, 1) : sizeSuffix[0])
		mysize = '0' + (nospace ? '' : ' ') + unit
	}

	return mysize
}
```

格式化字节(/1024)；格式化bps(1000)

```angular2html
setPrettyBytes (size) {
	let sizes = ['Byte', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB']
	return prettySize(size, sizes, 1024)
}
setPrettyBps (bps) {
	let sizes = ['bps', 'Kbps', 'Mbps', 'Gbps']
	return prettySize(bps, sizes, 1000)
},
```

格式化IP为 127.*.*.1

```angular2html
 normalizeIp(ip) {
	let ipItems = ip.split('.')
	ipItems.forEach(function (item, index) {
		if (index === 0) {
			ipItems[index] = item + '.'
		} else if (index < ipItems.length - 1) {
			ipItems[index] = '*.'
		}
	})
	return ipItems.join('')
}
```
返回对象中的元素

```angular2html
getObjectValue (object, path, defaultValue) {
	defaultValue = (typeof defaultValue === 'undefined') ? null : defaultValue
	let obj = object
	if (typeof path === 'string' && path.trim() !== '') {
		let keys = path.split('.')
		keys.forEach(function (key) {
			if (obj !== null && typeof obj[key] !== 'undefined' && obj[key] !== null) {
				obj = obj[key]
			} else {
				obj = defaultValue
			}
		})
	}
	return obj
}

例如： let obj = {info: {name: "utils", age: 12}}
console.log(getObjectValue(obj, "info.name"))
<!--输出utils-->
```

### 整理ing
