# Type assertion(类型断言)

## 意义

TS允许程序员覆盖TS类型的推断，然后以程序想要的方式使用，被称为“类型断言”。类型断言是一种很灵活的方式，灵活也同样意味着容易犯错。在有些场景下，类型声明会是更好的方案。

局限性：当`S`类型是`T`类型的子集的时候，或者`T`类型是`S`类型的子集时，`S`能被推断为`T`。所以类型断言，可以提供额外的安全性，而无根据的断言会导致危险。后者建议用`any`。

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

如果不需要收缩类型或者放大类型的范围，请使用类型声明，类型声明能做到更好的类型检查和代码提示。

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

### 和类型和类型断言

``` TypeScript

const bar = (() => {}) as () => (Promise<number> | Promise<string>)

bar().then((data) => {}) // then会被报类型错误

```

原因：`Promise<number>`和`Promise<string>`没有共同的签名的`then`方法。

解决办法：

``` TypeScript

const bar = (() => {}) as () => (Promise<number | string>)

```

## 和类型转换的区别

类型推断是一种编译的语法，也是为编译器提供如何分析代码的方法。而类型转换通常是运行时的。

## change log

- 2020/1/7 create document
