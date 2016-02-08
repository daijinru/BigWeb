# JavasScript设计模式
##  一、模块方法模式
###  定义：
模块方法模式是一种需要使用继承就可以实现的模式。
由2部分组成，第一部分是抽象父类，第二部分是具体的实现子类。
通常在抽象父类中封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有的方法的执行顺序。子类通过继承这个抽象的类，也继承了整个算法结构，并且可以选择重写父类的方法。

###  例子：Coffer or Tea
####  1.泡一杯咖啡
首先我们泡一杯咖啡，基本步骤是：
* 把开水煮沸
* 用沸水冲泡咖啡
* 把咖啡倒入杯子
* 加糖和牛奶
```
var Coffee = function(){};

Coffee.prototype.boilWater = function(){
    console.log('把水煮沸');
}
Coffee.prototype.brewCoffee = function(){
    console.log('冲泡咖啡');
}
Coffee.prototype.pourInCup = function(){
    console.log('把咖啡倒入杯子');
}
Coffee.prototype.addSomething = function(){
    console.log('加糖和牛奶');
}

Coffee.prototype.init = function(){
    this.boilWater();
    this.brewCoffee();
    this.pourInCup();
    this.addSomething();
}

var coffee = new Coffee();
coffee.init();
```
####  2.泡一杯茶
和泡咖啡的步骤相差不大
* 把水煮沸
* 用沸水冲泡茶叶
* 把茶倒入杯子
* 加柠檬
```
var Tea = function(){};

Tea.prototype.boilWater = function(){
    console.log('把水煮沸');
}
Tea.prototype.brewTea = function(){
    console.log('冲泡茶叶');
}
Tea.prototype.pourInCup = function(){
    console.log('把茶倒入杯子');
}
Tea.prototype.addSomething = function(){
    console.log('加柠檬');
}

Tea.prototype.init = function(){
    this.boilWater();
    this.brewTea();
    this.pourInCup();
    this.addSomething();
}

var tea = new Tea();
tea.init();                                                      
```
####  3.分离出共同点
不管是泡咖啡还是泡茶都要经历以下几个步骤：
* 把水煮沸
* 用沸水冲泡原料
* 把成品倒入杯子
* 加配料
<p>所以，不管是泡什么原料，我们都可以给它一个新的方法名称，例如：brew( ) 。<br>
同理，不管是加什么配料，我们也可以给他一个新的方法名称，例如：add( ) 。<br>
因此，不管是泡什么东西，我们就可以给他一个新的父类名称，例如：Beverage( )。</p>
```
var Drunk = function(){};

Drunk.prototype.boil = function(){
    console.log('把水煮沸');
}
Drunk.prototype.brew = function(){
    throw new Error('子类必须重写brew方法');
}
Drunk.prototype.pour = function(){
    throw new Error('子类必须重写pour方法');
}
Drunk.prototype.add = function(){
    throw new Error('子类必须重写add方法');
}

Drunk.prototype.init = function(){
    this.boil();
    this.brew();
    this.pour();
    this.add();
}                                                    
```
####  4.创建coffee和tea子类
现在创建一个Drunk类的对象对我们来说没有意义，因为饮品有非常多种，在这里Drunk只是一个抽血的存在。接下来我们要创建咖啡喝茶类，让他们继承饮品Drunk类。
```
var Coffee = function(){};
Coffee.prototype = new Drunk();
//接下来需要重写抽象父类的一些方法，只有把水煮沸boil这个行为可以直接使用父类中boil的方法，其他方法都需要在Coffee子类中重写。
Coffee.prototype.brew = function(){
    console.log('用沸水冲泡咖啡');
}
Coffee.prototype.pour = function(){
    console.log('把咖啡倒入杯子');
}
Coffee.prototype.add = function(){
    console.log('加糖和牛奶');
}

var coffee = new Coffee();
coffee.init();
//我们的coffee已经完成，当调用coffee对象的init方法时，由于coffee对象和Coffee构造函数的原型prototype上都没有对应的init方法，所以该请求会顺着原型链，被委托给Coffee的父类Drunk原型上的init方法。
```
接下来照葫芦画瓢，来创建一下tea类
```
var Tea= function(){};
Tea.prototype = new Drunk();

Tea.prototype.brew = function(){
    console.log('用沸水冲泡茶叶');
}
Tea.prototype.pour = function(){
    console.log('把茶倒入杯子');
}
Tea.prototype.add = function(){
    console.log('加柠檬');
}

var tea = new Tea();
tea.init();
```
#### 5.总结
在上面栗子中，Drunk.prototype.init，就是所谓的模版方法。
因为方法中封装了子类的算法框架，它作为一个算法的模版，指导子类以何种顺序去执行哪些方法。在Drunk.prototype.init方法中，算法内的每一个步骤都是清楚展现在我们眼前的。


