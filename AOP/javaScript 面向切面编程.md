# 面向切面的概念解读
<p>Aspect Oriented Programming(AOP)，面向切面编程，是一个比较热门的话题。AOP主要实现的目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获得逻辑过程中各部分之间低耦合性的隔离效果。
</p>
<p>用刀切面包或用锯子锯木头，暴露在我们面前的横断面即为切面。面向切面编程有自身的很多优点，可以对已有的代码，进行无切入式的干扰。AOP的主要目的，是对业务过程中的切面进行提取。它所面对的，是处理过程中业务的某个步骤或阶段。即往已经存在的业务逻辑中穿插了一些代码。我们有干扰代码时，可以对业务逻辑进行干预，无干扰代码时，不会影响到原来任何的业务逻辑。</p>

# 相关技术要点回顾（1）



```
JavaScript的prototype
JavaScript的prototype返回对象类型原型的引用，使我们有能力向对象添加属性和方法。
<script>
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
```

