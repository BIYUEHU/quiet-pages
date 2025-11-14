## 0.前言

### 声明
本教程由 `ArimuraSena` 原创，遵循 **BCU** 知识版权协议，严禁任何人转载、二改、偷窃。
## 要求
能在正确地点发现本篇文章即代表你应有一定的网上冲浪和自我学习能力，因此对待本文章无需有太多心智负担。原则上，本文在于讲解 TypeScript 内容，不强求有 JavaScript 基础，但最好有任何一门编程语言基础，若有 JavaScript 基础当然最好。
### 介绍
**TypeScript**（以下简称「TS」）是由 Microsoft 开发的一款转译到 JavaScript（以下简称「JS」） 的静态类型语言，同时是 JS 的超集，旨在为 JS 提供类型系统以便于编写更安全、健壮、开发友好、易维护的代码和项目。
TS 有着目前所有工业语言（主流编程语言）中最强大的类型系统和类型表达能力，没有之一。即便在学术界，单论类型逻辑的表达能力能超过 TS 的姑且仅有 Idris、Agda、Coq、Aya、Lean 等支持 **依赖类型（Dependent Type）** 且常用于定理验证的语言。追其缘由，JS 是极其灵活的动态语言，而 TS 宗旨恰是「作为 JS 超集」，因此在类型层面上要有一定的逻辑能力对应值层面是绝对必要的，但成也如此，败也如此。类型层面上强大的逻辑能力无疑增加了复杂度，进而被戏称为「类型体操」，该词最初来源于 Haskell 社区，意旨较为复杂的类型定义，加上 TS 作为转译语言，类型信息一般会在编译到 JS 后全部清除，以及所谓的「拖慢开发进度」，这些成为了很多面向薪资编程者不学 TS 甚至贬低 TS 的理由。类型体操的复杂性自然是存在的，但绝大部分时候单纯写项目而非框架、库时，是几乎不需要写类型体操相关代码，何况还有本教程。又尤其是要快速成型的小项目，用 JS 或许能快一星半点，但用 TS 时用到的类型定义也都是十分基础简单的，甚至只需要写基本的类型注解，如果它真是个小项目的话，拖慢进度之言不攻自破，更不用说这忽略了 TS 带来的太多优点。
### 类型为何
尽管 TS 会在编译成 JS 后清除近乎所有类型信息，但这无伤大雅，因为有类型所带来的好处在此刻已被开发者尽数享受，反而 TS 的出现更加证明了类型擦除机制的极强可行性和可用性（虽然无法称之为高可用）。那么回归主题，类型是什么？
- 36 + 43 = 79
- 4,644,467 + 64,695,947 = 69,340,413
- 4,644,467 + 64,695,947 = "Hello Word!"
对于第一个算式，稍微口算一下便知答案是正确的，那对于第二个了？显然有点难度了，但不妨看看第三个，十分明显，完全不需要计算便知道这绝对是错的，因为数字加数字应当再返回数字而非字符串，这即是类型检查。不需运行程序便能发现如此明显的问题，避免了 JS 中各种在运行时才会显露的低级问题，将类型引起的错误提前到编译阶段发现。通过类型编写出来的代码将更加健壮，额外地，TS 一定程度上促进社区代码风格的相对统一，同时借类型所衍生出的良好开发体验和全面的编辑器代码提示，对于第三方库，不需要一边翻着文档一边写代码，只要库有 TS 类型定义，即可轻易查看库的导出信息，配上函数、方法等的类型注释（TSDoc）和名字信息，也能轻易明白其作用。极大缩减了代码上手难度和增加了可维护性。
> 当然，作为超集的 TS，为适应其 JS 的动态性，导致个别时候类型并非真正安全，而其它一些作为子集存在的转译到 JS 语言如 ReScript（Ocaml 方言）、ClojureScript（Lisp 方言）、PureScript（Haskell 方言）在这方面更加优秀
## 1.类型注解
不同于糟糕的 C、C++、Java、C# 语言，TS 采用类型后置风格，这既十分优雅、统一、简洁，也是现代编程语言风格设计的趋势之一，源于类型理论中的习惯。
```typescript
const foo: number = 1
const isDev: boolean = false
let bar: string = "Hi"
bar = "Hey"
bar = 233 // Illegally
```
不过很多时候对于常量、变量的类型注解是非必要的，TS 能够根据赋值正确地自动推导出它们，可以省略。函数的类型注解：
```typescript
function add(x: number, y: number): number {
   return x + y
}

const abs = (x: number): number => {
   if (x > 0) {
      return x 
   }
   return -x
}

// Or:

const abs: (x: number) => number = x => { /* ... */ }
```

