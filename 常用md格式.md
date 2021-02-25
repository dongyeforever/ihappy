title: 常用 markdown 格式
date: 2021-02-23 21:15:33
categories: Web

tags: markdown
---

#### 常用格式

##### 标题

```shell
# 一级标题
## 二级标题
...
###### 六级标题，最大为六级
```



##### 列表

- 无序列表

```shell
- Microsoft
- Google
- Apple
  - iPhone
  - iPad
```

- 有序列表

```shell
1. Microsoft // 序号后加一个空格
2. Google
3. Apple
```



##### 强调和引用

- 强调，`反引号包围的内容`会高亮显示。

```shell
`反引号包围的内容`会高亮显示。
``嵌套的话，可以使用`两个双引号`嵌套在外面``
```

- 引用

使用`>`符号，如果引用有多行，每行前面都加` >` 

```shell
> 引用内容
> 注意事项
> 多行引用
```



##### 粗体、斜体

```shell
**两个星号包围的是粗体**
*一个星号包围的是斜体*
```



##### 删除线、分隔线

```shell
~~删除线~~

分割线用`---`，注意单独一行，上面必须是一个空行
---
```



##### 图片、超链接

```shell
图片格式：![腾讯网](https://mat1.gtimg.com/pingjs/ext2020/qqindex2018/dist/img/qq_logo_2x.png)

超链接格式：[腾讯网](https://www.qq.com/)
```



##### CheckBox

可勾选的复选框。

```shell
- [ ] 内容  // 注意空格
- [x] 勾选内容
```



##### 代码格式

以 \`\`\`语言 开头，以 \`\`\` 结束。

```java
public static void main(String[] args) {
    System.out.println("hello world");
}
```

```xml
<html>
    <head><title>百度一下</title></head>
    <body></body>
</html>
```



#### 预览效果

---

- Microsoft
- Google
- Apple
  - iPhone
  - iPad



1. Microsoft // 序号后加一个空格
2. Google
3. Apple


`反引号包围的内容`会高亮显示。
``嵌套的话，可以使用`两个双引号`嵌套在外面``


> 引用内容
> 注意事项
> 多行引用



**两个星号包围的是粗体**
*一个星号包围的是斜体*

~~删除线~~



图片格式：![腾讯网](https://mat1.gtimg.com/pingjs/ext2020/qqindex2018/dist/img/qq_logo_2x.png)

超链接格式：[腾讯网](https://www.qq.com/)



- [ ] 内容  // 注意空格
- [x] 勾选内容



```java
public static void main(String[] args) {
    System.out.println("hello world");
}
```

