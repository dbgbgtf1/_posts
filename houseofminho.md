---
title: houseofminho 
date: 2024-08-01 11:29:00
tags: house系列
categories: ctf
---
# 前言
`houseofminho`最早是在宁波赛上遇到的，没想到这玩意给了我迎头痛击。当时老觉得这些过于复杂的堆利用有点钻牛角尖了，实战意义不大，但是现在想想，还是得认真学这些。

这种复杂的堆利用如果一直畏难的话，那我就一直不会有长进，但是啃下来之后却也觉得还能接受了。
# 题目详情
[这是完整题目附件](houseofminho.zip)
通过出题人给的源码我们可以发现，我们至始至终最多只能操控一个堆块，并且大小只能是0x40或0x80。尽管题目在0x40的堆块读入时给了0x80的大溢出，但是我们的利用还是有很大麻烦。
`houseofminho`的攻击思路是
1. 将一个A堆块放进`unsortedbin`
2. 将A堆块`size`位改到`smallbin`的`size`
3. 然后通过申请更大的堆块来将A堆块放入`smallbin`
4. 在`smallbin`中通过`bk`指针来伪造一个单向链表(`fd`指针不需要改动)
![这是伪造后的结果](smallbin.png)
1. 通过0x80的堆块申请命中`smallbin`(要求此时没有命中`tcachebin`，不过这个条件这题很好满足)
2. 如上操作后`glibc`堆管理机制会将`smallbin`填充入`tcachebin`，于是`tcachebin`里面就有了足够多的堆块来给我们申请任意地址写(原来的0x80堆块可以通过改`size`来让他掉入其他`bin`，就可以访问到被改了`fd`之后的堆块了)

我这里也只是简单写一下思路，在这题中还有一些更具体的操作我没有讲到
更完整详细的解题思路可以去看看[看雪Csome的记录](https://bbs.kanxue.com/thread-279588.htm)

[这是完整exp](minho.py)
