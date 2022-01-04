# typora使用

--常用快捷方式



加粗 ctrl+B 也可以是 **粗**

斜体 CTRL+I *斜体*

替换 ctrl+H

撤销 ctrl+Z

插入链接 [http://www.bai.com](CTRL+K)

插入图片 CTRL+SHIFT+I

![](D:\文档\2018\03\30\2018033023275200-F1C11A22FAEE3B82F21B330E1B786A39.jpg)



插入代码块 CTRL+SHIFT+``;

			public String compareDate(String starttime,String endtime){
			long nd = 1000 * 24 * 60 * 60;
		    long nh = 1000 * 60 * 60;
		    long nm = 1000 * 60;
			DateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
			try{
				Date c1 = df.parse(starttime);
				Date c2 = df.parse(endtime);
				long diff = c2.getTime() - c1.getTime();
			long day = diff/nd;
			
			long hour = diff%nd/nh;
			 
			long min = diff%nd%nh/nm;
			String time = "";
			if(day > 0){
				time = day + "天" + hour + "小时" + min + "分";
			}else if(hour > 0){
				time = hour + "小时" + min + "分";
			}else if(min > 0){
				time = min + "分";
			}
			return time;
		}catch(Exception e){
			Global.getInstance().LogError(e);
			return null;
		}
	}


显示大纲 CTRL+SHIFT+L



# 一级标题

 `# 这是一级标题 `

## 二级标题

 `## 这是二级标题 `

### 三级标题

`###这是三级标题 `

###### 六级标题

`###### 这是六级标题 `

也可以用快捷键crtl+1,2,3,4,5,6



> 一级引用

> > 二级引用

> > > 三级引用



### 列表

1. 有序列表

2. 可以用1. 2. 3.

3. 来表示

4. 下面是无效列表

   

+ +-*都可以用来表示标点，一级是原点
  * 二级是空心圆
    - 3级是方块实心

**都要带空格**

### 表格

用 | | |来新建一张表

|      |      |
| ---- | ---- |
|      |      |

分割线

`***粗分割线`

`---细分割线`

***

---

- 删除线

`~~~~显示删除线，快键键是ALT+SHIFT+5`

~~删除线~~

- 注脚

`注脚[^]`

 这是一个注脚测试[^测试]。 

[^测试]:注脚可以解释上面的标注



