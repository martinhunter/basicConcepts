## new
```
function User(){
  // when using new to construct a new object,
  // it automatically runs the two commented codes below
  // this = new Obj();??  //this is an instance of Obj(meta object),set properties of this below
  this.name = 'Mar';
  this.op = function(arg){
    this.create = arg;
    };
  // return this; // now nuser points to this
  };
var nuser = new User();
// var cc = new nuser() return Error,nuser is not a constructor
```
```
Example when it runs
>> a
Object { name: "Mar", op: op() }
>> a.op('bil')
undefined (cause it returns no value)
>> a
Object { name: "Mar", op: op(), create: "bil" }
>> a.create
"bil"
```

## constructor

## scope/context
* scope/context is distinguished by function rather than block

> Every bit of your JavaScript program is executed in one **execution context** or another.

> evaluating a function establishes a distinct execution context that appends 
its local scope to the scope chain it was defined within

* #### context is all the codes in a function
* #### scope is what the variables in the function refer to



## this

### 1. what is *this*

#### this serves as an identity function, providing our neighborhoods/contexts a way of referring to themselves
#### *this* always points to the context object where the current code is in.

### 2.1 when *this* points to an object
> this points to window(global object) by default

* **Calling an Object’s Method**

``` javascript
var deep_thought = {
  the_answer: 42,
  ask_question: function () {
    return this.the_answer;
  }
};
var the_meaning = deep_thought.ask_question();
```

* **Constructor** 

```
function BigComputer(answer) {
  this.the_answer = answer;
  this.ask_question = function () {
    return this.the_answer;
  }
}
var this_thought = new BigComputer(42);
var thisobj = this_thought.ask_question();
```

### 2.2 when *this* points to window

```
function myFun() {
    return this; // What is `this` here?
}
var obj = {
    myMethod: function () {
        eval("myFun()");
    }
};
```

* **Function Call**,not method of an object

```
function test_this() {
  return this;
  }
var i_wonder_what_this_is = test_this();
```

* **Event Handler**

    2.2.1 alerts the window object

```
<script type="text/javascript">
function click_handler() {
  alert(this); // alerts the window object
}
</script>
...
<button id='thebutton' onclick='click_handler()'>Click me!</button> 
```

    2.2.2 alerts the button DOM node
```
<script type="text/javascript">
function click_handler() {
alert(this); // alerts the button DOM node
}

function addhandler() {
document.getElementById(‘thebutton’).onclick = click_handler;
}

window.onload = addhandler;
</script>
…
<button id=’thebutton’>Click me!</button>
```

### 3. solution for the misleading "this"
step1. apply(),call(),but they'll execute immediately,not suitble for handler
step2. craete bind property to prorotype of function

```

Function.prototype.bind = function(obj) {
  var method = this,
  temp = function() {
    return method.apply(obj, arguments);
  };
  return temp;
}

function multiply(mult) {
  return this.num * mult;
}
btn.click = multiply.bind(handlerobject)(paramsforhandlerobject)；
```

[undestanding scope](http://www.digital-web.com/articles/scope_in_javascript/)

## prototype
