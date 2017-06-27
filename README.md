# 贝格尔编排法
贝格尔编排法从1985年起，世界性排球比赛多采用“贝格尔”编排法。其优点是单数队参加时可避免第二轮的轮空队从第四轮起每场都与前一轮的轮空队比赛的不合理现象。

## 编排步骤
一、将参赛队伍按顺序分为俩部分，前一半和后一半各为一部分，若参赛队伍数量为单数则在后面补数字“0”凑成双数。

如：参赛队伍为1、2、3、4、5、6、7等七支队伍，则将其分成```1、2、3、4```和```5、6、7、0```俩部分。

二、分为俩部分后，前一部分由1号开始，自上而下写在左边；后一部分的数自下而上写在右边，然后用横线把相对的号数连接起来。这即是第一轮的比赛。

| l |          | r |
| - |:--------:| -:|
| 1 | \-\-\-\- | 0 |
| 2 | \-\-\-\- | 7 |
| 3 | \-\-\-\- | 6 |
| 4 | \-\-\-\- | 5 |

三、第二轮开始将第一轮右上角的编号(“0”或最大的一个代号数)移到左角上，第三轮又移回右角上，以此类推。即单数轮次时“0”或最大的一个代号在右上角，双数轮次时则在左上角。且每轮将除了“0”或最大一个代号以外的每个位置的数字，沿逆时针方向移动n（n等于(队伍数量-1)/2取整）个位置，当然移动时遇到“0”或最大的一个代号则跳过。

同样以队伍数量7为例子, 其第二轮的对阵表应：


1) 将第一轮右上角的编号(“0”或最大的一个代号数)移到左角上

| l |          |  r  |
| - |:--------:| -:|
| 1(原来) 0(现在) | \-\-\-\- | null |
| 2 | \-\-\-\- | 7 |
| 3 | \-\-\-\- | 6 |
| 4 | \-\-\-\- | 5 |

2) 将除了“0”或最大一个代号以外的每个位置的数字，沿逆时针方向移动3（```(队伍数量-1)/2取整```）个位置，当然移动时遇到“0”或最大的一个代号则跳过。
下表为第二轮比赛的对阵表。

| l |          |  r  |
| - |:--------:| -:|
| 0 | \-\-\-\- | 5 |
| 6 | \-\-\-\- | 4 |
| 7 | \-\-\-\- | 3 |
| 1 | \-\-\-\- | 2 |

3)重复步骤三，直至得出了最后一轮比赛的对阵表(如果比赛队伍数量为单数，则总共轮数为比赛队伍数量，否则，总共轮数为比赛队伍数量-1)

比赛队伍数量为7时，循环赛对阵总表如下：

第一轮|第二轮|第三轮|第四轮|第五轮|第六轮|第七轮
 - | -  | -  | -  | -  | -  | -  
1－0|0－5|2－0|0－6|3－0|0－7|4－0
2－7|6－4|3－1|7－5|4－2|1－6|5－3
3－6|7－3|4－7|1－4|5－1|2－5|6－2
4－5|1－2|5－6|2－3|6－7|3－4|7－1

## 流程图
![流程图](F:/300/git/myProject/bergerMethod/public/bergerFlow.png)

## 伪代码
```pseudocode
begin
input 比赛队伍数量num
set resArr = [], arr = []

// 初始化第一轮的队伍以及轮数等数据
for: i = 1,2,...,num
	resArr.push(i)
set times = num - 1
if num%2 == 1
	resArr[num] = 0
	times = times + 1

// 依次输出各轮对阵队伍
for: count = 1,2,...,times
	output 轮数count

	// 输出本轮对阵队伍
	for: n = 0,1,...,times/2取整
		output 对阵的俩支队伍resArr[n], resArr[times-n]

	// 将第一轮右上角的编号(“0”或最大的一个代号数)移动	
	if count%2 == 0
		arr[0] = resArr[times]
	else 
		arr[times] = resArr[0]
	
	// 求得各个位置的数字移动的步数
	set step = (num-1)/2取整

	// 逆时针移动每个位置数字(除'0'或者最大编号)的位置，并遇到'0'或最大编号跳过
	for: n = 0,1,...,times	
		arr[(n>(times+1-count%2-step?n+3:n+4))%times] =lastArr[n]
	
	// 每轮对阵队伍的更新
	lastArr = arr
	arr = []
end			
```

