# vscode 插件



- 安装`easy-less`





# 常用语法



## 选择器写法



```less
.box1{
    .box2{
        background-color: aliceblue;

        .box2{
            background-color: aqua;
        }
    }

    >box3{
        background-color: antiquewhite;
    }
}
```



编译后的 css

```css
.box1 .box2 {
  background-color: aliceblue;
}
.box1 .box2 .box2 {
  background-color: aqua;
}
.box1 > box3 {
  background-color: antiquewhite;
}

```





## 变量





```less
// @ 定义变量
@a: #bfa; // 属性值 css 中属性值
@b: header-wrapper;// 属性值是字符串

div{
    background-color: @a;
}

div .@{b}{ // @{} 引用字符串式变量
    background-color: @a;
    background-image: url("@{b}/1.png"); // @{} 引用字符串式变量
}

div{
    @c: 100px; // 内部定义变量
    line-height: @c;
}



// $引用属性
div{
    width: 100px;
    height: $width;
}
```



## 父元素



```less
// 父元素

.button{

    // $ 应用父 .button
    &-ok{
        background-color: antiquewhite;
    }

    &-error{
        background-color: red;
    }
}
```



编译后的 css

```css
.button-ok {
  background-color: antiquewhite;
}
.button-error {
  background-color: red;
}

```





## 扩展



`:extend()` 相当于并集选择器



```less

.p1{
    width: 100px;
    height: 100px;
}

.p2:extend(.p1){
    background-attachment: fixed;
}
```



**编译成 css**

```css
.p1,
.p2 {
  width: 100px;
  height: 100px;
}
.p2 {
  background-attachment: fixed;
}

```



## mixin 函数





```less

// 没有参数的混合函数
.mix1(){
    height: 100px;
}

.mix0(){
    width: 100px;
}


// 有默认参数的混合函数
.mix2(@a:100px, @b:#bfa){
    line-height: @a;
    color: @b;
}

.box1{
    .mix0; // 直接引用
    .mix1(); // 函数形式引用
    .mix2(10px); // 一个参数使用指定值，其它参数使用默认值
}


```



**编译成 css**

```css
.box1 {
  width: 100px;
  height: 100px;
  line-height: 10px;
  color: #bfa;
}
```





## 模块化



- 不同的文件定义不同的内容
  - `variables.less` 定义变量
  - `mixin.less` 定义混合函数
- 使用其它 `less` 文件
  - `import ./less/mixin.less` 即可（相当于 将 `mixin.less` 中的内容直接拷贝到当前 `less` 文件）







## 调试



在浏览器中可以直接找到样式在 `less` 文件中位置



1、 配置 `easy-less`



2、vscode -》左下角设置 -》扩展 -》找到 `easy less` 