# css - 效果



## 





## 过渡 transition



- 什么是过渡（`transition`)？
  - 在一段时间内，元素样式从某一样式变化到另一方式



- 样式
  
  - 设置所有属性：
    
    - `transition`, 设置过渡相关的所有属性，属性没有次序要求	
    
    - 只有一个要求，同时设置延迟时间、持续时间的话，第一个时间是延迟时间，第二个是持续时间
    
      `transition: 1s steps(3) 2s`
    
  - 设置单个属性
  
    - `transition-property`，
    -  `transition-duration` 必须和 `transition-property`同时使用
    - `transition-timing-function`
      - `steps(n,m)`分布执行
        - `steps(1)`
        - `steps(2)`
        - `steps(3, end)`（第二值，默认：end）
        - `steps(3, start)`
  



**示例**

```css
	  .box2{
            width: 20px;
            height: 20px;
            background-color: #bfa;
            margin-top: 20px;

            /* 可以写多个属性 */
            transition-property: width, height, backgroud-color;
            /* 需要带上单位，支持的单位： s, ms */
            transition-duration: 1s;

            /* 可以写 all，表示所有属性 */
            transition-property: all;
            transition-duration: 1000ms;

            /* 这个属性暂时不需要知道 */
            transition-timing-function: cubic-bezier(0.075, 0.82, 0.165, 1);

            /* delay 多少时间时，再过渡 */
            transition-delay: 1s;

        }

        .box1:hover .box2{
            width: 100px;
            height: 100px;
            background-color: aqua;
        }

```





## 动画 animation







- 属性 `animation`
- 







## 变形 transform



### 平移



- `x`, `y`, `z` 平移

- 平移不会影响布局，也就是不影响其它元素的位置

  

```css
transform: translateX(100px);
    /* 还可以设置 百分比，相对于本身的百分比 */
transform: translateX(50%);

transform: translateY(100px);

transform: translateZ(100px);
```





```css
.box2:hover{
    transform: translateX(100px);
    /* 还可以设置 百分比，相对于本身的百分比 */
    transform: translateX(50%);
}
```









### 旋转



- 旋转的时候如果需要近大远小，请开启视距`persipective`



```css
transform: rotateZ(1turn); /* z 轴旋转*/
transform: rotateY(45deg); /* z 轴旋转*/
transform: rotateX(45deg); /* z 轴旋转*/

/* 也可以多次不同旋转 */
transform: rotateZ(45deg) rotateX(45deg);
```





```css
.box1{
    width: 100px;
    height: 100px;
    background-color: #bfa;
    border-top: 1px red solid;

    margin: 200px auto;
    transition: all 5s;
}

.box1:hover{
    /* 值可以是度数 */
    /* transform: rotateZ(45deg); */
    /* 也可以是圈数 */
    transform: rotateZ(1turn);
}
```



### 缩放



- 缩放的属性
  - `scaleX`
  - `scaleY`
  - `scaleZ`， 这个不常用，当时没学



```css
 .box1:hover{
     /* 值只能是数字，表示倍数 */
     /* 宽度变成两倍 */
     transform: scaleX(2); 

     transform: scaleY(.5); 

     /* 宽度、高度都放大两倍 */
     transform: scale(2);
}
```

