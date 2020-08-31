### 链表这种数据结构网上的资料汗牛充栋，本片仅仅作为自己的笔记回顾总结。[本篇示例代码适用Go|Java|JavaScript/演示]
#### 首先来看看单链表
- 定义
	
 单链表是一种链式存取的数据结构，用一组地址任意的存储单元存放线性表中的数据元素。链表中的数据是以结点来表示的，每个结点的构成：元素(数据元素的映象) + 指针(指示后继元素存储位置)，元素就是存储数据的存储单元，指针就是连接每个结点的地址数据。[百度百科](https://baike.baidu.com/item/%E5%8D%95%E9%93%BE%E8%A1%A8/3228368)
 - 最简单示例
 我们使用面向对象的思想实现这个单链表：节点对象/链表对象
 ```js
 //节点
class Node{
    constructor(data,next){
        this.data = data;
        this.next = next;
    }
}
//链表对象
class LinkedList{
    constructor(head){
        this.head = head;
    }
}    
 ```
[注:这里链表对象的头节点可有可无]
- 单链表的增删

[涉及到数据当然需要这种操作啦]
- 1.1 增加节点
增添的思路及遍历,找到增添的位置,挂载节点
```js
class LinkedList{
  //添加方法，接收一个node
  add(node){
      if(this.head === undefined){
        this.head = node;
        return;
      }
      let temp = this.head;
      while(true){
          //如果当前链表没有数据
          if(temp.next === undefined){
              break;
          }
          temp = temp.next;
      }
      temp.next = node;
  }
}
```
如果根据链表的节点标号(节点设有此属性)进行插入操作，思路同样遍历，找到合适的位置，然后插入节点
```js
//根据节点序号添加节点

class LinkedList{
    addByOrder(node){
   	  if(this.head === undefined){
        this.head = node;
        return;
      }
      let temp = this.head;
      //用于标识节点是否找到
      let flag = false;
      while(true){
          if(temp.next === undefined){
              break;
          }
          if(temp.next.no > node.no){
              break;
          }else if(temp.no == node.no){
              flag = true;
              break;
          }
          temp = temp.next;
      }
      if(flag){
          console.log("待插入的节点已经存在！");
      }else{
           node.next = temp.next;
           temp.next = node;
      }
  }
 }   
```
- 1.2 删除节点

删除节点的思路也是遍历,不过要处理一下边界的问题
```js
class LinkedList{
  del(node){
    if(this.head === undefined){
      console.log("链表为空,不能删除");
      return;
    }
    let temp = this.head;
    let flag = false;
    while(true){
       if(temp.next === undefined){
           break;
       }
       if(temp.next === node){
           flag = true;
           break;
       }
       temp = temp.next;
   } 
   if(flag){
       //删除该节点
       temp.next = temp.next.next;
    }else{
       console.log("未找到该节点！");
   }
  }
}
```
[节点的更新，查找逻辑也是以上差不多的思路,离不开遍历或暂存指针的小操作]

是不是觉得挺简单的？那下面来些比较有趣小例题。
- 单链表的反转
- 2.1 头插法实现
头插法实现即设置一个头节点，将原链表的节点有序的插在头节点的后面，实现链表的反转。showCode
```js
class LinkedList{
  //单链表的反转1:通过构造头指针来反转
  reverse(){
    let node = new Node(0);
    //指针
    let cur = this.head.next;
    //缓存cur.next,用于迭代
    let temp = undefined;
    while(true){
      if(cur === undefined){
        break;
      }
      //缓存cur.next,然后执行插入操作
      temp = cur.next;
      cur.next = node.next;
      node.next = cur;
      cur = temp;
      }
      let reverseLinkedList = new LinkedList(node);
      return reverseLinkedList;
    }
}
```
- 2.2 三指针法实现单链表的反转

三指针法实现单链表的反转是利用三指针"记忆链表节点的前序节点"。showCode
```js
class LinkedList{
//三指针法实现单链表的反转
reverse2(){
    let cur = this.head;
    let temp = undefined;
    let point = undefined;
    while(true){
        if(cur == undefined){
            break;
        }
        point = temp;
        temp = cur;
        cur = cur.next;
        temp.next = point;
    }
    let reverseLinkedList = new LinkedList(temp);
    return reverseLinkedList;
    }
}
```
当然,利用栈这种数据结构同样也可以实现单链表的反转,思路是顺序将节点入栈,出栈顺序即单链表的反转顺序。
- 3.1 如何判断链表上是否含有环

判断链表是否含有环的思路是构造两个指针,一个快指针(一次向后移动两位),一个慢指针(一次向后移动一位),当快指针与慢指针相遇时(注意边界处理)即链表上有环。showCode：
```js
class LinkedList{
//判断链表是否有环
  hasCircle(){
      if(this.head === undefined){
          console.log("链表为空！");
          return;
      }
      let slow = this.head;
      let fast = this.head;
      let flag = false;
      while(true){
          if(slow.next === undefined||fast.next.next === undefined){
              break;
          }
          //快指针
          slow = slow.next;
          //慢指针
          fast = fast.next.next;
          if(slow.no === fast.no){
              flag = true;
              break;
          }
      }
      if(flag){
          console.log("此链表有环");
      }else{
          console.log("此链表无环");
      }
  }
}
```
- 3.2 如何判断链表上环的入口位置？

思路是在快指针与慢指针相遇的位置设定一个指针,在链表开头位置设定一个指针,是两个指针同时遍历,当它们相遇时即链表环的入口位置.(后续更新证明)
```js
class LinkedList{
//判断环的节点位置
    intersectionPosition(){
      if(this.head === undefined){
          console.log("链表为空！");
          return;
      }
      let slow = this.head;
      let fast = this.head;
      //是否存在标志
      let flag = false;
      while(true){
          if(slow.next === undefined || fast.next.next === undefined){
              break;
          }
          slow = slow.next;
          fast = fast.next.next;
          if(slow.no === fast.no){
              flag = true;
              break;
          }
      }
      if(flag){
          let prt1 = this.head;
          //或者等于fast
          let prt2 = slow;
          while(prt1.no !== prt2.no){
              prt1 = prt1.next;
              prt2 = prt2.next;
          }
          console.log("链表有环，环节点的入口位置为：",prt1.no);
      }else{
          console.log("此链表无环");
      }
    }
}
```
