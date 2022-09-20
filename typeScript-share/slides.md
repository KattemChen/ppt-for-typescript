---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS (experimental)
css: unocss
---

# Typescript

ts 类型体操

<p> ts相对于js其中一个最大的区别就是类型是约束。</p>
<p> 类型体操多适用于约束其类型的,多用于自定义类型语法,最后组成我们得到的类型;</p>

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer absolute bottom-8 right-50" hover="bg-white bg-opacity-10">
    <carbon:arrow-right class="inline"/>
  </span>
</div>
<div class="abs-br m-6 flex gap-2">
  <a href="https://www.typescriptlang.org/zh/play" title="Link to PlayGround" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </a>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

## Typescript 区别于 Javascript

这里只列举部分：

<div grid="~ cols-2 gap-2" m="-t-2">

```yaml
数组、元祖； never ; undefined;import type;as const
有值空间与类型空间；泛型；
```

```yaml
数组;import；
只有值空间；
```

</div>
泛型(Generic Type)的意义
```ts
// C++

int add(int a, int b) {
return a + b;
}

double add(double a, double b) {
return a + b;
}

//泛型简化后

T add<T>(T a, T b) {
return a + b;
}

````
---

类型体操的语法,大多都是围绕泛型进行的.所以提一下.
疑问:在ts中,interface(接口)如何约束一个函数?
函数一般只约束参数跟返回值,写法如下:

```ts

interface MyFuncInterface {
(...args): any
}

interface MyFn {
  number: string
}

````

extends 与 implements

<div grid="~ cols-2 gap-2" m="-t-2">

```yaml
implements
实现，一个新的类，从父类或者接口实现所有的属性和方法，
同时可以重写属性和方法，包含一些新的功能;


(关键词: class, rewrite methods and property.)

```

```yaml
extends
继承，一个新的接口或者类，从父类或者接口继承所有的属性和方法，
不可以重写属性，但可以重写方法;

(关键词: type, interface. Only rewrite methods, but
it can't rewrite property.)
```

</div>

---

# 一些常用的类型语法

infer

**infer**一定是跟着 extends,并且放在右边;用来推断一个类型变量，高效的对类型进行模式匹配;
多数情况下是跟泛型或者翻出一起使用,推断出某些传入的泛型内的类型;

```ts
//从泛型中提取(推断)类型
type T0 = string[]
type UnpackedArray<T> = T extends (infer U)[] ? U : T
type U0 = UnpackedArray<T0>

//推断函数的参数类型
type InferParameters<T extends Function> = T extends (...args: infer R) => any
  ? R
  : never
type ParamsRes = InferParameters<(arg1: string, arg2: number) => void> // [string, number]

//推断函数的返回值类型
type InferReturnType<T extends Function> = T extends (...args: any) => infer R
  ? R
  : never
```

---

## in

类比于 for 循环,遍历的是 type;

```ts
type Names = 'xiaoming' | 'zhangsan'

type Obj = {
  [k in Names]: k
}

Obj = {
  xiaoming: 'xiaoming',
  zhangsan: 'zhangsan',
}
```

---

## keyof,typeof

keyof 类比于 Object.keys( {a:1 , b:2 } )=>return ['a','b'],返回的是 'a'|'b',
但是只能访问出 公共属性(public property),私有属性(private property)是无法获取的;

```ts
function getProperty(obj:object, key:string) {
  return obj[key];
}
//Element implicitly has an 'any' type because expression of type 'string' can't be used to index type '{}'.
  No index signature with a parameter of type 'string' was found on type '{}'.
//元素隐式具有 "any" 类型，因为类型为 "string" 的表达式不能用于索引类型 "{}"。
//在类型 "{}" 上找不到具有类型为 "string" 的参数的索引签名.

const user = {
  id: 666,
  name: "CC",
}

const userName = getProperty(user, "name")

//正确的写法是这样的,映射类型

function getProperty<T extends object,K extends keyof T>(obj:T, key:K) {
  return obj[key]
}
const userName = getProperty(user, "name") // string

```

---

## typeof

JS 中的 typeof：在运行时判断类型;TS 中的 typeof：在编译时获取类型;