## JavaScript代码
### ES5
```javascript
/**
 *  使用贝格尔排列法获取比赛对阵表
 *  @param {number} num - 比赛队伍数量
 *  @return {array} - 表示比赛对阵表的三维数组
 */
function bergerMethod(num) {
	// 格式不满足时抛出错误
	if(typeof num !== 'number' || num < 2) throw new Error('输入非数字或小于2');
	
	// 初始化结果数组和比赛轮数
	var resArr = [],
		times = num - 1;

	// 初始化第一轮数组(先创建一定长度的数组，再添加速度上可以更快一些)
	var arr = new Array(num);
	for(var i = 0; i < arr.length; i++) {
		arr[i] = i + 1;
	}
	resArr[0] = arr;
	// 如果队伍数量是单数则将0添加，凑成双数,并且将轮数增1
	if(num%2) {
		arr[num] = 0;
		times++;
	}

	// 求得各个位置的数字需要逆时针移动的步数
	var step = (num-1)/2|0;

	var round = 2;
	do{
		var curArr = [],
			lastArr = resArr[resArr.length - 1];

		// 将第一轮右上角的编号(“0”或最大的一个代号数)移动
		if(round%2) {
			curArr[times] = lastArr[0];
		} else {
			curArr[0] = lastArr[times];
		}

		var len = lastArr.length;
		for(var idx = 0; idx < len; idx++) {
			if(lastArr[idx] === 0 || lastArr[idx] === len) continue;
			// 逆时针移动数字
			curArr[(idx>=len-round%2-step ? idx+4 : idx+3)%len] = lastArr[idx]
		}

		resArr.push(curArr);

		round++;
	} while(round <= times)

	// 将数组中的循环数组映射为对赛数组
	resArr = resArr.map(function(v) {
		var arr = [],
		    len = v.length;
		for(var i = 0; i < len/2; i++) {
			arr.push([v[i], v[len-i-1]]);
		}
		return arr;
	});

	return resArr;
}
```

### ES6
除了用let取代var、箭头函数、以及利用Array.from形成密集数组的小机灵外与ES5版本也没什么不同...
```javascript
/**
 *  使用贝格尔排列法获取比赛对阵表
 *  @param {number} num - 比赛队伍数量
 *  @return {array} - 表示比赛对阵表的三维数组
 */
const bergerMethod = num => {
	// 格式不满足时抛出错误
	if(typeof num !== 'number' || num < 2) throw new Error('输入非数字或小于2');
	
	// 初始化结果数组和比赛轮数
	let resArr = [],
		times = num - 1;

	// 初始化第一轮数组
	let arr = Array.from({length: num}).map((v, i) => i + 1);
	resArr[0] = arr;
	// 如果队伍数量是单数则将0添加，凑成双数,并且将轮数增1
	if(num%2) {
		arr[num] = 0;
		times++;
	}

	// 求得各个位置的数字需要逆时针移动的步数
	let step = (num-1)/2|0;

	let round = 2;
	do{
		let curArr = [],
			lastArr = resArr[resArr.length - 1];

		// 将第一轮右上角的编号(“0”或最大的一个代号数)移动
		if(round%2) {
			curArr[times] = lastArr[0];
		} else {
			curArr[0] = lastArr[times];
		}

		var len = lastArr.length;
		for(let idx = 0; idx < len; idx++) {
			if(lastArr[idx] === 0 || lastArr[idx] === len) continue;
			// 逆时针移动数字
			curArr[(idx>=len-round%2-step ? idx+4 : idx+3)%len] = lastArr[idx]
		}

		resArr.push(curArr);

		round++;
	} while(round <= times)

	// 将数组中的循环数组映射为对赛数组
	resArr = resArr.map(v => {
		var arr = [],
		    len = v.length;
		for(let i = 0; i < len/2; i++) {
			arr.push([v[i], v[len-i-1]]);
		}
		return arr;
	});

	return resArr;
} 
```