一般地，函数返回值也能够正确地自动推导出来，主要是需要注解参数类型（再某些更为强大的 FP 语言中， 参数类型往往也是能够被推导出来的）。对于箭头函数则有两种写法，法一较为简洁，法二将注解全放一边符合 Haskell 类语言传统。
对于自动推导，上面特别强调了 `正确地`，但任何类型系统都并非万能，当推导有误时自然应该进行类型注解，如：
```typescript
const strList = []
strList.push("233") // Illegally
```
我们希望这是一个字符串数组，但显然 TS 并不知道，它被自动推断成了 `never[]`，所以此时添加注解 `: string[]` 则是必要的。
## 2.类型别名
使用 `type` 关键字声明：
```typescript
type MyStr = string
type MyNumber = number
```
仅仅是别名作用，在 Haskell 中类型别名常有语义化表示函数参数的习惯：
```haskell
type MaxValue = Int
type MinValue = Int

getRandom :: MaxValue -> MinValue -> IO Int
```
不过这主要因为 Haskell 语言的函数类型定义不需要写形式参数名，在 TS 这会显得无关紧要。
### 原语类型
注意，这个概念并非由 TS 直接给出，而是从其它编程语言中引入的，意为最小化的原子类型。在 TS 中常见的原语类型有 `string`、`number`、`boolean`、`object`、`undefined`、`null`、`symbol` 等。数组以及其它所有的 JS 类及实例如 `Map`、`Set` 均不属于原语类型。
> 原语类型开头首字母是小写的，诸如 `String`、`Number`、`Boolean`、`Object` 等也是合法的 TS 类型，但与原语类型完全是两回事，它们代表 JS 中的相应类型值的构造函数
### 数组类型
```typescript
type NumList = number[]
// Or:
type NumList2 = Array<number>

const list: NumList = [1, 2, 3]
```
`kind[]` 是 `Array<kind>` 的语法糖，两者等价，后者是从 `Array` 类型算子（也可看作是 JS 中的 `Array` 构造函数）角度进行构造新类型，建议前者。
### 元祖类型
```typescript
type Vector2 = [number, number]
type Vector3 = [...Vector2, number]
```
`...kind` 语法类似于 JS 中的展开运算符，在类型中仅能用于元祖类型。
### 记录类型
或称*字典类型*，类似与对象类型，表示一串键值对。
```typescript
type Mapping = {
   [key: string]: number
}
```
TS 仅允许 `string`、`number`、`symbol` 作为键类型。
### 对象类型
```typescript
type Response = {
  code: number,
  data: any
  error: {
    message: string
  }
}
```
### 联合类型
或称*或类型*，声明语法很像函数式编程里的代数数据类型，但实际截然不同。
```typescript
type MyType = string | number
type MyType2 = MyType | boolean

let bar: MyType = "hi"
bar = 2333
bar = true // Illegally

bar.toString()
bar.toFixed() // Illegally
bar.charAt(0) // Illegally

if (typeof bar === "number") {
   bar.toFixed()
}
```
联合类型的值仅拥有其下所有类型的共有属性与方法（即其下所有类型的交集），若要调用某一特定类型的方法应当线判断其实际使用通过 `typeof` 操作符，同时这会反应到 TS 的类型推导中去，类似的实际值判断操作将在后续屡见不鲜。
### 交叉类型
或称*并类型*，应当用于多个对象类型间。
```typescript
type A = {
   a: string
}
type B = {
   b: string
}
type C = A & B // { a: string, b: string}
type D = string & number // never
```
交叉类型的值拥有其下所有类型的所有属性与方法（即其下所有类型的交集），前提是彼此不冲突。原语类型间不存在交集故为 `never`。
### 只读类型
仅用于元祖类型、记录类型、对象类型。
```typescript
interface A {
   readonly a: string
   b: string
}

type B = [readonly string, number]

const a: A = {
   a: "a",
   b: "b"
}

a.b = "c"
a.a = "b" // Illegally
```