##二、职责链模式

### 定义：
使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理为止。
链中受到请求的对象要么亲自处理它，要么转发给下一个候选者。提交方并不明确有多少个对象会处理它，任一候选者都可以响应相对应的请求，可以再运行时刻决定哪些候选者参与到链中。

### 例子：
一个售卖手机的电商网站，之前有用户分别交纳500元订金和200元定金预订，现在已经可以正式购买了。
支付过定金的用户肯定有优先购买权还有优惠券补贴，支付500定金的发放100元优惠券，支付200定金的发放50优惠券。没有支付过定金的用户按正常购买。
假设订单页面是PHP吐出的模版，在页面加载之处，PHP会传递给页面几个字段：
* orderType：表示订单类型。code值为1的时候表示支付500元定金的用户，2表示支付200元定金，3表示普通用户。
* pay：表示用户是否已支付定金，code值为true的时候表示已经支付，false的时候虽然下了订单，但没有支付，降级为普通用户。
* stock：表示当前产品的存库数量，已经支付过定金的用户将不受限制。

#### 1.一般般的写法
```
var order = function(orderType,pay,stock){
    if(orderType == 1 ){
        if( pay === true ){
            console.log('500元定金预购，得到100元优惠券');
        }else{
            if( stock > 0 ){
                console.log('普通用户购买，无优惠券');
            }else{
                console.log('手机库存不足');
            }
        }
    }
    else if(orderType == 2 ){
        if( pay === true ){
            console.log('200元定金预购，得到50元优惠券');
        }else{
            if( stock > 0 ){
                console.log('普通用户购买，无优惠券');
            }else{
                console.log('手机库存不足');
            }
        }
    }
    else if(orderType == 3 ){
        if( stock > 0 ){
            console.log('普通用户购买，无优惠券');
        }else{
            console.log('手机库存不足');
        }
    }
}

order(1,true,500);
order(1,false,500);
order(2,true,500);
order(3,true,500);
order(3,true,0);
```
以上代码中order函数非常巨大，而且需要经常进行修改。虽然能正常运行，但不利于后期维护。
#### 2.用职责链模式重构代码
先把500元订单、200元订单以及普通购买分成3个函数。让500元订单函数处理，如果该函数不符合处理条件，则把请求传递到200元订单函数。如果200元订单函数不能处理，则把请求传递给普通购买函数处理。
```
var order500 = function(orderType,pay,stock){
    if(orderType===1 && pay==true){
        console.log('500元定金预购，得100元优惠券');
    }else{
        order200(orderType,pay,stock);
    }
}

var order200 = function(orderType,pay,stock){
    if(orderType===2 && pay==true){
        console.log('200元定金预购，得50元优惠券');
    }else{
        orderNormal(orderType,pay,stock);
    }
}

var orderNormal = function(orderType,pay,stock){
    if(stock>0){
        console.log('普通用户购买，没有优惠');
    }else{
        console.log('手机存库不足');
    }
}

order500(1,true,500);
order500(1,false,500);
order500(2,true,500);
order500(3,true,500);
order500(3,true,0);

```
## 三、建造者模式

### 定义：
将一个复杂的对象构建与他的表象分离，使得同样的构建过程可以创建不同的表示。
建造者模式通常包括下面几个角色：
* Builder：给出一个抽象借口，以规范产品对象的各个组成成分的构建。这个借口规定要实现复杂对象的哪些部分的建造。这个结构规定要实现复杂对象的哪些部分的创建，并不设计具体对象部件的建造。
* ConcreteBuilder：实现builder接口，针对不同的商业逻辑，具体化复杂对象的各部分的创建。在创建过程中完成后，提供产品实例。
* Director：调用具体建造者来创建复杂对象的各个部分，不涉及具体产品的信息，只负责保证对象各部分完整创建或按某种顺序来创建。
* Product：要创建的复杂对象。

