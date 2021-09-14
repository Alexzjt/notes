# KMP

字符串匹配算法。

入参：两个字符串str1和str2,

返回：str2在str1中的位置

1. 先求next数组，0位置为-1,1位置为0。代表某个位置之前字符的重复情况，即index位置的前next[index]个字符和从0开始的next[index]个字符相同

```javascript
function kmp(str1, str2) {
    if (!str1 || !str2 || str2.length > str1.length)
        return -1;
    let i1=0,i2=0;
    let next = getNext(str2);
    while (i1<str1.length && i2<str2.length) {
        if (str1[i1]===str2[i2]) {
            i1++;
            i2++;
        } else if(i2===0) { // 或者next[i2]===-1  表示str2比对的位置已经无法往前跳了
            i1++;
        } else {
            i2=next[i2];
        }
    }
    return i2===str2.length ? i1-i2: -1;
}

function getNext(str) {
    if (str.length === 0)
        return [-1];
    let next = [-1, 0];
    let cn=0, i=2;
    while (i<str.length) {
        if (str[cn] === str[i-1]) {
            next[i++] = ++cn;
        } else if (cn > 0) {
            cn = next[cn];
        } else {
            next[i++] = 0
        }
    }
    return next;
}

console.log(kmp("abaabcababaccc", "ccc"));
```