有时想表达元祖类型或对象类型作为常量（即只读），但自动推导并不知道，因此需要添加类型注释：
```typescript
const A = ["hi"] // A: string[]
const B: ["hi"] = ["hi"]
```

但有点麻烦，于是有了语法糖 `const` 类型：
```typescript
const C = ["hi"] as const // C: [readonly "hi"]
const D = <const>["hi"]
```

上面用到了类型断言，有两种语法，效果等价。一般地更推荐用前者即通过 `as` 关键字，后者容易时新人与后面提到的泛型参数搞混，也正是因为 TS 设计之初有后者这种糟糕的断言语法导致原本的 `.ts` 文件不支持 `JSX/TSX` 语法，而需要使用专门的 `.tsx` 文件。
### 可选类型
用于元祖类型、记录类型、对象类型和变量。
```typescript
interface A {
   a: string,
   b?: string
}

const a: A = { a: "hi" }

let b: string // Illegally
let c?: string // c: undefined | string
c = "hi"
c = undefined
```
虽然可选类型标识会将原类型推导成 `undefined | kind`，但实际上元祖类型、记录类型中，使用可选类型与直接声明为 `undefined | kind` 类型在创建相应值上是有细微差别的。
### 函数与类类型
函数类型的定义与函数的定义相似。
```typescript
type MathCalc = (x: number, y: number) => number
```
类的类型类似于函数类型的定义，类的类型是构造器/构造函数的类型，不是类实例的类型，类实例的类型实质是记录类型。
```typescript
type Constructor = new (x: number) => { x: number }

// Or

class Obj {
   x: number
   constructor(x: number) {
		this.x = x
   }
}
type Constructor2 = new (x: number) => Obj
// Or
type Constructor3 = typeof Obj // Constructor3 = new (x: number) => Obj
```
使用 `typeof` 可直接获取对应值的类型。一个类或构造函数在 TS 中既是值也是类型，类直接作为类型时表示类实例的类型，类作为时的类型表示类的构造器/构造函数。
### 额外类型
除去 JS 原有的类型外，TS 提供四种额外类型：`any`、`unknow`、`never`、`void`。
`any` 类似于集合中的全集（Universe Set），包括了所有的类型，当被注解为 `any` 时该值可以像 JS 中一样任意操作。
> 请区别于部分函数式编程中的类型的类型 `kind`

```typescript
let bar: any
bar = 123
bar = "abc."
bar()
bar.x.y
```
`any` 是调和程序健全性与完备性的产物，类型系统绝非万能，有时会产生一些运行时正常但在编译时类型检查器判定为非法的情况从而拒绝编译，`any` 则是一个类型逃生通道。固然很好在某些时候，但万不可滥用，否则会再次造成如上所示的一些低级类型错误。应当分清楚何时必要进行类型体操何时运用 `any`，这是一门大学问，后续将特别讲解。

## 4.接口与类
## 5.命名空间

## 6.参数化类型（泛型）
## 7.类型与值

## 8.类型算子

### 条件判断
### 模式匹配
### 类型映射
## 9.协变、逆变

> 请注意，`object`、`number`、`string`、`boolean`