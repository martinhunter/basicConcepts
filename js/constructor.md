https://cloud.google.com/translate/docs/basic/translating-text#translate_translate_text-python

## new

#### 1. what new does

When the code new Foo(...) is executed, the following things happen:

    1. A new object is created, inheriting from Foo.prototype.
    2. The constructor function Foo is called with the specified arguments, and with this bound to the newly created object. 
    new Foo is equivalent to new Foo(), i.e. if no argument list is specified, Foo is called without arguments.
    3. The object (not null, false, 3.1415 or other primitive types) returned by the constructor function 
    becomes the result of the whole new expression. 
    If the constructor function doesn't explicitly return an object, the object created in step 1 is used instead. 
    (Normally constructors don't return a value, 
    but they can choose to do so if they want to override the normal object creation process.)

``` javascript
function User(){
  // when using new to construct a new object,
  // it automatically runs the two commented codes below
  // this = new Obj();??  //this is an instance of Obj(meta object),set properties of this below
  this.name = 'Mar';
  this.op = function(arg){
    this.create = arg; // only when nobj.op() runs,
                       // this.create is called on nobj and the create property is assigned
    };
  // return this; // now nuser points to this
  };
var nuser = new User();
// var cc = new nuser() return Error,nuser is not a constructor
```

``` javascript
Example when it runs
>> nuser
Object { name: "Mar", op: op() }
>> nuser.op('bil')
undefined (cause it returns no value)
>> nuser
Object { name: "Mar", op: op(), create: "bil" }
>> nuser.create
"bil"
```

#### 2. how to inherit

2.1 prototypal inheritance

KeyCode: SonFunc.prototype = [instanceObject = ]new ParentFunc(parsOfParent);

if property is not found in an object, it will try to find it in object.prototype

``` javascript
>> function User(age){this.age = age;}
>> var u1 = new User(12);
>> u1
User {age: "12"}
>> function client(par){this.hair = par;}
undefined
>> client.prototype = u1;
User {age: "12"}
>> var c1 = new client('black');
c1.age;
"12"
>> c1
>client {hair: "black"}
   hair: "black"
  >__proto__: User
     age: "12"
    >__proto__:
      >constructor: ƒ User()
      >__proto__: Object
```

2.2 class inheritance



#### 3. refrence link

* [new operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)


## constructor
#### 1. concept/purpose

This technique is usually used to initialize public instance variables.

The constructor's this variable is used to add members to the object.

## scope context
### 1. scope is distinguished by function rather than block

### 2. the difference between scope and context

2.1 **context** is the enviroment of an object.
context refers to the object to which a function belongs.

> Every bit of your JavaScript program is executed in one **execution context** or another.

> evaluating a function establishes a distinct execution context that appends 
its local scope to the scope chain it was defined within

> Execution Context includes "your code","this","global object","other enviroment".

2.2 **Scope** is a rule that pertains to the visibility of variables,like a boundery.



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



``` javascript
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



* **Function Call**,not method of an object

``` javascript
function test_this() {
  return this;
  }
var i_wonder_what_this_is = test_this();
```

* **Event Handler**

exp 1. alerts the window object

``` javascript
<script type="text/javascript">
function click_handler() {
  alert(this); // alerts the window object
}
</script>
...
<button id='thebutton' onclick='click_handler()'>Click me!</button> 
```

exp 2. alerts the button DOM node

``` javascript
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

### 3. how *this* is set: EVAL establishes new Execution Context
When evaluating the eval code, this is obj. 
However, in the eval code, myFun is not called on an object, 
so ThisBinding is set to window for the call.

``` javascript
function myFun() {
    return this; // What is `this` here?
}
var obj = {
    myMethod: function () {
        eval("myFun()");
    }
};
```

### 4. solution for the misleading "this"
step1. apply(),call(),but they'll execute immediately,not suitble for handler
step2. craete bind property to prorotype of function

``` javascript
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

### 5. refrence link

* [this keyword](https://stackoverflow.com/questions/3127429/how-does-the-this-keyword-work)

* [Establishing an Execution Context](https://ecma-international.org/ecma-262/5.1/#sec-10.4)

* [undestanding scope](http://www.digital-web.com/articles/scope_in_javascript/)
* [protorype vs this](https://stackoverflow.com/questions/310870/use-of-prototype-vs-this-in-javascript)

## prototype
1. concept/purpose

This technique is usually used to add public methods.When a member is sought and it isn't found in the object itself, then it is taken from the object's constructor's prototype member
The prototype mechanism is used for inheritance.
2. how it works


### 5. refrence link
* [private members](http://crockford.com/javascript/private.html)