###例子：
例如一个客户要做一个好大上的网页，网页就是要创建的对象。然后老板说没问题抱在我身上，老板就是director。老板就啪一声扔给了程序员，程序员就是builder。程序员做的网站中，要弄html框架，要弄css样式，要弄js行为，最后实现用户提出的高大上需求，完成了打成一个包，好让老板提着走，这个包就是concretebuilder。
#### 1.客户说要一个高大上的网页
提出要创建复杂对象的一个需求
```
var Web = function(){
    this.html ='高大上',
    this.css='高大上',
    this.js='高大上';
}
```
#### 2.老板说没问题。翻滚吧！程序员！
程序员首先搭建html，搭建css，搭建js，然后把html，css，js写得高大上。
```
var Builder = function(){
    this.makehtml = function(){
        console.log('构建html文件');
    }
    this.makecss = function(){
        console.log('构建css文件');
    }
    this.makejs = function(){
        console.log('构建js文件');
    }
    this.getResult = function(){
        var web = new Web();
        web.html = '这个是高大上的html';
        web.css = '这个是高大上的css';
        web.js = '这个是高大上的js';
        return web;
    }
}
```
#### 3.老板就指指点点说，先弄这个，再弄这个
```
var Director = function(){
    this.getWeb = function(builder){
        builder.makehtml();
        builder.makecss();
        builder.makejs();
    }
}
```
####4.客户呼唤老板交稿
```
var builder = new Builder();
var director = new Director();
director.getWeb(builder);
var hightWeb = builder.getResult();
console.log(hightWeb); 
```

## 四、策略模式

