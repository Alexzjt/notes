# Manacher算法

#### 经典回文算法（中心扩散）O(n^2)

122131221

在每个字符之间加#，例如

#1#2#2#1#3#1#2#2#1#

131393131

每个位置中心扩散开，计算以当前位置为中心的最长回文子串长度。最后结果/2

在每个字符之间加的字符**不需要**在原文没出现过。

#### Manacher算法 O(n)

1. 回文半径 

2. 回文直径

   以某个中心向外扩散，长度就是回文直径，一半就是回文半径

3. 最右回文右边界 R

   初始化为-1

   扩散的右边界

4. 取得最有边界时的中心点 C

分类讨论各种情况

1. 当前位置**i**不在最右回文边界内部，就暴力扩散

2. 如果在最右回文右边界内部，则必有

   **L**左边界 **i'** 当前位置的对称位置 **C**中心点 **i**当前位置 **R**右边界

   1. 如果**i'**的回文区域在C的回文区域即LR之中，那么**i**的回文半径=**i‘**的回文半径
   2. 如果**i'**的回文区域左侧已经超出**L**的范围，那么i的回文半径=**R-i**
   3. **i’**的区域和**L**压线，**i**的回文半径>**i'**的回文半径，则只需要比较**R**的后续字符

```javascript
function manacher(s) {
    const str = addSharpToStr(s);  // 1221 -> #1#2#2#1#
    let r=-1, // 回文右边界的下一个位置，有效区域是r-1
        c=-1, // 中心
        pArray = [], // 回文半径数组
        max=-1; // 扩出来的最大值
    for (let i = 0; i < str.length; i++) {  //每个位置都求回文半径
        // i的至少回文区域先赋值给pArray[i]
        pArray[i] = r > i ? Math.min(pArray[2*c-i], r-i) : 1;
        while(i+pArray[i] < str.length && i-pArray[i]>-1) {
            if (str[i+pArray[i]]===str[i-pArray[i]]) {
                pArray[i]++;
            } else {
                break;
            }
        }
        if (i+pArray[i]>r) {
            r = i+pArray[i];
            c = i;
        }
        max = Math.max(max, pArray[i])
    }
    return max-1;   //例如#1#2#2#1#的max是5，实际结果是4
}

function addSharpToStr(s) {
    let charArray = [], index=0;
    for (let i = 0; i < s.length*2+1; i++) {
        charArray[i] = (i & 1) ? s[index++] : '#';
    }
    return charArray.join('');
}

console.log(manacher("12221"));
```

