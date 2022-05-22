



# HTML





## 块元素



- div
- 、

### 标题标签



- 标题标签，`h1`,`h2`,`h3` ，一般都是最多 h3
- 仅次于`<title>` 标签（seo 优化）
- 一个页面中只能有一个 `<h1>h1</h1>`





### 布局标签



布局的时候主要用块元素，总结一下我常用布局标签

1. `<header></header>`
2. `<footer></footer>`
3. `<nav></nav>`
4. `<div></div>`



**注意**

1. 有些标签是 `html5` 新增的，可能兼容性并不是很好

   



## 行内元素

- span



### a 







## 行级块元素

- `inline-block`，有以下标签

  ```html
  <button></button>
  <input>
  <textarea></textarea>
  <select>1</select>
  <img>
  ```



- 特点

  - 可以设置 `width`, `height`, `margin`, `padding`

    ```html
    <style>
            button{
                width: 100px;
                height: 100px;
                margin: 10px 100px;
                padding: 100px 20px;
            }
        </style>
    ```



## 语义化元素



语义化元素在布局的时候，只需要看标签语义即可





## 常用元素:star:





### nav & ul & div

- `nav`, `ul`, `div`
  - 重置之后都是一样，没什么区别

- `ul`, `li`
  - 重置之后都是一样，没什么区别



![image-20220419120254527](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220419120254.png)



**ul, li**

<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220419120655.png" alt="image-20220419120654683" style="zoom: 33%;" />

### button



- 行级块元素
- 可以设置`width`,`height`
- 设置边框，背景色
- 可以设置字体（大小，颜色）



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220419113559.png" alt="image-20220419113558899" style="zoom: 33%;" />





### form



- 常用属性
  - `action`
- `input`  和 `button`
  - `button` 常用，原因：双标签，可以设置样式
  - `submit`, 会提交父元素 `form` 的数据
  - `reset`, 会清空父元素`form` 的数据



![image-20220419122733937](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220419122734.png)







# CSS



## 选择器



### 关系选择器





```html
# 后代选择器
# 选择 div 的所有 p 元素
div p {
  background-color: yellow;
}

# 子选择器
# 选择 div 的所有子 p 元素
div > p {
  background-color: yellow;
}

# 相邻兄弟选择器
# 兄弟（同级）元素必须具有相同的父元素，“相邻”的意思是“紧随其后”。
# 下面的例子选择紧随 div 元素之后的所有 p 元素：
div + p {
  background-color: yellow;
}
    
#   通用兄弟选择器
# 选择和 div 同级的所有 p 元素   
div ~ p {
  background-color: yellow;
}    
```



### 交集并集选择器

1. 交集选择器。语法：`选择器1选择器2选择器3...`
2. 并集选择器。语法：`选择器1，选择器2，选择器3 ...`





### 伪类选择器



> 参考：https://www.w3school.com.cn/css/css_pseudo_classes.asp



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        /* 
            将ul里的第一个li设置为红色
         */

/*
            - 选择特殊状态的元素
                比如：第一个子元素、被点击的元素、鼠标移入的元素...
            - 伪类一般情况下都是使用:开头
                :first-child 第一个子元素
                :last-child 最后一个子元素
                :nth-child() 选中第n个子元素
                :only-child
                    特殊值：
                        n 第n个 n的范围0到正无穷
                        2n 或 even 表示选中偶数位的元素
                        2n+1 或 odd 表示选中奇数位的元素

                    - 以上这些伪类都是根据所有的子元素进行排序

                :first-of-type
                :last-of-type
                :nth-of-type()
                :only-of-type
                :empty
                    - 这几个伪类的功能和上述的类似，不通点是他们是在同类型元素中进行排序

            - :not() 否定伪类
                - 将符合条件的元素从选择器中去除
 */
        /* ul > li:first-child{
            color: red;
        } */
    
        /* ul > li:last-child{
            color: red;
        } */

        /* ul > li:nth-child(2n+1){
            color: red;
        } */

        /* ul > li:nth-child(even){
            color: red;
        } */

        /* ul > li:first-of-type{
            color: red;
        } */

        ul > li:not(:nth-of-type(3)){
            color: yellowgreen;
        }
    </style>
</head>
<body>
    
    <ul>
        <span>我是一个span</span>
        <li>第〇个</li>
        <li>第一个</li>
        <li>第二个</li>
        <li>第三个</li>
        <li>第四个</li>
        <li>第五个</li>
    </ul>


</body>
</html>
```



### 超链接伪类



```html
<style>

        /* 
            :link 用来表示没访问过的链接（正常的链接）
         */
        a:link{
            color: red;      
        }

        /* 
    		开发中一般不用
            :visited 用来表示访问过的链接
            由于隐私的原因，所以visited这个伪类只能修改链接的颜色
        */
        a:visited{
            color: orange; 
            /* font-size: 50px;   */
        }

        /* 
            :hover 用来表示鼠标移入的状态
         */
         a:hover{
             color: aqua;
             font-size: 50px;
         }

         /*
            :active 用来表示鼠标点击
         */
         a:active{
             color: yellowgreen;
             
         }
