# dependent type

## 问题

函数前序的函数的实参，决定后续参数的类型。

下一个参数的类型，由上一个参数的实参类型确定。这其实是 Dependent Type。

## 普通的解答

有问题的解答：

``` TypeScript

type FooParams = {
    type: 'foo'
    value: string
}

type BarParams = {
    type: 'bar'
    value: number
}

type Params = FooParams | BarParams

function test<TParams extends Params> (
    type: TParams['type'],
    value: TParams['value']
): void {}

```

答案的问题：`test('foo', 123)`不会报错。

原因：TS把`TParams['type']`和`TParams['value']`，当成独立的类型。类型分析得到前者为` 'foo' | 'bar'`，后者为`string | number`。故没有报错。

``` TypeScript
function test2<TType extends Params['type']>(
    type: TType,
    value: Extract<Params, {type: TType}>['value']
): void {}
```

小技巧：使用 `Extract` 提取联合类型中的具体类型。

会决定其他参数类型的参数，要用泛型，在通过这个参数推导其他参数类型。


## 实现 Dependent Type

TS目前不支持 Dependent Type，但可以借助TS的函数重载和 condition type支持。如：

``` TypeScript

type F<T> = T extends {
  type: infer X,
  value: infer Y
} ? (type: X, value: Y) => void : never;
export type UnionToIntersection<U> = (U extends any
  ? (k: U) => void
  : never) extends (k: infer I) => void
  ? I
  : never;
type Fn = UnionToIntersection<F<string | number>>;

interface FooParams {
  type: 'foo';
  value: string;
}

interface BarParams {
  type: 'bar';
  value: number;
}

type Params = FooParams | BarParams;

let test: UnionToIntersection<F<FooParams | BarParams>> = (...args: any[]): any => {
  return undefined as any;
}

test('foo', '1')
test('foo', 1);
test('bar', 2)
test('bar','2')


```


## 思路

- 通过distributive conditional types和infer 将Type Variable进行拆分映射为不同的函数类型的union
- 通过 unionToIntersection 将函数union转换为函数intersection
- 函数 inersection 可以当做函数重载使用
- 函数重载时只对外暴露overload signature并不对外暴露implementation signature

## 疑问

0. distributive conditional types 具体是什么概念？

1. infer的使用？

2. inersection 是什么？

3. union是指什么？

4. signature是指什么？

## 资料

- [TypeScript 类型技巧 - 多参数类型约束](https://zhuanlan.zhihu.com/p/95828198)

- [Typescript Tips: 动态重载实现廉价版dependent type](https://zhuanlan.zhihu.com/p/95829351)

- [深入理解 TypeScript](https://jkchao.github.io/typescript-book-chinese/tips/infer.html#%E4%BB%8B%E7%BB%8D)

## change log

- 2019/12/29 create document
