

# 选择器



## 关系选择器

==四种关系选择器：==

- 后代选择器 (空格)
- 子选择器 (>)
- 相邻兄弟选择器 (+)
  - 兄弟：说明是同一个父元素
  - 其次是相邻
- 通用兄弟选择器 (~)



==1、==

![image-20220213130149799](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213130149799.png)



==2、==

![image-20220213130423283](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213130423283.png)



==3、相邻兄弟选择器==

![image-20220213130638116](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213130638116.png)



==4、通用兄弟选择器==

![image-20220213130722616](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213130722616.png)





## 并集、交集选择器



![image-20220213131957979](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213131957979.png)



## 伪类





# 长度单位



## 像素和百分比

==两种写法：==

1. 像素
2. 百分比。（父类元素的百分比）

像素就是一个小点点



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box{
            width: 200px;
            height: 200px;
            background-color: chartreuse;
        }
        
        .box2{
            width: 50%;
            height: 50%;
            background-color: aqua;
        }
    </style>
</head>
<body>
    
</body>
    <div class="box">
        <div class="box2"></div>
    </div>
</html>
```



下图的大小分别是：200x200, 100x100

![image-20220210201805519](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210201805519.png)



## em 和 rem







# 颜色单位

==CSS 中设置颜色：==

1. 直接使用颜色值
2. RGB
   - 0 - 255 值
   - 16 进制
3. RGBA
   - A: 透明度，`0`: 完全透明，`1`: 不透明，`.5`: 半透明





![image-20220210202449093](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210202449093.png)



# 布局



==终于学到布局了==



## 文档流



==1、需要明白几个概念：==

1. 网页由一层一层组成的
2. 用户只能看到最上层
3. 网页最底层叫做：文档流
4. 元素有两种状态：
   1. 在文档流中
   2. 不在文档流中



==2、文档流中元素的特点：==

1. 块元素独占一行
   - 宽度：父元素宽度
   - 高度：内容撑开、子元素撑开
2. 行内元素：
   - 不会独占一行
   - 宽度：由内容决定
   - 高度：由内容决定

![image-20220210203943106](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210203943106.png)



## 盒子模型



1. 每个 html 元素都是一个盒子
2. 盒子包括：
   1. 边框（border）
   2. 内容区（content)
   3. 内边距（padding）
   4. 外边距（margin）（盒子和盒子之间的间距）



==设置内容区大小==：**heiht, width 设置的是内容区大小**

![image-20220210205411460](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210205411460.png)



==**盒子大小变了，但是内容区没有变**==

![image-20220210205540174](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210205540174.png)





### 盒子占用空间



==两方面：看得见的边框，看不见的边框==

1. 看得见的边框就是：border 里面的东西
2. 看不见的边框就是 margin





### 边框 border

边框（border）属性：

1. 宽度
2. 样式
3. 颜色



三种设置方式：

1. `border-width`, `border-style`, `border-color`
2. `border-xxx`, 比如：`border-top`, `border-right`, `border-bottom`, `border-left`
3. 直接使用`border`



==1、`border-width`, `border-style`, `border-color`==

![image-20220210210119695](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210210119695.png)



2、`border`, `border-xxx`

==分别设置四个边框，这个好用==



![image-20220210210304734](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220210210304734.png)

### 内边距 padding

==四个内边距：padding-top, padding-bottom, padding-left, padding-right==

==可以直接使用 padding（上 右 下 左）比如：`padding: 10px 20px 30px 40px`==



两种写法：

1. `padding-xxx`
2. `padding`

![image-20220211095203197](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220211095203197.png)



### 外边距 margin



==1、第一种写法==







## 盒子大小





```html
.box{

}
```







## outline, box-shadow



==outline, box-shadow 不会影响布局==



![image-20220213133632986](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213133632986.png)



## 圆角

可以将盒子设置为圆形

![image-20220213134405578](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213134405578.png)





## 水平布局







## 垂直方向布局









## 垂直方向外边距折叠

- 只有垂直方向的相邻元素的外边距才有折叠问题
  - 分两类：
    1. 相邻兄弟
    2. 相邻父子元素
- 水平方向的的相邻元素的外边距没有折叠问题







## 行内元素的表现



```xml
<-
   1. 常见的行内元素有什么？
   2. 行内元素可以设置 width，height 属性吗?
   3. 怎么让行内元素变为块元素？
   4. 怎么隐藏元素？
```



![image-20220213123642630](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213123642630.png)



## 浏览器的默认样式









# ==布局高阶==



## 浮动

水平布局时浮动的目的



![image-20220213140213571](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213140213571.png)





![image-20220213143002111](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213143002111.png)





### 浮动特点



1. 浮动元素，不会盖住文字。这是浮动的初始目的
2. 浮动之后，元素脱离文档流
   - 块元素脱离文档流后：
     - 不会独占一行
     - 宽度由内容决定
   - 行内元素脱离文档流后：
     - 就是一个块元素



![image-20220213143403038](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213143403038.png)



![image-20220213144157334](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220213144157334.png)







## 定位





# ==练习==

==一定要练习==

## 图片列表