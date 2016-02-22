# 面向切面的概念解读
<p>Aspect Oriented Programming(AOP)，面向切面编程，是一个比较热门的话题。AOP主要实现的目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获得逻辑过程中各部分之间低耦合性的隔离效果。
</p>
<p>用刀切面包或用锯子锯木头，暴露在我们面前的横断面即为切面。面向切面编程有自身的很多优点，可以对已有的代码，进行无切入式的干扰。AOP的主要目的，是对业务过程中的切面进行提取。它所面对的，是处理过程中业务的某个步骤或阶段。即往已经存在的业务逻辑中穿插了一些代码。我们有干扰代码时，可以对业务逻辑进行干预，无干扰代码时，不会影响到原来任何的业务逻辑。</p>

# 相关技术要点回顾

<p>JavaScript的prototype </p>
<p>JavaScript的prototype返回对象类型原型的引用，使我们有能力向对象添加属性和方法。</p>
```
<script>
<script type="text/javascript">
function employee(name,job,born)
{
this.name=name;
this.job=job;
this.born=born;
}

var bill=new employee("Bill Gates","Engineer",1985);
employee.prototype.salary=null;
bill.salary=20000;

document.write(bill.salary);
</script>
```
<p>JavaScript的apply</p>
<p>apply方法能劫持另外一个对象的方法，继承另外一个对象的属性</p>
<p>Function.apply(obj,args)方法接收两个参数</p>
<p>obj：这个对象将代替Function类里this对象</p>
<p>args：这个是数组，它将作为参数传给Function（args-->arguments）</p>

<p>apply 示例代码如下：</p>

```
<script type="text/javascript">
function Person(name,age){   //定义一个类，人类  
    this.name=name;     //名字
    this.age=age;       //年龄
    this.sayhello=function(){alert("hello")};
}
function Print(){            //显示类的属性
    this.funcName="Print";
    this.show=function(){
        var msg=[];
        for(var key in this){
            if(typeof(this[key])!="function"){
                msg.push([key,":",this[key]].join(""));
            }
        }
        alert(msg.join(" "));
    };
} 
function Student(name,age,grade,school){    //学生类 
    Person.apply(this,arguments);
    Print.apply(this,arguments);
    this.grade=grade;                //年级 
    this.school=school;                 //学校 
} 
var p1=new Person("jake",10);
p1.sayhello();
var s1=new Student("tom",13,6,"清华小学");
s1.show();
s1.sayhello();
alert(s1.funcName);
</script>
```

# JS 面向切面代码实战
<p>1、创建HTML文件</p>
<p>2、创建js文件aop.js</p>
<p>3、在HTML文件中引入aop.js</p>
<p>4、在aop.js中编写代码</p>

```
function test()
{
　　alert(2);
}

window.onload = function() { test(); }
```
<p>实现的目的现在需要统计一下当前页面所有的函数运行谁耗时最长，然后做相应的性能优化</p>

## 通用的做法：
```
function test()
{
　　var start = new Date();

　　alert(2);

　　var end= new Date();
　　//console.log(end - start);
}

```
<p>插入污染变量，埋点，输出或返回！</p>


