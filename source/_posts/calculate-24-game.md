---
title: 24点
date: 2016-07-19 17:15:34
tags: [每周总结]
---

从北戴河回来的时候，和水哥在火车上玩 24 点，玩着玩着就聊到了求 24 点的实现（可怕的程序猿

先考虑简单实现，即只有加减乘除四则运算。很直接的思路就是列出所有可能的表达式，依次求和，最后找出结果等于 24 的情况。

这里涉及到两个关键点，一个是通过数字求出所有表达式，另一个是根据式子计算结果。

求所有表达式可以理解为求四个数字的排列，然后插入运算符即可；根据式子计算结果我们可以利用前缀表达式做到。

<!-- more -->

## 前缀表达式

我们平常使用的式子比如 `3*(5+9)-4`，称为中缀表达式，运算符夹在数字的中间，是人类易于理解的表达式。而计算机很难识别这种表达式，一是括号改变了式子的运行顺序，另外各种运算符之间存在优先级大小关系。

相比而言，前缀表达式是计算机容易计算的，像上面这个例子，如果用前缀表达式来描述则是 `-*3+594`。可以看到前缀表达式中去掉了括号，运算符跑到了计算数字的左边。

### 中缀表达式转换为前缀表达式

中缀表达式转换为前缀表达式需要经过以下步骤：
1. 从右至左扫描中缀表达式，从右边第一个字符开始判断
  - 如果当前字符是数字，则将数字直接输出。
  - 如果是运算符，栈顶也是运算符，则比较优先级：如果当前运算符的优先级大于等于栈顶运算符的优先级，运算符入栈，如果当前运算符的优先级小于栈顶运算符的优先级，将栈顶运算符弹出并输出，直到当前运算符的优先级大于等于栈顶运算符的优先级。
  - 如果是运算符，栈顶为括号或栈为空，入栈。
  - 如果是右括号，则入栈。
  - 如果是左括号，将所有的运算符全部出栈并输出，直到栈顶为右括号，最后将左右的两括号一起删除。
2. 重复上述操作直至扫描结束，将栈内剩余运算符全部出栈并输出，最后逆序输出字符串。

以 `3*(5+9)-4` 为例，过程如下：
1. 字符为 4
  > 如果当前字符是数字，则将数字直接输出。

  - 输出序列：4
  - 栈：[]

2. 字符为 -
  > 如果是运算符，栈顶为括号或栈为空，入栈。

  - 输出序列：4
  - 栈：[-]

3. 字符为 )
  > 如果是右括号，则入栈。

  - 输出序列：4
  - 栈：[-, )]

4. 字符为 9

  - 输出序列：49
  - 栈：[-, )]

5. 字符为 +
  > 如果是运算符，栈顶为括号或栈为空，入栈。

  - 输出序列：49
  - 栈：[-, ), +]

6. 字符为 5

  - 输出序列：495
  - 栈：[-, ), +]