```





### 伪元素选择器



>  *::first-letter 表示第一个字母*
>
>  :first-line 表示第一行*
>
>  *::selection 表示选中的内容*
>
>  *::before 元素的开始* 
>
>  *::after 元素的最后*
>
>  ​          *- before 和 after 必须结合content属性来使用*



- `before`, `after` 可以添加内容
- `before`,`after` 仍然是选择器，选择的元素内容是 `content`,是行内元素
- `before`, `after` 是在某个元素的内容前后





- 为什么叫做伪元素？
  - 因为这些选择器选择的假冒的元素（可以这样理解）





### 选择器优先级



多个选择器都选中同一个元素，他们有优先级之分

- 优先级排序
  - 内联样式
  - id 选择器
  - 类和伪类选择器
  - 元素选择器
  - 通配选择器
  - 继承样式
- 如果优先级相同，则先定义的生效







## 样式



### 继承



- 样式继承发生在祖先和后代之间
- 有些样式可以继承，有些样式不可以继承
  - 可以继承的样式 `font-size`
  - 不可以继承的样式 `background-color`





## 单位



### 长度单位



- `width`, `height` 的单位有

  - `px`
  - `%`
  - `em`
  - `rem`

- 总结

  - ```
    长度单位：
    像素
        - 屏幕（显示器）实际上是由一个一个的小点点构成的
        - 不同屏幕的像素大小是不同的，像素越小的屏幕显示的效果越清晰
        - 所以同样的200px在不同的设备下显示效果不一样
    
    百分比
        - 也可以将属性值设置为相对于其父元素属性的百分比
        - 设置百分比可以使子元素跟随父元素的改变而改变
    
    em
        - em是相对于元素的字体大小来计算的
        - 1em = 1font-size
        - em会根据字体大小的改变而改变
    
    rem
    	- rem是相对于根元素的字体大小来计算
    ```

    



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>

        html{
            font-size: 30px;
        }

        .box1{
            width:300px;
            height: 100px;
            background-color: orange;
        }

        .box2{
            width: 50%;
            height: 50%;
            background-color:aqua; 
        }

        .box3{
            font-size: 50px;
            /* width: 10em;
            height: 10em; */
            width: 10rem;
            height: 10rem;
            background-color: greenyellow;
        }
        .box4{
            font-size: 50px;
            width: 10rem;
            height: 10rem;
            background-color: greenyellow;
        }
    </style>
</head>
<body>

    <div class="box1">

        <div class="box2"></div>

    </div>

    <div class="box3"></div>
    <div class="box4"></div>
    
</body>
</html>
```





### 颜色单位



- 三种写法
  1. 颜色值
  2. RGB、RGBA
     - 透明度，1：不透明，0：完全透明，.5: 半透明
  3. 16进制
     - 6位16进制数字，有时候可以省略为3位



```css
background-color: rgb(255, 0, 0);
background-color: rgb(0, 255, 0);
background-color: rgb(0, 0, 255);
background-color: rgba(0, 0, 255, 1);
background-color: rgba(0, 0, 255, 0);
background-color: rgba(0, 0, 255, .5);
background-color: #fff;
background-color: #fff0aa;
background-color: #000;
```









## 常用属性:star:



### display



- 常用的属性
  - `block`
  - `inline-block`



![image-20220418152258107](https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220418152258.png)







### text-align

- 仅适用于块元素

  - 让子元素（inline， inline-block）以及文本如何排列

- 属性值可以是

  ```css
  /* Keyword values */
  text-align: left;
  text-align: right;
  text-align: center;
  text-align: justify;
  text-align: justify-all;
  text-align: start;
  text-align: end;
  text-align: match-parent;
  
  /* Block alignment values (Non-standard syntax) */
  text-align: -moz-center;
  text-align: -webkit-center;
  
  /* Global values */
  text-align: inherit;
  text-align: initial;
  text-align: unset;
  ```

- `center` 效果

  - 让**子行内元素**、**子行内块元素**、**文本**，**水平居中**
  - 可以被继承



**示例**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style>
        .box1{
            width: 1226px;
            border: 1px red solid;
            text-align: center;
        }

        img{
            border: 1px #bfa solid;
        }

        p{
            width: 100px;
            height: 100px;
            border: 1px #bfa solid;
        }

        h2{
            border: 1px #bfa solid; 
        }


    </style>
</head>
<body>
    

    <div class="box1">
        <p>段落</p>
        <h2>h2 标题</h2>
        <img src="./img/logo.svg" alt="">
    </div>
</body>
</html>
```





### line-height



- 作用
  - 单行文字居中（==垂直居中==）



<img src="https://blog-bt.oss-cn-beijing.aliyuncs.com/1/20220428103750.png" alt="image-20220428103749740" style="zoom:33%;" />







## 不是很懂的地方



1. line-height 属性
2. backgroud-img 不能撑开 a 标签
   - a 需要变成块元素，并设置 width，height





