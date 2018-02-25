# TypeScript练习题


### void 和 undefined 有什么区别？
void表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void。
TypeScript里，undefined有自己的类型叫做undefined。 
默认情况下undefined是所有类型的子类型。 就是说你可以把undefined赋值给number类型的变量。
当你指定了--strictNullChecks标记，undefined只能赋值给void和它们各自。


### 什么是 never 类型？
never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。
never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。


### 下面代码会不会报错？怎么解决？
```
const obj = {
    a: 1,
    b: 'string',
};
```

```  
obj.c = null;
const obj = {
	a: 1,
	b: 'string',
        c: {},
};
 
obj.c = null;
```


### readonly 和 const 有什么区别？
const赋值后不能再改变。 换句话说，它们拥有与 let相同的作用域规则，但是不能对它们重新赋值。
对于普通类型不能改变的是值，对于对象它们引用的值是不可变的。
 
readonly来指定只读属性，即使是数组，被创建后再也不能被修改，相比于const更加严格。
最简单判断该用readonly还是const的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用const，若做为属性则使用readonly。


### 下面代码中，foo 的类型应该如何声明
```
function foo (a: number) {
    return a + 1;
}
 
foo.bar = 123;
```

```
interface FooInterface {
(a: number) : number;
bar ?: number;
}
let foo : FooInterface = (a: number) => a+1;
foo.bar = 123;
```
 
### 下面代码中，foo 的类型如何声明
```
let foo = {};
  
for (let i = 0; i< 100; i++) {
    foo[String(i)] = i;
}
```

```
interface FooInterface {
[i: string]: number;
}

let foo: FooInterface = {};

for (let i = 0; i < 100; i++) {
foo[String(i)] = i;
}
```


### 实现 MyInterface
```
interface MyInterface {
    ...
}
class Bar implements MyInterface {
    constructor(public name: string) {}
}
class Foo implements MyInterface {
    constructor(public name: string) {}
}
  
function myfn(Klass: MyInterface, name: string) {
    return new Klass(name);
}
  
myfn(Bar);
myfn(Foo);
```

```
interface MyfnConstructor {
new (name: string) : MyInterface;
}
interface MyInterface {
name: string;
}
class Bar implements MyInterface {
constructor(public name: string) {}
}
class Foo implements MyInterface {
constructor(public name: string) {}
}

function myfn(Klass: MyfnConstructor, name: string){
return new Klass(name);
}

myfn(Bar, '1');
myfn(Foo, '2');
```


### 什么是 Abstract Class？
抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。
抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 abstract关键字并且可以包含访问修饰符。
 

### 什么是 class mixin, 如何实现？
class是指将不同的类mixin后使得新生成的类同时具有不同类各自的功能，
eg：
```
// Disposable Mixin
class Disposable {
isDisposed: boolean;
dispose() {
this.isDisposed = true;
}
}
// Activatable Mixin
class Activatable {
isActive: boolean;
activate() {
this.isActive = true;
}
deactivate() {
this.isActive = false;
}
}
class SmartObject implements Disposable, Activatable {
constructor() {
setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
}
interact() {
this.activate();
}
// Disposable
isDisposed: boolean = false;
dispose: () => void;
// Activatable
isActive: boolean = false;
activate: () => void;
deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable]);
let smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);
////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////
function applyMixins(derivedCtor: any, baseCtors: any[]) {
baseCtors.forEach(baseCtor => {
Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
derivedCtor.prototype[name] = baseCtor.prototype[name];
});
});
}
```

### typeof 关键词有什么用？
不必将 typeof x === "number"抽象成一个函数，因为TypeScript可以将它识别为一个类型保护。 也就是说我们可以直接在代码里检查类型了。
keyof 关键词有什么用？
 keyof 是索引类型查询操作符。 对于任何类型 T， keyof T的结果为 T上已知的公共属性名的联合


### 下面代码中，foo 的类型如何声明
```
function fn(value: number): string {
    return String(value);
}
const foo = fn;
function fn(value: number): string {
return String(value);
}
const foo: (value: number) => string = fn;
下面代码会导致 TS 编译失败，如何修改 getValue 的类型声明。
function getValue() {
    return this.value;
}
  
getValue();

function getValue() : any{
return this.value;
}

getValue();
```
 
 
### 下面是 vue-class-component 的部分代码，解释为什么会有多个 Component 函数的声明，作用是什么？TS 官方文档的那一节有对应的解释文档？
```
function Component <U extends Vue>(options: ComponentOptions<U>): <V extends VueClass>(target: V) => V
function Component <V extends VueClass>(target: V): V
function Component <V extends VueClass, U extends Vue>(
 options: ComponentOptions<U> | V
): any {
  if (typeof options === 'function') {
    return componentFactory(options)
  }
  return function (Component: V) {
    return componentFactory(Component, options)
  }
}
```

函数的重载问题，根据不同的参数输入返回不同的结果。
TS说明：https://www.tslang.cn/docs/handbook/functions.html
如何声明 foo 的类型？
const foo = new Map();
foo.set('bar', 1);
const foo: Map<string, number> = new Map();
foo.set('bar', 1);
注：ts编译时，如果文件使用了Map，会提示error TS2304: Cannot find name 'Map'.，因为ts默认按照es5编译，Map属于es6的内容，因此需要指定lib，即tsc tets.ts  --lib es6


### 如何声明 getProperty，以便能检查出第八行将会出现的运行错误。
```
function getProperty(obj, key) {
    return obj[key].toString();
}
 
let x = { a: 1, b: 2, c: 3, d: 4 };
 
getProperty(x, "a");
getProperty(x, "m");
```