7. 字符为 (
  > 如果是左括号，将所有的运算符全部出栈并输出，直到栈顶为右括号，最后将左右的两括号一起删除。

  输出序列：495+
  栈：[-]

8. 字符为 *
  > 如果当前运算符的优先级大于等于栈顶运算符的优先级，运算符入栈

  - 输出序列：495+
  - 栈：[-, \*]

9. 字符为 3
  - 输出序列：495+3
  - 栈：[-, \*]

10. 结束
  > 将栈内剩余运算符全部出栈并输出，最后逆序输出字符串。

  - 输出序列：`495+3*-`
  - 逆序：`-*3+594`

所以最后结果为 `-*3+594`。

javascript实现如下：

```javascript
/**
 * 中缀表达式转前缀表达式
 * @param  {string} inExpress 中缀表达式
 * @return {string}           前缀表达式
 */
function translate(inExpress) {
  if(inExpress == '') {
    throw new Error('表达式为空');
  }
  // 输出前缀表达式
  let output = [];
  // 反转
  let chars = inExpress.match(/(\d+)|[^\d]/g).reverse();
  // 存放运算符的栈
  let stack = [];
  for(let ele of chars) {
    if(/\d+/.test(ele)) {
      // 如果是数字，直接输出
      output.push(ele);
    } else if(/[\+\-]/.test(ele)) {
      // 栈顶元素
      let topEle = stack[stack.length - 1];
      if(/[\*\/]/.test(topEle)) {
        // 如果栈顶元素优先级比加减高，依次将优先级高的运算符弹出
        while(stack.length > 0 && /[\*\/]/.test(topEle)) {
          output.push(stack.pop());
          topEle = stack[stack.length - 1];
        }
      }
      stack.push(ele);
    } else if(/[\*\/\)]/.test(ele)) {
      // 如果是乘除或右括号，入栈
      stack.push(ele);
    } else {

      // 如果是左括号，依次弹出至右括号
      let topEle = stack.pop();
      while(topEle !== ')') {
        output.push(topEle);
        topEle = stack.pop();
      }
    }
  }

  // 将栈剩余的元素依次弹出
  while(stack.length > 0) {
    output.push(stack.pop());
  }

  return output.reverse().join(',');
}
```

### 计算前缀表达式

前缀表达式的计算也使用到了栈，规则如下：

1. 从右至左扫描表达式
  - 如果当前字符是数字，入栈
  - 如果当前字符是运算符，栈弹出两个元素，进行运算，并将结果入栈
2. 重复上述过程，直到扫描结束

继续以 `-*3+594` 为例，计算如下：

1. 栈:[4]
2. 栈:[4, 9]
3. 栈:[4, 9, 5]
4. 计算 `5 + 9`，得到结果 14 压入栈中。栈:[4, 14]
5. 栈:[4, 14, 3]
6. 计算 `3 * 14`，得到结果 42 压入栈中。栈:[4, 42]
7. 计算 `42 - 4`，得到结果 38 压入栈中。栈:[38]

最后结果即为 48。

javascript 实现：
```javascript
/**
 * 计算前缀表达式
 * @param  {string} preExpress 前缀表达式
 * @return {number}            结果
 */
function calculate(preExpress) {
  let chars = preExpress.split(',');
  let nums = [];
  while(chars.length > 0) {
    let ele = chars.pop();
    if(/\d+/.test(ele)) {
      nums.push(ele);
    } else {
      let left = +nums.pop();
      let right = +nums.pop();
      switch (ele) {
        case '+':
          nums.push(left + right);
          break;
        case '-':
          nums.push(left - right);
          break;
        case '*':
          nums.push(left * right);
          break;
        case '/':
          nums.push(left / right);
          break;
      }
    }
  }
  return nums[0];
}
```

## 排列
对于 `1, 2, 3, 4`，四个数字，我们知道一共有 `4! = 24` 种排列。

而事实上，这二十四种排列是从第一个数字开始，依次与后面的数字交换而来的。

- 1, 2 交换: `2, 1, 3, 4`
- 1, 3 交换：`2, 3, 1, 4`
- 1, 4 交换：`2, 3, 4, 1`
- 1, 2 交换：`1, 3, 4, 2`
- 1, 3 交换：`3, 1, 4, 2`
- ...依次类推

所以只要在 `n!` 时间内，就能得出所有可能情况

javascript 实现
```javascript
function createExpress(nums) {
  let noBraket = [], oneBraket = [], twoBraket = [];

  let swapIndex = 0;
  for(let i = 0; i < 24; i++) {
    swap(swapIndex);
    swapIndex == nums.length - 1 ? swapIndex = 0 : swapIndex++;
    // nums 即为一个排列
  }

  function swap(i) {
    if(i < nums.length - 1) {
      [nums[i], nums[i + 1]] = [nums[i + 1], nums[i]];
    } else {
      [nums[i], nums[0]] = [nums[0], nums[i]];
    }
  }
}
```

## 添加括号

式子列出之后，需要给表达式添加括号，实际上有括号的可能情况只有两种: 只有一个括号和有两个括号。而每种情况都只有 5 种情况。

1. 一个括号
  - (1 2) 3 4
  - (1 2 3) 4
  - 1 (2 3) 4
  - 1 (2 3 4)
  - 1 (2 3 4)
2. 两个括号
  - (1 2) (3 4)
  - ((1 2) 3) 4
  - 1 (2 (3 4))
  - 1 ((2 3) 4)
  - (1 (2 3)) 4

## TODO

解决了以上问题之后，基本能算出所有的可能情况了，剩下的问题在于如何去除重复，和判断重复情况。

比如 `(1 + 2 + 3) * 5` 和 `5 * (1 + 2 + 3)` 应该判断为同一种情况。

## 所有代码
```javascript
let nums = process.argv.slice(2, 6);

createExpress(nums);
// translate('(3+(3+4)*5');

function createExpress(nums) {
  let noBraket = [], oneBraket = [], twoBraket = [];

  let swapIndex = 0;
  for(let i = 0; i < 24; i++) {
    swap(swapIndex);
    swapIndex == nums.length - 1 ? swapIndex = 0 : swapIndex++;

    // 表达式
    let operTypes = ['+', '-', '*', '/'];
    for (let j = 0; j < operTypes.length; j++) {
      for (let k = 0; k < operTypes.length; k++) {
        for (let l = 0; l < operTypes.length; l++) {
          let express = `${nums[0]}${operTypes[j]}${nums[1]}${operTypes[k]}${nums[2]}${operTypes[l]}${nums[3]}`;
          // console.log(express)
          noBraket.push(express);

          // 当不是全部都是同样优先级时才加括号
          if(!isSamePriority(operTypes[j], operTypes[k], operTypes[l])) {
            // 加一个括号
            oneBraket.push(addBraket(express, {
              0: '(',
              4: ')'
            }));
            oneBraket.push(addBraket(express, {
              2: '(',
              6: ')'
            }));
            oneBraket.push(addBraket(express, {
              4: '(',
              8: ')'
            }));
            oneBraket.push(addBraket(express, {
              0: '(',
              6: ')'
            }));
            oneBraket.push(addBraket(express, {
              2: '(',
              8: ')'
            }));

            // 加两个括号
            twoBraket.push(addBraket(express, {
              0: '(',
              4: ')',
              6: '(',
              10: ')'
            }));
            twoBraket.push(addBraket(express, {
              0: '(',
              1: '(',
              5: ')',
              8: ')'
            }));
            twoBraket.push(addBraket(express, {
              2: '(',
              3: '(',
              7: ')',
              10: ')'
            }));
            twoBraket.push(addBraket(express, {
              0: '(',
              3: '(',
              7: ')',
              8: ')'
            }));
            twoBraket.push(addBraket(express, {
              2: '(',
              5: '(',
              9: ')',
              10: ')'
            }));
          }
        }
      }
    }

    let expresses = noBraket.concat(oneBraket).concat(twoBraket);
    for (let i = 0; i < expresses.length; i++) {
      // if(expresses[i] == '')
      // console.log(expresses[i], calculate(translate(expresses[i])))
      if(calculate(translate(expresses[i])) == 24) {
        console.log(expresses[i]);
      }
    }
  }

  function isSamePriority() {
    let opers = Array.from(arguments).join('');
    if((opers.match(/[\+\-]/g) && opers.match(/[\+\-]/g).length == opers.length)
      || (opers.match(/[\*\/]/g) && opers.match(/[\*\/]/g).length == opers.length)) {
      return true;
    }
    return false;
  }

  function addBraket(express, param) {
    let chars = express.match(/(\d+)|[^\d]/g);
    for(let pos in param) {
      chars.splice(+pos, 0, param[pos]);
    }
    return chars.join('');
  }

  function swap(i) {
    if(i < nums.length - 1) {
      [nums[i], nums[i + 1]] = [nums[i + 1], nums[i]];
    } else {
      [nums[i], nums[0]] = [nums[0], nums[i]];
    }
  }
}

/**
 * 中缀表达式转前缀表达式
 * @param  {string} inExpress 中缀表达式
 * @return {string}           前缀表达式
 */
function translate(inExpress) {
  if(inExpress == '') {
    throw new Error('表达式为空');
  }
  // 输出前缀表达式
  let output = [];
  // 反转
  let chars = inExpress.match(/(\d+)|[^\d]/g).reverse();
  // 存放运算符的栈
  let stack = [];
  for(let ele of chars) {
    if(/\d+/.test(ele)) {
      // 如果是数字，直接输出
      output.push(ele);
    } else if(/[\+\-]/.test(ele)) {
      // 栈顶元素
      let topEle = stack[stack.length - 1];
      if(/[\*\/]/.test(topEle)) {
        // 如果栈顶元素优先级比加减高，依次将优先级高的运算符弹出
        while(stack.length > 0 && /[\*\/]/.test(topEle)) {
          output.push(stack.pop());
          topEle = stack[stack.length - 1];
        }
      }
      stack.push(ele);
    } else if(/[\*\/\)]/.test(ele)) {
      // 如果是乘除或右括号，入栈
      stack.push(ele);
    } else {

      // 如果是左括号，依次弹出至右括号
      let topEle = stack.pop();
      while(topEle !== ')') {
        output.push(topEle);
        topEle = stack.pop();
      }
    }
  }

  // 将栈剩余的元素依次弹出
  while(stack.length > 0) {
    output.push(stack.pop());
  }

  return output.reverse().join(',');
}

/**
 * 计算前缀表达式
 * @param  {string} preExpress 前缀表达式
 * @return {number}            结果
 */
function calculate(preExpress) {
  let chars = preExpress.split(',');
  let nums = [];
  while(chars.length > 0) {
    let ele = chars.pop();
    if(/\d+/.test(ele)) {
      nums.push(ele);
    } else {
      let left = +nums.pop();
      let right = +nums.pop();
      switch (ele) {
        case '+':
          nums.push(left + right);
          break;
        case '-':
          nums.push(left - right);
          break;
        case '*':
          nums.push(left * right);
          break;
        case '/':
          nums.push(left / right);
          break;
      }
    }
  }
  return nums[0];
}
```
