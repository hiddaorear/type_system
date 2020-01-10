# Type assertion(类型断言)

## 意义

TS允许程序员覆盖TS类型的推断，然后以程序想要的方式使用，被称为“类型断言”。类型断言是一种很灵活的方式，灵活也同样意味着容易犯错。在有些场景下，类型声明会是更好的方案。

局限性：当`S`类型是`T`类型的子集的时候，或者`T`类型是`S`类型的子集时，`S`能被推断为`T`。这个规则表明，类型断言是一个做类型选择的办法，不能做类型转换。类型断言，可以提供额外的安全性，而无根据的断言会导致危险。后者建议用`any`。

绕开这个局限性的技巧，是使用双重断言，见下文。

## 语法

``` TypeScript

let foo: any;
let bar = <string>foo; // bar的类型从any变为string
// 或者用as
let bas = foo as string;

// 泛型的语法在JSX中有歧义，如：
let foo = <string>bar;</string>;

// 为了一致性，建议用as语法

```

## 常见用途

### 类型断言的害处

如果不需要收缩(narrow down)类型或者放大类型的范围，请使用类型声明，类型声明能做到更好的类型检查和代码提示。

不好的用法示例：

``` TypeScript

interface Foo {
    bar: number;
    bas: string;
}

const foo = {
    // 没有添加bar和bas属性，编译也不会报错
} as Foo;

// 或者

const foo1 = <Foo>{
    // 没有添加bar和bas属性，编译也不会报错
}

```

正确用法：

``` TypeScript

interface Foo {
    bar: number;
    bas: string;
}

const foo: Foo = {
    // 编译器会检查foo的属性，是否符合Foo，也会给出Foo的代码提示
}

```


### 从JS迁移到TS

``` javascript

const foo = {};
foo.bar = 123; // Error: 'bar' 属性不存在于'{}'
foo.bas = 'hello' // Error: 'bas' 属性不存在于'{}'
```

`foo`的类型推断为`{}`，是零属性对象。不能在上面加属性。可以通过类型断言来避免此问题：

``` TypeScript

interface Foo {
    bar: number;
    bas: string;
}

const foo = {} as Foo;
foo.bar = 123;
foo.bas = 'hello';

```

### 双重断言

使用者了解更具体的类型，使用类型断言

``` TypeScript

function handler(event: Event) {
    const mouseEvent = event as MouseEvent;
}

```

但有些断言会失败。断言的类型应和原来的类型同属一个具体类型的子类型，要么是原来类型的子集，或者超集。

``` TypeScript

function handler(event: Event) {
    const mouseEvent = event as HTMLElement; // Error: 'Event'和'HTMLElement'中任何一个不能赋值给另一个
}

```

解决办法，强行使用加入一个高于原类型和断言的类型的类型，通常是`any`。

``` TypeScript

function handler(event: Event) {
    const mouseEvent = (event as any) as HTMLElement;
}

```

### 和类型(联合类型)和类型断言

#### 没有共同的签名方法

``` TypeScript

const bar = (() => {}) as () => (Promise<number> | Promise<string>)

bar().then((data) => {}) // then会被报类型错误

```

原因：`Promise<number>`和`Promise<string>`没有共同的签名的`then`方法。

解决办法：

``` TypeScript

const bar = (() => {}) as () => (Promise<number | string>)

```
#### 没有共同的属性

报错的代码

``` TypeScript

const getLength = (something: string | number): number => {
        return something.length;
}

// Error:  Property 'length' does not exist on type 'string | number'. Property 'length' does not exist on type 'number'.

```

加上`length`属性判断，也不能修复报错。


``` TypeScript

const getLength = (something: string | number): number => {
    if(something.length){
        return something.length;
    } else {
        return something.toString().length;
    }
}

// Error:  Property 'length' does not exist on type 'string | number'. Property 'length' does not exist on type 'number'.

```

用类型断言修正

``` TypeScript

const getLength = (something: string | number): number => {
    if((something as string).length){
        return something.length;
    } else {
        return something.toString().length;
    }
}

```

这个地方使用类型断言，是否这好的办法呢？不一定。就这个例子而言，`(something as string)`可以使用`typeof something === string`。


更进一步，在有些情况，可以使用`is`。

``` TypeScript

const isString = (s: any): s is string => {
    return typeof s === 'string';
}

```

或者使用 discriminated union(模拟模式匹配)的技巧。当然，这个办法代码多了，约束也更多。

``` TypeScript

type s1 = {
    type: 'nunber'
    value: number
}

type s2 = {
    type: 'string'
    value: string
}

type s = s1 | s2

function getLength(s: s): number {

    switch (s.type) {
        case 'number':
            return something.toString().length;
        case 'string':
            return something.length;
        default:
            return 0;
    }
}

```


## 和类型转换的区别

类型推断是一种编译的语法，也是为编译器提供如何分析代码的方法。而类型转换通常是运行时的。

## 和声明的区别

特别是用临时类型声明，如`let a = {} : {name: string, id: number};`，这种情况下，与使用`as`的区别是什么？

## change log

- 2020/1/7 create document