```
function getProperty<T, K extends keyof T>(obj: T, key: K): string {
return obj[key].toString();
}
let x = { a: 1, b: 2, c: 3, d: 4 };
getProperty(x, "a");
getProperty(x, "m");
```

### 类型声明里 「&」和「|」有什么作用？
&表示需要同时满足，属于交叉类型，是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性，例如， Person & Serializable & Loggable同时是 Person 和 Serializable 和 Loggable
联合类型表示一个值可以是几种类型之一。 我们用竖线（ |）分隔每个类型，所以 number | string | boolean表示一个值可以是 number， string，或 boolean。如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。
 
 
### 下面代码里「date is Date」有什么作用？
```
function isDate(date: any): date is Date {
  if (!date) return false;
  return Object.prototype.toString.call(date) === '[object Date]';
}
```
「date is Date」是类型谓词，，TypeScript会将date变量缩减为Date类型，在使用isDate检查过类型后，就能在之后的每个分支里清楚地知道date的类型，从而直接使用对应的一些方法。
 
 
 
### tsconfig.json 里 --strictNullChecks 参数的作用是什么？
默认情况下，类型检查器认为 null与 undefined可以赋值给任何类型。 null与 undefined是所有其它类型的一个有效值。 这也意味着，你阻止不了将它们赋值给其它类型，就算是你想要阻止这种情况也不行。
--strictNullChecks标记可以解决此错误：当你声明一个变量时，它不会自动地包含 null或 undefined。


### interface 和 type 声明有什么区别？
通过使用type会给一个类型起个新名字，起别名不会新建一个类型 - 它创建了一个新 名字来引用那个类型。 给原始类型起别名通常没什么用，尽管可以做为文档的一种形式使用。
interface会直接生成一个新的类型。
同时还有以下区别：
其一，接口创建了一个新的名字，可以在其它任何地方使用。 类型别名并不创建新名字—比如，错误信息就不会使用别名。
另一个重要区别是类型别名不能被 extends和 implements（自己也不能 extends和 implements其它类型）。
另一方面，如果你无法通过接口来描述一个类型并且需要使用联合类型或元组类型，这时通常会使用类型别名。


### 如何完善 Calculator 的声明。
```
interface Calculator {
    ...
}
 
let calcu: Calculator;
calcu(2)
  .multiply(5)
  .add(1)
```

```
interface CalculatorInterface {
currentVal: number;
multiply (val: number) : Calculator;
add (val: number): Calculator;
}
class Calculator implements CalculatorInterface {
currentVal: number;
constructor(currentVal: number) {
this.currentVal = currentVal;
}
multiply(val: number) {
this.currentVal *= val;
return this;
}
add(val: number) {
this.currentVal += val;
return this;
}
}
let calcu: Calculator;
calcu = new Calculator(2);
calcu.multiply(5).add(1);
```


### 「import ... from」、「 import ... = require()」 和 「import(path: string)」有什么区别？
 
import ... from属于es6中新增的内容
若要导入一个使用了export =的模块时，必须使用TypeScript提供的特定语法import module = require("module")
import 'xxx'用于引入全局模块

### declare 关键字有什么用？ 
declare 可以创建.d.ts 文件中的变量，declare 只能作用域最外层。
在Node.js里大部分工作是通过加载一个或多个模块实现的。 我们可以使用顶级的 export声明来为每个模块都定义一个.d.ts文件，但最好还是写在一个大的.d.ts文件里。 我们使用与构造一个外部命名空间相似的方法，但是这里使用 module关键字并且把名字用引号括起来，方便之后import。


### module 关键字有什么用？
在写一个外部模块的时候，使用 module关键字并且把名字用引号括起来，方便之后import。 例如：
 ```
declare module "url" {
export interface Url {
protocol?: string;
hostname?: string;
pathname?: string;
}
 
export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}
 
/// <reference path="node.d.ts"/>
import * as URL from "url";
let myUrl = URL.parse("http://www.typescriptlang.org");
```


### 如何处理才能在 TS 中引用 CSS 或者 图片使之不报错？
import "./index.scss";
import imgPath from "./home.png";
使用require代替
eg：
require('./index.scss');
const imgPath = require('./home.png');

或者使用typings-for-css-modules-loader进行相应的配置
 
### 编写 d.ts 来声明下面的 js 文件
class Foo {
}
module.exports = Foo;
module.exports.Bar = 1;
 
declare module "Foo" {
export class Foo {}
}
 
declare module "Bar" {
export const Bar : number = 1;
}
### namespace 和 module 有什么区别
“内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”，这是为了与 ECMAScript 2015里的术语保持一致，(也就是说 module X { 相当于现在推荐的写法 namespace X {)。
namespace主要是解决命名冲突的问题，会在全局生成一个对象，定义在namespace内部的类都要通过这个对象的属性访问。
 TS1.5 以后，推荐全面使用namespace关键字代替module。因为JS里本身就有module的概念，而且已经是ES6标准里的关键字，各种加载框架比如CommonJS，AMD等也都有module的概念，但是TS里之前的module关键字与他们都不太相同。所以换了一个关键字加以区分，避免造成概念上的混淆。实际语法上，使用namespace等价于TS以前使用的module，然后推荐代码中不要再出现module关键字，这个关键字基本上变成了一个编译后和运行时里的概念，留给纯JS中使用。


### 如何实现 module alias?编译成 JS 能否直接运行？
import Bar from '@src/Bar';
TypeScript编译器通过使用tsconfig.json文件里的"paths"来支持这样的声明映射，编译器会根据配置去进行模块导入。


### 哪些声明类型既可以当做 type 也可以当做 value？
undefined、null



注：题目源于https://segmentfault.com/a/1190000010969537
    答案未必一定正确，如有错误之处敬请斧正！