### 定义：
定义了一些列的算法，把它门一个个封装起来，并使他们可以相互互换，算法的变化不会影响使用算法的客户。
###例子：
用策略模式创建一个表单验证的功能。
以下是表单结构：
```
<form  id="registerForm" action="http://xxx.com/register" style="padding:50px;">
    <label for="userName" >userName</label>
    <div>
      <input type="text" id="userName" placeholder="userName" name="userName">
    </div>

    <label for="password" name="password" >Password</label>
    <div>
      <input type="password" id="password" name="password" placeholder="Password">
    </div>

    <label for="phoneNumber" name="phoneNumber">phoneNumber</label>
    <div>
     <input type="text" id="phoneNumber" name="phoneNumber" placeholder="phoneNumber">
    </div>

    <div>
      <button type="submit">提交</button>
    </div>
</form>
```
#### 1.封装策略类
我们创建一个策略对象strategies，并把算法类型都在里面分别封装起来。
```
var strategies = {
    isNonEmpty:function(value,errorMsg){                  //不为空
        if (value==='') { 
            return errorMsg;
        }
    },
    minLength:function(value,length,errorMsg){            //限制最小长度
        if (value.length<length) {
            return errorMsg;
        }
    },
    isMobile:function(value,errorMsg){                   //手机号码格式
        console.log(this);
        if (!/(^1[3|5|8][0-9]{9}$)/.test(value)) {
            return errorMsg;
        }
    }

};
```
#### 2.封装验证类
* add方法接收到registerForm.userName,[{strategy:'minLength:6'},{errorMsg:'用户名长度不能少于6位'}];
* strategyAry就把'minLength:6'通过冒号区分了策略和参数
* strategy通过shift() 方法用于把数组的第一个元素从其中删除，并返回第一个元素的值。
* 这样stragety就等于'minLength'
* strategyAry就等于'6'
* strategyAry通过unshift() 方法可向数组的开头添加了'dom.value',
* strategyAry就等于｛'value','6'}
* strategyAry通过push()方法添加上了'errorMsg',
* strategyAry就等于了{'value','6','errorMsg'}
```
var Validator = function(){
    this.cache = [];                                     //保存验证规则
};

Validator.prototype.add = function(dom,rules){

    var self = this;

    for(var i = 0, rule; rule = rules[i++];){
        (function(rule){
            var strategyAry = rule.strategy.split(':');  //把strategy和参数分开
            var errorMsg = rule.errorMsg;

            self.cache.push(function(){                 //把校验的步骤用空函数包装起来，并放入cache
                var strategy = strategyAry.shift();     //取出用户挑选的strategy
                strategyAry.unshift(dom.value);         //把input的value 添加进参数列表
                strategyAry.push(errorMsg);             //把errorMsg     添加进参数列表
                return strategies[strategy].apply(dom,strategyAry);
            });
        })(rule)
    }
};

Validator.prototype.start=function(){                   
    for(var i=0, validatorFunc;validatorFunc=this.cache[i++];){
        var errorMsg = validatorFunc();                  //开始校验，并取得校验后的返回信息
        if (errorMsg) {                                  //如果有确切的返回值，说明没有验证通过
            return errorMsg;
        };
    }
}
```
#### 3.客户调用代码
先创建一个validator对象，然后通过validator.add方法，往validator对象添加一些校验规则。validator.add方法接受3个参数。
registerForm.password 为参与验证的输入框。minLength:6 是一个以冒号隔开的字符串。冒号前的minlength代表客户挑选的strategy对象，冒号后面的6表示校验需要的必要参数。第三个参数就是返回的错误信息。
往vlidator对象添加完一系列的校验规则之后，会调用validator。start返回一个确切的errormsg字符串当作返回值，说明这次校验没有通过。
然后呢，让registerform.onsubmit方法返回false来阻止表单的提交。
```
var registerForm = document.getElementById('registerForm');

var validataFunc = function(){
    var validator = new Validator();                   

    validator.add(registerForm.userName,[{     //添加校验规则
        strategy:'isNonEmpty',
        errorMsg:'用户名不能为空'
    },{
        strategy:'minLength:6',
        errorMsg:'用户名长度不能小于6位'
    }]);

    validator.add(registerForm.password,[{
        strategy:'minLength:6',
        errorMsg:'密码长度不得能少于6位'
    }]);

    validator.add(registerForm.phoneNumber,[{
        strategy:'isMobile',
        errorMsg:'手机号码格式不正确'
    }]);

    var errorMsg = validator.start();           //获得校验结果
    return errorMsg;                            //返回校验结果
}

registerForm.onsubmit = function(){             //如果errormsg有确切返回值，说明验证没有通过
    var errorMsg = validataFunc();

    if (errorMsg) {
        alert(errorMsg);
        return false;
    };
};

```
使用策略模式重构代码之后，我们仅仅通过配置的方法就可以完成一个表单的校验，
这些校验规则也可以服用在程序的任何地方，还能作为插件的形式，方便地被移植到其它项目中。
在修改校验规则的时候，只需要编写或改动少量的代码。
#### 4.总结
策略模式是一种可常用且有效的设计模式，从案例中我们可以总结出策略模式的一些优点：
* 策略模式利用组合，委托和多态，有效地避免了多重条件语句。
* 策略模式提供了对开放－封闭原则的支持，将算法封装在独立的strategy中，使得它们易与切换，易于理解，易于扩展。
* 策略模式中的算法也可以复用在系统的其它地方，从而避免许多复制粘贴
* 策略模式中利用组合和委托来让context拥有算法的能力，这也是继承的的一种更轻便的替代方案。


## 五、发布－订阅模式

### 定义：
发布－订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生变化时，所依赖于它的对象都将得到通知。
在javascript开发中，我们一般用事件模型来替代传统的发布－订阅模式。

```
define(function(require, exports, module) {
    var observer = {};                             // 创建一个储存仓库

    var util = {
        // 订阅方法
        subscribe: function (name, func) {
            if (!observer[name]) {        // 在储存仓库内添加一个事件仓库
                observer[name] = [];
            }
            observer[name].push(func);
        },


        // 发布方法
        publish: function (name, arg) {
            if (!observer[name]){                   // 判断是否有本仓库
                return false;
            }
            observer[name].forEach(function (item) {       // 遍历执行
                item(arg);
            });
        }

    }

    module.exports = util;
});

```
## 六、适配器模式

### 定义：
适配器模式(Adapter) 是将一个类（对象）的接口（方法或属性）转化成客户希望的另外一个接口（方法或属性），适配器模式使得原本由于接口不兼容而不能一起工作的那些类（对象）可以一起工作。速成包装器（wrapper）。