```ts
interface Person {
  name: string
  age: number
}

let person: Person = { name: 'tao', age: 18 }
//两者一样
type p1 = typeof person
type p2 = Person
```

---

# 内置的类型别名(Built-in type)

```ts
//Partial 将某个类型里的属性全部变为可选项 ?.
type Partial<T> = {
  [P in keyof T]?: T[P]
}

//Required 与 Partial相反(下面是两种实现的写法)
type Required<T> = {
  [P in keyof T]-?: T[P]
}
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}

//Pick（选择）
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}
```

---

```ts
//Record（记录）
/**其实就相当于对象,这个类型主要是用来描述一个对象，Record<string, any>基本是万金油
这个对象的属性都具有相同的类型,但是ts很少用object作为类型指定,因为太泛了;*/

type Record<K extends keyof any, T> = {
  [P in K]: T
}

//Exclude（排除）:得到的是多出来不一样的type,注意,前者的属性必须大于后者; T must extends U
type Exclude<T, U> = T extends U ? never : T

//Extract（取出）与Exclue相反,得到的是交集
type Extract<T, U> = T extends U ? T : never

//Omit（省略）或者说排除,类似于对象的delete操作符,只不过操作的是type
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>

//NonNullable（不能为null）
type NonNullable<T> = T extends null | undefined ? never : T

//Parameters（参数）接收一个函数,获取该函数的参数组成的元祖
type Parameters<T extends (...args: any) => any> = T extends (
  ...args: infer P
) => any
  ? P
  : never

//ReturnType（返回类型）接收一个函数.返回的是该函数的返回值
type ReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any
```

---

```ts
/**并不是那么常用 */

//ConstructorParameters（构造参数）:接收一个构造函数,返回的时候该构造函数的参数
type ConstructorParameters<T extends abstract new (...args: any) => any> =
  T extends abstract new (...args: infer P) => any ? P : never

//InstanceType（构造返回类型、实例类型）获取传入构造函数的返回类型
type InstanceType<T extends abstract new (...args: any) => any> =
  T extends abstract new (...args: any) => infer R ? R : any

//Uppercase（大写）
type Uppercase<S extends string> = intrinsic

//Lowercase（小写）
type Lowercase<S extends string> = intrinsic

//Capitalize（首字母大写）
type Capitalize<S extends string> = intrinsic

//Uncapitalize（首字母小写）
type Uncapitalize<S extends string> = intrinsic
```

---

# 单个语法的应用,一些实用的场景

## extends

有时候我们定义的泛型不想过于灵活或者说想继承某些类等，可以通过 extends 关键字添加泛型约束。

```ts
interface Lengthwise {
  length: number
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length)
  return arg
}

loggingIdentity(3)

//Argument of type 'number' is not assignable to parameter of type 'ILengthwise'.

loggingIdentity({ length: 3 }) // ok
```

---

## infer

类型推断

```ts
//
type Obj<T> = T extends { a: infer U; b: infer U } ? U : number

const result: Obj<{ a: number; b: () => void }> // a | ()=>void

//推断函数的返回值类型

type T0 = string[]
type UnpackedArray<T> = T extends (infer U)[] ? U : T
type U0 = UnpackedArray<T0>
```

---

## Record

约束属性键名范围以及对应的值的类型,值是一样的类型

```ts
interface Staff {
  name: string
  salary: number
}

//属性名约束在Staff这个type里，value一定是string类型

type StaffJson = Record<keyof Staff, string>

const product: StaffJson = {
  name: 'John',
  salary: '3000',
}
```

---

## Parameters

提取函数的参数

```ts
export interface Student {
  name: string
  age: number
}

export interface StudentFunc {
  (name: string, age: number): Student
}

type student = Parameters<StudentFunc>
//type student = [name: string, age: number]

//封装api的参数

export interface GetUserInfoAPi {
  ({ userName: string, passWord: number }): {
    token: string
    detail: unknown
    result: unknown
  }
}

```

---

# Learn More

https://bigfrontend.dev/zh/typescript/

https://www.typescriptlang.org/zh/play

https://codesandbox.io/s/typescript-playground-export-forked-cmbsyq?file=/index.ts